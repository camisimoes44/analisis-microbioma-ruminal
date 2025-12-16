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
