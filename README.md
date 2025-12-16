# Pipeline de microbioma ruminal por amplicón  


Pipeline en **R** para el procesamiento y análisis de datos de microbioma ruminal a partir de lecturas pareadas (**FASTQ**) de secuenciación por amplicón.

El pipeline infiere **ASVs** utilizando el modelo estadístico de **DADA2**, asigna taxonomía mediante el clasificador implementado en DADA2 empleando un *training set* derivado de **SILVA**, y estructura los datos en objetos **phyloseq** y **TreeSummarizedExperiment** para realizar análisis exploratorios y estadísticos.

> Este repositorio contiene únicamente el **código del pipeline**.  
> **No se incluyen datos de ejemplo** (ni archivos FASTQ ni metadatos).
