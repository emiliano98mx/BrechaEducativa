# Análisis de correlación

**Autor:** Adrián Israel Silva Cardoza  
          
**Fecha:** 2025-03-27

Este análisis tiene como objetivo identificar las variables socioeconómicas y educativas que influyen en el rezago educativo en México. Se utiliza una estrategia basada en matrices de correlación y modelos de regresión para seleccionar las variables más significativas.

---

##  Bibliotecas utilizadas

- `readr`, `readxl`: Para la carga de datos.
- `knitr`, `kableExtra`: Para formateo de tablas.
- `MASS`: Para modelado estadístico.
- `ggcorrplot`: Para visualización de correlaciones.
- `pacman`: Para facilitar la carga de paquetes.

---

##  Cargar los datos 

Como insumos se utilizo:
- una tabla en formato csv de indicadores estatales, generada a partir del preprocesamiento realizado en SQL.
- Un diccionario de variables, que describe cada indicador junto con su categoría o "criterio".
  
La documentación de la base de datos se elaboró siguiendo la metodología descrita en el siguiente enlace: [Metodología - Brecha Educativa](https://github.com/emiliano98mx/BrechaEducativa/blob/main/Proyecto_Final.md).
.




