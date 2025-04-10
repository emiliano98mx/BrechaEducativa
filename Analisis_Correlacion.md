# Análisis de correlación

        
**Fecha:** 2025-03-27

Este análisis tiene como objetivo identificar las variables socioeconómicas y educativas que influyen en el rezago educativo en México. Se utiliza una estrategia basada en matrices de correlación y modelos de regresión para seleccionar las variables más significativas. El análisis fue realizado utilizando el software RStudio.



---

##  Bibliotecas utilizadas

- `readr`, `readxl`: Para la carga de datos.
- `knitr`, `kableExtra`: Para formateo de tablas.
- `MASS`: Para modelado estadístico.
- `ggcorrplot`: Para visualización de correlaciones.
- `pacman`: Para facilitar la carga de paquetes.

---

##  Insumos

- Tabla en formato csv de indicadores estatales, generada a partir del preprocesamiento realizado en SQL "bd_ent_socioeconomico_2020.csv".
- Diccionario de variables, que describe cada indicador junto con su categoría o "criterio" " diccionario_bd.csv".
  
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

## Variable dependiente principal:
Se definió como variable dependiente principal re_ptot, que representa la población con rezago educativo. Esta fue seleccionada mediante el método stepwise, al ser la que mostró la mayor capacidad explicativa dentro de las variables catalogadas como rezago educativo 


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

### Correlaciones por criterio
A partir de esta variable, se calculó la matriz de correlación con el resto de las variables, agrupadas según los criterios definidos en el diccionario de datos. Para cada grupo, se identificó la variable con mayor correlación respecto a re_ptot

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
### Cohesion social

```{r Matriz corr cohesion social, eval =FALSE}
# Filtrar las variables
var_cs <- dic_bd$variable[dic_bd$criterio == "cohesion social"]

# Crear una base de datos filtrada con estas variables
bd_cs <- bd[, c(var_cs, "re_ptot")]

# Calcular la matriz de correlación
mc_cs <- cor(bd_cs, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_cs, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Cohesión social y Rezago Educativo",
           colors = c("blue", "white", "red"))
```
### Grado de accesibilidad y resago educativo 

```{r Matriz corr GDCAP}
# Filtrar las variables
var_gdcap <- dic_bd$variable[dic_bd$criterio == "grado accesibilidad"]

# Crear una base de datos filtrada con estas variables
bd_gd <- bd[, c(var_gdcap, "re_ptot")]

# Calcular la matriz de correlación
mc_gd <- cor(bd_gd, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_gd, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Grado accesibilidad y Rezago Educativo",
           colors = c("blue", "white", "red"))
```
### Ingreso percapita
```{r Matriz ingreso percapita}
# Filtrar las variables
var_inper <- dic_bd$variable[dic_bd$criterio == "ingreso per capita"]

# Crear una base de datos filtrada con estas variables
bd_inper <- bd[, c(var_inper, "re_ptot")]

# Calcular la matriz de correlación
mc_inper <- cor(bd_inper, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_inper, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Ingreso y Rezago Educativo",
           colors = c("blue", "white", "red"))
```
### Pobreza y rezago educativo 
```{r Matriz pobreza}
# Filtrar las variables
var_pbrz <- dic_bd$variable[dic_bd$criterio == "pobreza"]

# Crear una base de datos filtrada con estas variables
bd_pbrz <- bd[, c(var_pbrz, "re_ptot")]

# Calcular la matriz de correlación
mc_pbrz <- cor(bd_pbrz, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_pbrz, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Pobreza y Rezago Educativo",
           colors = c("blue", "white", "red"))
```
### Seguridad social y rezago educativo
```{r Matriz seguridad social}
# Filtrar las variables
var_seg <- dic_bd$variable[dic_bd$criterio == "seguridad social"]

# Crear una base de datos filtrada con estas variables
bd_seg <- bd[, c(var_seg, "re_ptot")]

# Calcular la matriz de correlación
mc_seg <- cor(bd_seg, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_seg, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Seguridad social y Rezago Educativo",
           colors = c("blue", "white", "red"))
```
### Servicios basicos vivienda y rezago educativo

```{r Matriz servicios vivienda}
# Filtrar las variables
var_sv <- dic_bd$variable[dic_bd$criterio == "servicios basicos vivienda"]

# Crear una base de datos filtrada con estas variables
bd_sv <- bd[, c(var_sv, "re_ptot")]

# Calcular la matriz de correlación
mc_sv <- cor(bd_sv, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_sv, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Servicios basicos vivienda y Rezago Educativo",
           colors = c("blue", "white", "red"))
```
### Servicios salud y rezago educativo
```{r Matriz servicios salud}
# Filtrar las variables
var_sal <- dic_bd$variable[dic_bd$criterio == "servicios de salud"]

# Crear una base de datos filtrada con estas variables
bd_sal <- bd[, c(var_sal, "re_ptot")]

# Calcular la matriz de correlación
mc_sal <- cor(bd_sal, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_sal, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Servicios salud y Rezago Educativo",
           colors = c("blue", "white", "red"))
```
### Infraestructura y rezago educativo
```{r Matriz infraestructura educativa}
# Filtrar las variables
var_infra <- dic_bd$variable[dic_bd$criterio == "infraestructura educativa"]

# Crear una base de datos filtrada con estas variables
bd_infra <- bd[, c(var_infra, "re_ptot")]

# Calcular la matriz de correlación
mc_infra <- cor(bd_infra, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_infra, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Infraestructura y Rezago Educativo",
           colors = c("blue", "white", "red"))
```

```{r Matriz educativa, eval =FALSE}
# Filtrar las variables
var_edu <- dic_bd$variable[dic_bd$criterio == "educativo"]

# Crear una base de datos filtrada con estas variables
bd_edu <- bd[, c(var_edu, "re_ptot")]

# Calcular la matriz de correlación
mc_edu <- cor(bd_edu, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_edu, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Eduación y Rezago Educativo",
           colors = c("blue", "white", "red"))
```


```{r Matriz de variables seleccionadas para el modelo}
# Seleccionar las variables específicas
variables_correlacion <- c("re_ptot", "al_paal", "cv_ccvi", "cs_ring", 
                           "ga_porb", "ip_pibe", "pz_pobr", 
                           "se_pvcs", "sb_pvdd", "ss_pasa", "ie_nesc", "ed_phind")

# Filtrar las variables de interés en la base de datos
bd_filtro <- bd[, variables_correlacion]

# Calcular la matriz de correlación
matriz_correlacion <- cor(bd_filtro, use = "pairwise.complete.obs")
print(round(matriz_correlacion,2))

# Generar el correlograma
ggcorrplot(matriz_correlacion, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma variables socioeconomicas vs rezago educativo",
           colors = c("red", "white", "blue"))

```
Una vez calculadas las correlaciones, se selecciona una variable por criterio (la más correlacionada con re_ptot) para construir un conjunto reducido de predictores. Las variables seleccionadas son las siguientes:

- `re_ptot`: Población con rezago educativo  
- `al_paal`: Población con acceso a la alimentación (estimado)  
- `cv_ccvi`: Calidad y condiciones de la vivienda (índice compuesto)  
- `cs_ring`: Índice de cohesión social (estimado)  
- `ga_porb`: Grado de accesibilidad a recursos básicos  
- `ip_pibe`: Ingreso per cápita estimado o PIB estatal  
- `pz_pobr`: Porcentaje de población en pobreza  
- `se_pvcs`: Seguridad social: población vinculada a cobertura social  
- `sb_pvdd`: Servicios básicos en vivienda: disponibilidad de drenaje  
- `ss_pasa`: Servicios de salud: acceso asegurado  
- `ie_nesc`: Infraestructura educativa: número de escuelas 
- `ed_phind`: Índice de educación promedio o escolaridad promedio (estimado)  

## Modelo predictivo del rezago educativo

Con base del conjunto de variables seleccionadas en el paso anterior, se construyó un modelo de regresión lineal múltiple. Para optimizar el modelo, se aplicó nuevamente una técnica de selección stepwise, que permitió conservar únicamente aquellas variables que resultaron estadísticamente significativas para explicar el rezago educativo.

```{r Modelo para rezago, eval =FALSE}
# Preparar el modelo inicial (modelo nulo) y completo
modelo_nulo <- lm(re_ptot  ~ 1, data = bd) 

# Ajustar el modelo completo de regresión lineal
modelo_completo <- lm(re_ptot ~ 
                        al_paal + cv_ccvi + cs_ring + 
                        ga_porb + ip_pibe + pz_pobr + 
                        se_pvcs + sb_pvdd + ss_pasa + 
                        ie_nesc + ed_phind, data = bd)

# Mostrar un resumen del modelo
summary(modelo_completo)

# Aplicar la selección stepwise
modelo_stepwise <- step(modelo_nulo, 
                        scope = list(lower = modelo_nulo, upper = modelo_completo), 
                        direction = "both")  # "both" para hacia adelante y hacia atrás

# Resumen del modelo stepwise
summary(modelo_stepwise)

# Variables seleccionadas
modelo_stepwise$coefficients

# Extraer los nombres de las variables seleccionadas en el modelo stepwise
variables_seleccionadas <- names(coef(modelo_stepwise))[-1]  # Excluye el intercepto

# Filtrar las descripciones correspondientes en dic_bd
descripciones <- dic_bd[dic_bd$variable %in% variables_seleccionadas, c("variable", "descripcion")]

# Mostrar la tabla en un formato más legible
kable(descripciones, format = "html", table.attr = "style='width:100%;'") %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed"), full_width = F) %>%
  scroll_box(width = "100%", height = "300px")

# Generar predicciones del modelo
predicciones <- predict(modelo_stepwise)

# Crear el gráfico: valores reales vs predicciones
plot(bd$re_ptot, predicciones, 
     main = "Predicción vs Valores Observados", 
     xlab = "Valores Observados (re_ptot)", 
     ylab = "Valores Predichos", 
     col = "blue", 
     pch = 20)
abline(0, 1, col = "red")  # Línea de referencia

# Filtrar la base de datos para incluir solo las variables seleccionadas
var_sel <- bd[, variables_seleccionadas]

# Calcular la matriz de correlación
mc_rez <- round(cor(var_sel, use = "pairwise.complete.obs"),2)

# Generar el correlograma
ggcorrplot(mc_rez, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma variables signficativas vs rezago educativo",
           colors = c("red", "white", "blue"))

# Guardar la matriz en un archivo para futuras consultas
#write.csv(mc_rez, paste0(dibas, "rezago_matriz_correlacion.csv"))
```


