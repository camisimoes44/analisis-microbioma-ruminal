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
6. Realiza análisis exploratorios y estadísticos del microbioma.
7. Compara métodos de muestreo ruminal (**Sonda vs Fístula**), incluyendo:
   - diversidad alfa y beta
   - ordenamientos multivariados
   - PERMANOVA / dbRDA
   - correlaciones pareadas
   - abundancia diferencial pareada con DESeq2

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

## Preparación de los archivos de entrada

### 1. Archivos FASTQ (input primario)

El pipeline espera **lecturas pareadas de amplicón** en formato FASTQ comprimido (`.fastq.gz`), con la siguiente nomenclatura:

SAMPLENAME_1.fastq.gz # lecturas forward
SAMPLENAME_2.fastq.gz # lecturas reverse


- El identificador de muestra (`SAMPLENAME`) se infiere automáticamente a partir del nombre del archivo forward.
- Es importante que cada muestra tenga **un par forward/reverse**.

Los FASTQ deben estar ubicados en el directorio de trabajo definido en el script:

```r
path <- "."



