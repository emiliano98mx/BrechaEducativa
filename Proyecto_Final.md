# PROYECTO FINAL SIG

El backup de la base de datos así como las consultas realizadas se pueden encontrar en los siguientes links:
Backup: [link](https://drive.google.com/file/d/1w_Ox36Pyg6RBx_DcA3C53d5yVtuP6By8/view?usp=drive_link)
Consultas: [link](https://drive.google.com/file/d/16NMO18MGuAoxG5UXnQzLH4f140nzvKe9/view?usp=drive_link)

A pesar de que las consultas se encuentran en en el link previamente adjunto, a continuación se mostrará las consultas realizadas para su limpieza, esto con el fin de poner a prueba los conocimientos aprendidos en el curso de SIG.

# PREPARANDO BASE DE DATOS
Debido a la naturaleza del proyecto, se utilizaran datos de escuelas, alumnos, situación socioeconómica, entre otros. Por ende se crearon esquemas para organizar los datos de acuerdo a su categoría.
``` sql
create database proyecto_sig;

create schema escuelas;
create schema alumnos;
create schema pobreza;
```
Todos los datos mencionados anteriormente se tienen en formato .csv, a esto se les suma los datos de municipios y estados de la República Mexicana en formato shapefile.
Los datos fueron importados a la base de datos mediante QGIS, los datos .csv no tenían ningún tipo de geometría asignada, pero sí el nombre de la entidad o municipio a la que pertenecen.
En primer lugar se genera una nueva columna para almacenar la geometría a las tablas.
``` sql
alter table alumnos."DBALUMNOS"
add column geom (geometry,4326);
```
Debido a que los datos de los alumnos solamente contienen los datos por entidad (nombre de entidad), se realiza la siguiente consulta para asignarle la misma geometría que la capa shapefile de entidades.
``` sql
UPDATE alumnos."DBALUMNOS" AS a
SET geom = e.geom
FROM public."Entidades" AS e
WHERE a.estado = e.ent_nomg;
```
Este proceso logró asignar la geometría de estado a las filas de los datos de alumnos, este mismo proceso se realizaron con las demás tablas.
Como ya se mencionó, se utilizó el nombre de las entidades o la clave de las entidades para realizar la unión de las tablas con sus respectivas geometrías de sus entidades; sin embargo, el formato de algunas claves se encontraba en formato 01 y otras en formato 1, es decir, no era posible realizar la unión entre estos dos formatos, es por ello que se realizó la siguiente consulta para corregir esta limitante, tanto para entidades como municipios.
``` sql
UPDATE bd_accesibilidad_carreteras_2020
SET d_accesibilidad_carreteras_2020 = 
    CASE 
        WHEN LENGTH(entidad_cve) = 1 THEN LPAD(entidad_cve, 3, '0')
        WHEN LENGTH(entidad_cve) = 2 THEN LPAD(entidad_cve, 3, '0')
        ELSE entidad_cve
    END
WHERE LENGTH(entidad_cve) BETWEEN 1 AND 2;
```
## DOCENTES/DIRECTORES
Los datos de evaluación de los directores y profesores se encontraban de manera individual, es decir que las filas indicaban el resultado de evaluación de cada docente o director, y al estado al que pertenecen, por ello, se optó por realizar la siguiente consulta para poder agrupar el promedio de evaluación de los docentes por estado en una tabla, y el promedio de evaluación de los directores en otra tabla.
``` sql
SELECT 
    e.ent_nomg AS estado,
    COUNT(a.*) AS total_registros,
    COUNT(a.p_global) FILTER (WHERE a.p_global > 0) AS evaluaciones_validas_global,
    COUNT(a.suma_puntaje) FILTER (WHERE a.suma_puntaje > 0) AS evaluaciones_validas_suma,
    AVG(NULLIF(a.p_global, 0)) AS promedio_estatal_global,
    AVG(NULLIF(a.suma_puntaje, 0)) AS promedio_estatal_suma,
    e.geom
FROM alumnos."Eval_Director&Docente_1" AS a
JOIN "Entidades" AS e ON a.entidad = e.ent_nomg
WHERE a.plaza !~* '(DIRECCIÓN|DIRECTOR)'
GROUP BY e.ent_nomg, e.geom;
```
## ESCUELAS
Los datos escolares indican el porcentaje de cobertura por estado y nivel educativo que se tiene de diferentes servicios, como lo son el acceso a computadoras, internet, electricidad, agua potable y sanitarios. Para un posterior análisis estadístico más eficiente, se optó por realizar un promedio de la cobertura de estos servicios de educación básica a nivel nacional, agrupándolo por estado y obteniendo el promedio de los tres niveles educativos evaluados:
``` sql
SELECT estado, 
       ROUND(AVG("%_computadoras"), 2) AS computadoras,
       ROUND(AVG("%_internet"), 2) AS internet,
       ROUND(AVG("%_electricidad"), 2) AS electricidad,
       ROUND(AVG("%_aguapotable"), 2) AS aguapotable,
       ROUND(AVG("%_sanitarios"), 2) AS sanitarios
FROM (
    SELECT estado, "%_computadoras", "%_internet", "%_electricidad", "%_aguapotable", "%_sanitarios" 
    FROM escuelas.infraestructura_prescolar
    UNION ALL
    SELECT estado, "%_computadoras", "%_internet", "%_electricidad", "%_aguapotable", "%_sanitarios" 
    FROM escuelas.infraestructura_primaria
    UNION ALL
    SELECT estado, "%_computadoras", "%_internet", "%_electricidad", "%_aguapotable", "%_sanitarios" 
    FROM escuelas.infraestructura_secundaria
) AS infraestructura_general
GROUP BY estado
ORDER BY estado;
```
