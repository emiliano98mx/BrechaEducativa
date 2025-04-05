# Análisis de correlación

**Autor:** Adrián Israel Silva Cardoza  
**Fecha:** 2025-03-27

Este análisis tiene como objetivo identificar las variables socioeconómicas y educativas que influyen en el rezago educativo en México. Se utiliza una estrategia basada en matrices de correlación y modelos de regresión para seleccionar las variables más significativas.

---

## 📦 Bibliotecas utilizadas

- `readr`, `readxl`: Para la carga de datos.
- `knitr`, `kableExtra`: Para formateo de tablas.
- `MASS`: Para modelado estadístico.
- `ggcorrplot`: Para visualización de correlaciones.
- `pacman`: Para facilitar la carga de paquetes.

---

## 📁 Cargar los datos para 

Se trabaja con dos archivos:

- Una base principal de indicadores estatales.
- Un diccionario que describe cada variable y su categoría o "criterio".

La ruta de acceso definida es:

```r
dibas = "D:/Centro_geo/C02_SIG/proyecto_final/"


