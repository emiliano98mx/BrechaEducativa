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
A partir de esta variable se calculo la matriz de correlación y las variables agrupadas por los siguientes criterios del diccionario:

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



```{r matriz de rezago educativo}
# Filtrar las variables del diccionario por criterio
var_rezg <- dic_bd$variable[dic_bd$criterio %in% c("rezago educativo")]

# Crear una base de datos filtrada con estas variables
bd_rezg <- bd[, var_rezg]

# Calcular la matriz de correlación
mc_rezg <- cor(bd_rezg, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_rezg, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Rezago Educativo",
           colors = c("blue", "white", "red"))
```
Una vez seleccionada esta variable, se corre la matriz para cada criterio y se selecciona dentro de cada criterio la variable con mayor correlación a re_ptot.
### Alimentación 
```{r matriz de correlacion alimentación vs rezago}
# Filtrar las variables por criterio
variables_alimentacion <- dic_bd$variable[dic_bd$criterio == "alimentacion"]

# Crear una base de datos filtrada con las variables de alimentación y re_ptot
bd_filtro <- bd[, c(variables_alimentacion, "re_ptot")]

# Calcular la matriz de correlación
matriz_correlacion <- cor(bd_filtro, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(matriz_correlacion, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Alimentación vs re_ptot",
           colors = c("blue", "white", "red"))

```
### Calidad y espacios
```{r matriz Calidad y espacios}
# Filtrar las variables
var_espacios <- dic_bd$variable[dic_bd$criterio == "calidad y espacios vivienda"]

# Crear una base de datos filtrada con las variables de alimentación y re_ptot
bd_cev <- bd[, c(var_espacios, "re_ptot")]

# Calcular la matriz de correlación
mc_cev <- cor(bd_cev, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_cev, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Calidad y vivienda y Rezago Educativo",
           colors = c("blue", "white", "red"))
```
