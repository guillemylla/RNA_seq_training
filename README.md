# Introduction to RNA-seq data analysis 

- Guillem Ylla
- Training for the Extavour Lab
- September 2021

## Raw data quality control

```
fastqc 
```

## Read trimming

The main issues that the RNA-seq data usually have are:
- Reads containing adapters and PCR primers that might difficult the mapping (much worse for de-novo transcriptome assemblies)
- Bad quality at the end of the reads

There are lots of programs to clean reads. 

My recommendations:

- For single-end reads

```
cutadapt
```

- For paire-end data

```
trimmomatic
```

## Clean reads quality control

```
fastqc
```


## Read mapping

Many options!

- Pseudo-aligners (very fast!)
  - Salmon
  - Kallisto
- Aligners
  - Splice aware:
    - STAR
  - Splice unaware
    - Bowtie
    - Bowtie2
    - bwa
    - ....
    
## Table of counts

Now we want to know ho many reads mapped within each annotated gene.

Again, many options. My highlights:

- FeatureCounts (R & Python package)
- RSEM (allows to quanitfy isoforms! base don reads that map in exon-exon junctions)


## Normalization


The number of counts mapping to a given gene depends on multiple factors. Obvious ones are the length of the genes and the total number of reads obtained from each samples. Depending what we want to do next is important to consider how we want to normalize to make these numbers comparable. For example, if we want to compare the expression of one gene in multiple samples, the length of the gene is less important than the total number of of reads of each library. However, if we want to compare expression of genes within each library, normalize by length would be very important.


- RPKMs and TPM are widely used (often incorrectly) ways to normalize accounting for both, read length and library size. 


### My recommendation

- For mapping, quantifying and normalizing as TPM & FPKM can be done all in RSEM

```
RSEM.....
```

## Data analysis

From this point on we will use R to perform the analysis. 

- Rmd file with the source code
- Compiled html file with code and figures


https://htmlpreview.github.io/?https://raw.githubusercontent.com/guillemylla/Ovariole_morphogenesis_RNAseq/main/1-Whole_Ovary_libraries.html 










