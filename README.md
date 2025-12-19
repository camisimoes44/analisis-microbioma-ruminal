# Pipeline de microbioma ruminal por amplicón  


Pipeline en **R** para el procesamiento y análisis de datos de microbioma ruminal a partir de lecturas pareadas (**FASTQ**) de secuenciación por amplicón.

El pipeline infiere **ASVs (Amplicon Sequence Variants)** utilizando el modelo estadístico de **DADA2**, asigna taxonomía mediante el clasificador implementado en DADA2 empleando un *training set* derivado de **SILVA**, y estructura los datos en objetos **phyloseq** y **TreeSummarizedExperiment** para realizar análisis exploratorios y estadísticos.

> Este repositorio contiene únicamente el **código del pipeline**.  
> **No se incluyen datos de ejemplo** (ni archivos FASTQ ni metadatos).

## ¿Qué hace este pipeline?

1. Procesa lecturas crudas FASTQ pareadas de secuenciación por amplicón.
2. Corrige errores de secuenciación e infiere **ASVs** con DADA2.
3. Elimina quimeras y construye una tabla de conteos de ASVs por muestra.
4. Asigna taxonomía a las ASVs usando un *training set* basado en SILVA.
5. Integra los datos en estructuras estándar (`phyloseq`, `TreeSummarizedExperiment`).
6. Realiza análisis exploratorios y estadísticos del microbioma, incluyendo:
   - diversidad alfa (riqueza observada, Shannon, Simpson, Faith)
   - comparaciones no paramétricas entre grupos (tests de Wilcoxon)
   - visualización de resultados mediante boxplots con significancia estadística
8. Compara métodos de muestreo ruminal (**Sonda vs Fístula**), incluyendo:
   - análisis de diversidad beta (PCoA, NMDS, MDS)
   - PERMANOVA y dbRDA
   - correlaciones pareadas entre muestras
   - análisis de abundancia diferencial pareado (DESeq2)

---

## Requisitos

### Software

- R (versión compatible con DADA2)
- Paquetes principales utilizados:
  - dada2
  - Biostrings
  - phyloseq
  - mia / miaViz
  - scater
  - vegan
  - DESeq2
  - ampvis2
  - ggplot2
  - dplyr / tidyr / tibble
  - patchwork / ggpubr / ggsignif
  - reshape2 / RColorBrewer
  - readxl
  - TreeSummarizedExperiment
 
### Base de datos de referencia para taxonomía

La asignación taxonómica se realiza con el clasificador de DADA2 utilizando un
*training set* derivado de **SILVA**. Por ejemplo:

- `silva_nr99_v138.1_wSpecies_train_set.fa.gz`

El usuario debe descargar la versión deseada y asegurarse de indicar la ruta
correcta en el script.

---


## Instalación y uso rápido

### 1. Descargar el repositorio

```bash
git clone https://github.com/camisimoes44/analisis-microbioma-rumial
cd analisis-microbioma-rumial
```

Alternativamente, se puede descargar el repositorio como archivo `.zip` desde la
interfaz web de GitHub.

---


## Preparación de los archivos de entrada

### 1. Archivos FASTQ (input primario)

El pipeline espera **lecturas pareadas de amplicón** en formato FASTQ comprimido (`.fastq.gz`), con la siguiente nomenclatura:

```text
SAMPLENAME_1.fastq.gz   # lecturas forward
SAMPLENAME_2.fastq.gz   # lecturas reverse
```

- El identificador de muestra (`SAMPLENAME`) se infiere automáticamente a partir del nombre del archivo forward.
- Es importante que cada muestra tenga **un par forward/reverse**.

Los FASTQ deben estar ubicados en el directorio de trabajo definido en el script:

```r
path <- "."

```
El parámetro `path` define el directorio donde se encuentran los archivos FASTQ.
El valor `"."` indica el directorio de trabajo actual.

Si el proyecto se encuentra en otra carpeta, el usuario debe cambiar
explícitamente el directorio de trabajo antes de ejecutar el script, por ejemplo:

```r
setwd("~/proyectos/rumen/")
```

### 2. Archivo de metadatos de muestras

Para las etapas de análisis exploratorio y estadístico, el pipeline requiere un archivo tabular de metadatos, por ejemplo:

```text
datos_rumen.txt
```

Requisitos mínimos del archivo:

- formato tabular (TSV)
- encabezado
- una columna llamada `sample`
- los valores de `sample` deben coincidir **exactamente** con los identificadores
  de muestra inferidos a partir de los FASTQ

Las columnas adicionales (animal, momento de muestreo, tratamiento, dispositivo
de muestreo, etc.) se incorporan automáticamente al objeto `phyloseq` y se
utilizan en los análisis posteriores.

---

### 3. Chequeos previos recomendados

Antes de ejecutar el pipeline, se recomienda verificar que:

- todos los archivos FASTQ tengan su par forward/reverse
- los nombres de muestra sean consistentes entre FASTQ y metadatos
- el directorio de trabajo (`path`) esté correctamente definido

Errores en cualquiera de estos puntos pueden provocar fallos en la ejecución
o inconsistencias en los análisis.

---

## Cómo ejecutar el pipeline

Desde una sesión de R, una vez preparados los archivos:

```r
source("scriptRumenComentado.txt")
```

El script se ejecuta de forma secuencial y genera archivos intermedios y finales
en el directorio de trabajo activo (y en subdirectorios creados durante la
ejecución).

Se recomienda ejecutar el pipeline en una **sesión limpia de R**.


---

## Principales archivos de salida

Durante la ejecución, el pipeline genera una serie de archivos intermedios y
finales que documentan cada etapa del análisis.

Entre los principales outputs se incluyen:

- `qualities.pdf`  
  Perfiles de calidad de las lecturas forward y reverse (primeras muestras).

- `plotErrors.pdf`  
  Visualización de los modelos de error aprendidos por DADA2.

- `track_rumen.txt`  
  Tabla de seguimiento de lecturas a través de las distintas etapas del pipeline
  (input, filtrado, denoising, merge, no quiméricas).

- `ps.rumen`  
  Objeto `phyloseq` serializado que contiene la tabla de conteos, taxonomía,
  metadatos y secuencias de referencia.

- `conteos_ASVs.txt`  
  Matriz de conteos de ASVs por muestra.

- `abundanciaRel_ASVs.txt`  
  Matriz de abundancias relativas de ASVs.

- `correlaciones_pares_generos.tsv`  
  Correlaciones pareadas entre muestras obtenidas por sonda y fístula.

- `correlaciones_resumen_generos.tsv`  
  Resumen estadístico de las correlaciones por grupo experimental.

- `de_analysis.txt`  
  Resultados del análisis de abundancia diferencial (DESeq2) para comparaciones
  pareadas entre métodos de muestreo.

---

## Notas importantes

- El pipeline asume un diseño experimental pareado **Sonda (S)** vs **Fístula (F)**.
- Algunas secciones del análisis asumen nombres de muestra con estructura
  `cow_date_sampleType`.
- El script combina etapas de procesamiento (DADA2) y análisis estadístico en un
  único archivo.
- El script carga múltiples paquetes y crea objetos en el entorno global
  (por ejemplo `ps`, `tse`, `dds`) que se reutilizan a lo largo del análisis.
- Algunas etapas cambian el directorio de trabajo mediante `setwd()`;
  se recomienda revisar y adaptar estas líneas según la organización local.
- Para evitar conflictos, se recomienda ejecutar el pipeline en una sesión limpia
  de R.

---


