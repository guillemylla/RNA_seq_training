# Introduction to RNA-seq data analysis 

- Guillem Ylla
- Training for the Extavour Lab
- September 2021

## Raw data quality control

```
fastqc YOUR_SAMPLE.fastq
```

- Examples of the FasqtQC file at [data/Example_FastQC](data/Example_FastQC)

## Read trimming

The main issues that the RNA-seq data usually have are:

- Reads containing adapters and PCR primers that might difficult the mapping (much worse for de-novo transcriptome assemblies)
- Bad quality at the end of the reads

There are lots of programs to clean reads. 

### My recommendations for trimming:

- For single-end reads

```
cutadapt -a ADAPTER_SEQ -o output.fastq YOUR_SAMPLE.fastq
```

- For paired-end data

```
 java -jar trimmomatic-0.32.jar PE \
	-threads 8  \
	YOUR_SAMPLE_R1.fastq YOUR_SAMPLE_R1.fastq \
	YOUR_SAMPLE_R1_clean.fastq YOUR_SAMPLE_R1_Unpaired.fastq YOUR_SAMPLE_R2_clean.fastq YOUR_SAMPLE_R2_Unpaired.fastq \
	ILLUMINACLIP:"adapters/TruSeq3-PE-2.fa":2:30:10:8:TRUE SLIDINGWINDOW:4:15
```

## Clean reads quality control

```
fastqc YOUR_SAMPLE_clean.fastq
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

Again, many options. **My highlights:**

- FeatureCounts (R & Python package)
- RSEM (allows to quanitfy isoforms! base don reads that map in exon-exon junctions)


## Normalization


The number of counts mapping to a given gene depends on multiple factors. Obvious ones are the length of the genes and the total number of reads obtained from each samples. Depending what we want to do next is important to consider how we want to normalize to make these numbers comparable. For example, if we want to compare the expression of one gene in multiple samples, the length of the gene is less important than the total number of of reads of each library. However, if we want to compare expression of genes within each library, normalize by length would be very important.


- RPKM/FPKM and TPM are widely used (often incorrectly) ways to normalize accounting for both, read length and library size. 


### My recommendation for Mapping + Counting + Table of counts

Mapping, quantifying and normalizing as TPM & FPKM can be done all in **RSEM**. I usually run RSEM specifying STAR as a read mapper.


1. Index Genome

```
rsem-prepare-reference --num-threads 6 --star --star-path STAR-2.7.6a/bin/Linux\_x86\_64/ --gtf dmel-all-r6.36_rsemfriendly.gtf dmel\-all\-chromosome-r6.36.fasta Dmel6.36RSEM
```


2. Run RSEM

```

indexname="Dmel6.36RSEM"
outputpath="rsem_output_v2"
fastqfile="Path_to_fastq_file"

rsem-calculate-expression --star \
          --star-path software/STAR-2.7.6a/bin/Linux\_x86\_64/ \
          --phred64-quals \
          --keep-intermediate-files \
            --num-threads $SLURM_NTASKS \
            --star-gzipped-read-file \
            --star-output-genome-bam \
               $fastqfile \
               $indexname \
               $outputpath/$fname

```

3. Get table of counts


```
rsem-generate-data-matrix rsem_output/*.genes.results  > Genes.COUNTS.txt
```

4. Get quantification in TPMs and FPKMs

- Small hack to get table of counts as TPMs or FPKMs in RSEM

  - Find the RSEM script  rsem-generate-data-matrix
  - Copy it & find the line that contains "$offsite" 
  - Set  $offsite=5 for TPMs, $offsite = 6 FPKMs

```
rsem-generate-data-matrix_FPKMs rsem_output/*.genes.results  > Genes.FPKMs.txt
rsem-generate-data-matrix_TPMs rsem_output/*.genes.results  > Genes.TPMs.txt

```

## Data analysis

From this point on we will use R to perform the analysis. 

- Rmd file with the source code: [RNA_Seq_data_analysis_training.Rmd](RNA_Seq_data_analysis_training.Rmd)
- Compiled html file with code and figures: [RNA_Seq_data_analysis_training.htm](https://htmlpreview.github.io/?https://raw.githubusercontent.com/guillemylla/RNA_seq_training/main/RNA_Seq_data_analysis_training.html)











