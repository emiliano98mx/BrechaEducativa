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

Se utilizó como insumos:
- una tabla en formato csv de indicadores estatales, generada a partir del preprocesamiento realizado en SQL "bd_ent_socioeconomico_2020.csv".
- Un diccionario de variables, que describe cada indicador junto con su categoría o "criterio" " diccionario_bd.csv".
  
La documentación de la base de datos se elaboró siguiendo la metodología descrita en el siguiente enlace: [Metodología - Brecha Educativa](https://github.com/emiliano98mx/BrechaEducativa/blob/main/BD_SQL.md).


```{r Carga bases de datos}
dibas = "D:/Centro_geo/C02_SIG/proyecto_final/"

# Cargar la base de datos principal
bd <- read_csv(paste0(dibas,"c01_exploracion_datos/analisis_r/bd_ent_socioeconomico_2020.csv"),show_col_types = FALSE)

# Cargar el diccionario de variables
dic_bd <- read_csv(paste0(dibas,"c01_exploracion_datos/analisis_r/diccionario_bd.csv"),show_col_types = FALSE)
```
Se visualizaron los datos cargados 

### Indicadores estatales
```{r Visualizar cuadro de variables}

kable(head(bd, 10), format = "html", table.attr = "style='width:100%;'") %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed"), full_width = F) %>%
  scroll_box(width = "100%", height = "400px")
```
### Diccionario de variables
```{r Visualizar diccionarios}


kable(head(dic_bd, 10), format = "html", table.attr = "style='width:100%;'") %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed"), full_width = F) %>%
  scroll_box(width = "100%", height = "400px")
```

## Objetivo 1:
El objetivo de esta sección es identificar las variables que se asocian con el rezago educativo a nivel estatal. Para ello, se define como variable principal re_ptot, que representa la población con rezago educativo. Esta variable fue seleccionada mediante el método stepwise, al ser la que mostró mayor capacidad explicativa del fenómeno entre un conjunto amplio de indicadores.

### Correlaciones por criterio
A partir de esta variable principal, se calculo la matriz de correlación entre re_ptot y el resto de las variables, agrupadas según los criterios definidos en el diccionario de dato: 

- Alimentación

- Calidad y espacios en la vivienda

- Cohesión social

- Grado de accesibilidad

- Ingreso per cápita

- Pobreza

- Seguridad social

- Servicios básicos en vivienda

- Servicios de salud

- Infraestructura educativa

- Nivel educativo

Se obtiene un correlograma por cada grupo para identificar cuál variable tiene mayor relación con re_ptot.
