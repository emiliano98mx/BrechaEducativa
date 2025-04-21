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

## Objetivo 1:

-	Determinar las variables socioeconomicas y educativas a nivel estatal que influyen en la población analfabeta en Mexico.

Aqui se genera un correlagrama por CRITERIO versus la variable de población analfabeta.


### Correlaciones por criterio
A partir de esta variable, se calculó la matriz de correlación con el resto de las variables, agrupadas según los criterios definidos en el diccionario de datos. Para cada grupo, se identificó la variable con mayor correlación respecto a la población analfabeta (re_pana)

- Alimentación

- Calidad y espacios en la vivienda

- Cohesión social

- Educación

- Grado de accesibilidad

- Infraestructura educativa

- Ingreso per cápita

- Seguridad social

- Servicios básicos en vivienda

- Servicios de salud

- Pobreza


Una vez seleccionada esta variable, se corre la matriz para cada criterio y se selecciona dentro de cada criterio la variable con mayor correlación a re_ptot.

### Alimentación 
```{r Correlograma de alimentación vs rezago}
# Filtrar las variables por criterio
variables_alimentacion <- dic_bd$variable[dic_bd$criterio == "alimentacion"]

# Crear una base de datos filtrada con las variables de alimentacion y re_ptot
bd_filtro <- bd[, c(variables_alimentacion, "re_pana")]

# Calcular la matriz de correlacion
matriz_correlacion <- cor(bd_filtro, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(matriz_correlacion, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Población analfabeta vs alimentación",
           colors = c("blue", "white", "red"))
```

### Calidad y espacios en la vivienda

```{r Correlograma Calidad y vivienda}
# Filtrar las variables
var_espacios <- dic_bd$variable[dic_bd$criterio == "calidad y espacios vivienda"]

# Crear una base de datos filtrada con las variables de alimentacion y re_ptot
bd_cev <- bd[, c(var_espacios, "re_pana")]

# Calcular la matriz de correlacion
mc_cev <- cor(bd_cev, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_cev, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Población analfabeta vs Calidad y vivienda ",
           colors = c("blue", "white", "red"))
```

### Cohesion social

```{r Correlograma Cohesion social}
# Filtrar las variables
var_cs <- dic_bd$variable[dic_bd$criterio == "cohesion social"]

# Crear una base de datos filtrada con estas variables
bd_cs <- bd[, c(var_cs, "re_pana")]

# Calcular la matriz de correlacion
mc_cs <- cor(bd_cs, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_cs, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Población analfabeta vs Cohesion social",
           colors = c("blue", "white", "red"))
```

### Educación

```{r Correlograma educación}
# Filtrar las variables
var_edu <- dic_bd$variable[dic_bd$criterio == "educativo"]

# Crear una base de datos filtrada con estas variables
bd_edu <- bd[, c(var_edu, "re_pana")]

# Calcular la matriz de correlacion
mc_edu <- cor(bd_edu, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_edu, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Población analfabeta vs Eduacion",
           colors = c("blue", "white", "red"))
```

### Grado de accesibilidad 

```{r Correlograma grado de accesibilidad carreteras}
# Filtrar las variables
var_gdcap <- dic_bd$variable[dic_bd$criterio == "grado accesibilidad"]

# Crear una base de datos filtrada con estas variables
bd_gd <- bd[, c(var_gdcap, "re_pana")]

# Calcular la matriz de correlacion
mc_gd <- cor(bd_gd, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_gd, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Población analfabeta vs Grado accesibilidad",
           colors = c("blue", "white", "red"))
```

### Infraestructura educativa

```{r Correlograma infraestructura educativa}
# Filtrar las variables
var_infra <- dic_bd$variable[dic_bd$criterio == "infraestructura educativa"]

# Crear una base de datos filtrada con estas variables
bd_infra <- bd[, c(var_infra, "re_pana")]

# Calcular la matriz de correlacion
mc_infra <- cor(bd_infra, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_infra, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Población analfabeta vs Infraestructura educativa",
           colors = c("blue", "white", "red"))
```

### Ingreso económico

```{r Correlograma ingreso economico}
# Filtrar las variables
var_inper <- dic_bd$variable[dic_bd$criterio == "ingreso per capita"]

# Crear una base de datos filtrada con estas variables
bd_inper <- bd[, c(var_inper, "re_pana")]

# Calcular la matriz de correlacion
mc_inper <- cor(bd_inper, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_inper, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Población analfabeta vs Ingreso per capita",
           colors = c("blue", "white", "red"))
```

### Seguridad social 

```{r Correlograma seguridad social}
# Filtrar las variables
var_seg <- dic_bd$variable[dic_bd$criterio == "seguridad social"]

# Crear una base de datos filtrada con estas variables
bd_seg <- bd[, c(var_seg, "re_pana")]

# Calcular la matriz de correlacion
mc_seg <- cor(bd_seg, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_seg, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Población analfabeta vs Seguridad social",
           colors = c("blue", "white", "red"))
```

### Servicios basicos vivienda 

```{r Correlograma servicios basicos vivienda}
# Filtrar las variables
var_sv <- dic_bd$variable[dic_bd$criterio == "servicios basicos vivienda"]

# Crear una base de datos filtrada con estas variables
bd_sv <- bd[, c(var_sv, "re_pana")]

# Calcular la matriz de correlacion
mc_sv <- cor(bd_sv, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_sv, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Población analfabeta vs Servicios basicos vivienda",
           colors = c("blue", "white", "red"))
```

### Servicios salud

```{r Correlograma servicios salud}
# Filtrar las variables
var_sal <- dic_bd$variable[dic_bd$criterio == "servicios de salud"]

# Crear una base de datos filtrada con estas variables
bd_sal <- bd[, c(var_sal, "re_pana")]

# Calcular la matriz de correlacion
mc_sal <- cor(bd_sal, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_sal, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Población analfabeta vs Servicios salud",
           colors = c("blue", "white", "red"))
```
### Pobreza

```{r Correlograma pobreza}
# Filtrar las variables
var_pbrz <- dic_bd$variable[dic_bd$criterio == "pobreza"]

# Crear una base de datos filtrada con estas variables
bd_pbrz <- bd[, c(var_pbrz, "re_pana")]

# Calcular la matriz de correlacion
mc_pbrz <- cor(bd_pbrz, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(mc_pbrz, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Población analfabeta vs Pobreza",
           colors = c("blue", "white", "red"))
```


### Variables independientes por criterio con mayor nivel de correlación

Una vez calculadas las correlaciones, se selecciona una variable por criterio (la más correlacionada con re_pana) para construir un conjunto reducido de predictores. Las variables inicialmente seleccionadas son las siguientes:

- `re_ptot`: Población con rezago educativo  
- `al_caal`: Carencias promedio en por acceso a la alimentación
- `cv_ccvi`: Calidad y condiciones de la vivienda (índice compuesto)  
- `cs_ring`: Índice de cohesión social (estimado)
- `ed_phind`: Porcentaje de hablantes de alguna lengua indígena
- `ga_porb`: Grado de accesibilidad a carreteras pavimentadas muy bajo y bajo
- `ie_nesc`: Infraestructura educativa: número de escuelas 
- `ip_pism`: Porcentaje de población ocupada con ingresos menores a 2 salarios mínimos 
- `pz_pobr`: Porcentaje de población en pobreza multidimensional
- `se_cvcs`: Carencias promedio de población en vulnerabilidad por carencia social 2020  
- `sb_pvdr`: Porcentaje de viviendas que no disponen de refrigerador  
- `ss_pasa`: Porcentaje de población carencia por acceso a los servicios de salud

### Correlograma de las variables seleccionadas para el mododelo inicial

```{r Correlograma de variables seleccionadas para el modelo}
# Seleccionar las variables especificas
variables_correlacion <- c("re_pana",  "al_caal", "cv_ccvi", "cs_ring", 
                           "ed_phind", "ga_porb", "ie_nesc", "ip_pism",
                           "se_cvcs",  "sb_pvdr", "ss_pasa", "pz_pobr" 
                            )

# Filtrar las variables de interes en la base de datos
bd_filtro <- bd[, variables_correlacion]

# Calcular la matriz de correlacion
matriz_correlacion <- cor(bd_filtro, use = "pairwise.complete.obs")

# Generar el correlograma
ggcorrplot(matriz_correlacion, 
           type = "lower", 
           lab = TRUE, 
           title = "Correlograma: Población analfabeta vs variables socioeconomicas",
           colors = c("red", "white", "blue"))

```

## Objetivo 2

Una vez definidas las variables del apartado anterior para probar el modelo de regresión lineal, con el objetivo de identificar las variables predictoras más significativas que contribuyen la cantidad de población con analfabetismo (re_pana). Esto se utiliza el método stepwise que garantiza una selección óptima de variables, considerando criterios estadísticos que definen la probabilidad de obtener los resultados observados si estas no tuvieran ningún efecto en el modelo (i.e. Criterio de Información de Akaike y el valor de p). Este proceso permitió reducir el modelo inicial que incluía todas las variables consideradas relevantes en el análisis preliminar, optimizando el ajuste estadístico mediante la inclusión o exclusión de variables. 

### Modelo predictivo de población analfabeta

Con base del conjunto de variables seleccionadas en el paso anterior, se construyó un modelo de regresión lineal múltiple. Para optimizar el modelo, se aplicó nuevamente una técnica de selección stepwise, que permitió conservar únicamente aquellas variables que resultaron estadísticamente significativas para explicar el rezago educativo.

```{r Modelo para rezago}
# Preparar el modelo inicial (modelo nulo) y completo
modelo_nulo <- lm(re_pana  ~ 1, data = bd) 

# Ajustar el modelo completo de regresian lineal
modelo_completo <- lm(re_pana ~ 
                        al_caal + cv_ccvi +  cs_ring + ga_porb + ip_pism +
                        pz_pobr + se_cvcs + sb_pvdr +  ss_pasa +
                        ie_nesc + ed_phind, data = bd)

# Mostrar un resumen del modelo
summary(modelo_completo)

# Aplicar la seleccion stepwise
modelo_stepwise <- step(modelo_nulo, 
                        scope = list(lower = modelo_nulo, upper = modelo_completo), 
                        direction = "both")  # "both" para hacia adelante y hacia atras

# Resumen del modelo stepwise
summary(modelo_stepwise)

# Variables seleccionadas
modelo_stepwise$coefficients

# Extraer los nombres de las variables seleccionadas en el modelo stepwise
variables_seleccionadas <- names(coef(modelo_stepwise))[-1]  # Excluye el intercepto

# Filtrar las descripciones correspondientes en dic_bd
descripciones <- dic_bd[dic_bd$variable %in% variables_seleccionadas, c("variable", "descripcion")]

# Mostrar la tabla en un formato mas legible
kable(descripciones, format = "html", table.attr = "style='width:100%;'") %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed"), full_width = F) %>%
  scroll_box(width = "100%", height = "300px")

# Generar predicciones del modelo
predicciones <- predict(modelo_stepwise)

# Crear el grafico: valores reales vs predicciones
plot(bd$re_ptot, predicciones, 
     main = "Prediccion vs Valores Observados", 
     xlab = "Valores Observados (re_ptot)", 
     ylab = "Valores Predichos", 
     col = "blue", 
     pch = 20)
abline(0, 1, col = "red")  # Linea de referencia

# Filtrar la base de datos para incluir solo las variables seleccionadas
var_sel <- bd[, variables_seleccionadas]

# Calcular la matriz de correlacion
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

### Resultado del modelo final
```
> summary(modelo_stepwise)

Call:
lm(formula = re_pana ~ ie_nesc + cs_ring + se_cvcs + sb_pvdr + 
    ss_pasa, data = bd)

Residuals:
   Min     1Q Median     3Q    Max 
-48087 -13295  -2828  16690  50917 

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept) -2.710e+05  8.124e+04  -3.336  0.00257 ** 
ie_nesc      1.891e+01  1.097e+00  17.242 9.49e-16 ***
cs_ring      1.710e+03  9.203e+02   1.858  0.07456 .  
se_cvcs      1.587e+05  5.703e+04   2.784  0.00989 ** 
sb_pvdr      2.084e+03  1.132e+03   1.842  0.07692 .  
ss_pasa     -2.582e+03  1.482e+03  -1.743  0.09321 .  
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 25570 on 26 degrees of freedom
Multiple R-squared:  0.973,	Adjusted R-squared:  0.9678 
F-statistic: 187.6 on 5 and 26 DF,  p-value: < 2.2e-16
```

## Objetivo 3
Para la identificación de los estados con mayor brecha educativa y socioeconómica.De acuerdo con las variables definidas en el modelo de regresión lineal. Se utilizaron las consultas en SQL representadas en los mapas.


