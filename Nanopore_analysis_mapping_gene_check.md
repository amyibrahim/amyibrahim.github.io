
# Using Nanopore sequene data to check specific genes 

This page will guide you through the steps to check the sequence of a parasite line

## The basic steps involve:

  **1. Basecalling your sequence data using Dorado**

  **2. Demultiplexing your reads into FASTQ sequence files for each barcode**

  **3. Mapping individual FASTQ files to your reference genome**

  **4. Searching individual genes for coverage / SNPs**

***

## 1. Basecalling using Dorado

Dorado should already be installed in your server account - **please ask me to have a look if you have any issues running Dorado**

### i) Migrate to the folder containing data from MinKNOW

The folder will look similar to the image below, and the name of the folder will contian information on the date of the run and the flow cell used.

*- Do not change the name of this folder, it will help us to trace back results to each Nanopore run.*

<img width="683" alt="Screenshot 2024-12-17 at 16 18 37" src="https://github.com/user-attachments/assets/bb7334a0-7883-481f-a96b-58c677765db1" />

### ii) Within this folder, create a folder for the new basecalling output 

```
mkdir dorado_sup_basecall
```
*mkdir = make a new directory*

### iii) Migrate into yor new directory

```
cd dorado_sup_basecall
```
*cd = change directory*

### iv) Use dorado to start basecalling

>**Note:**
> Basecalling is a slow process that takes a lot of memory, this may not run if other large process are running in the server at the same time

```
dorado basecaller \
--min-qscore 10 \
--kit-name SQK-NBD114-96 \
sup ../pod5 > Filename_output.bam
```
>**Note:**
>1. change the kit-name to the relevant kit name, is it NBD or RBK??
>2. The above code uses the 'sup' basecalling alogrithm, this is the most accurate, but slowest

This creates a **combined file in BAM format**, that contains all of your sequence data reads in one file, each read has a header line with information, where the barcode within the reads is listed.

We can use this file to split all of the data into one file per Nanopore barcode to split our isolates into their separate pools.

This process is known as **DEMULTIPLEXING**

***

## 3) Demultiplexing reads

### i) Use dorado demux to demultiplex

```
dorado demux --output-dir ./classified_demux --no-classify ./
```

Within this folder **(classified_demux)**, you will see individual bam files for each barcode.

Like below:

![Screenshot from 2025-04-08 16-11-08](https://github.com/user-attachments/assets/1607a104-d1f0-4811-9f12-57a11017c161)

You will see barcodes that you did not use, do not worry, these files are likely empty or rubbish and can be ignored

### ii) format your demultiplexing output

First create a folder for the barcodes that you have used 

```
mkdir barcodes_used
```

**> Create a list within this folder of the names of the barcodes that you have used (barcodes_used.txt)**

An example of this file is:

![Screenshot from 2025-04-08 16-14-40](https://github.com/user-attachments/assets/9d46a429-1949-4a4f-9269-637a32a094bb)


>We will use **vim** a text editor to create this file

```
vim barcodes_used.txt
```

This will open a blank text document, press 'i' to enter your text

> List each barcode used in the **correct format** (matching the end of your filenames) on a new line -  **do not leave whitespace**

**To exit vim:**

**1. Press ESc**

**2. Type ':wq!:'**


**3. Press ENTER**



**> Move your files into the barcodes used folder**

```
cat ./barcodes_used/barcodes_used.txt | parallel -j 1 "mv ./PATH/TO/FILE/{}.bam ./barcodes_used"

```

***

## 5) Map your reads (bam files previously created) to a reference genome 

I have copied references genomes into your server account, within the folder, 'genomes', see below:

![Screenshot from 2025-04-09 11-04-34](https://github.com/user-attachments/assets/97d7a462-705f-4fe7-a895-832a36bf752e)


### i) Create a mapping directory 

You are currently in the 'dorado_sup' directory, which contained your barcode-sorted basecalled reads

It is a good idea to create a separate directory for the mapped reads

**a) move up a directory**

```
cd ../
```

**b) create a new directory for mapped reads**

```
mkdir mapping
```

**c) migrate to the mapping directory**

```
cd mapping
```

### ii) Use Minimap2 to align your individual reads to the reference genome

[Minimap2](https://github.com/lh3/minimap2) is a sequence aligner that is recommended for aligning long reads created by Oxford Nanopore sequencing technologies.

Here is [the reference](https://academic.oup.com/bioinformatics/article/34/18/3094/4994778) for Minimap2, an here is a [tutorial page](https://docs.tinybio.cloud/docs/minimap2-tutorial).

>Minimap2 should already be installed in your server account, please let me know if there are issues running Minimap2

```
minimap2 -ax map-ont ~/PATH/TO/GENOME/REFERENCE.fa ~/PATH/TO/BARCODED/BAMs/classified_demux/XXX_barcode01.bam > barcode01_aligned.sam       
```
>You will have to do this individually for each barcode file, and remember to change the names of the input and output files accordingly

### iii) format your individual aligned files

Alignment files are outputted as SAM files, these files are large - it is best to convert these to BAM files and then **DELETE YOUR SAM FILES**

All of the following steps with utilise [samtools](https://rnnh.github.io/bioinfo-notebook/docs/samtools.html), a useful ackage for working with SAM and BAM files after sequencing.


**a) convert SAM file to BAM file**
```
samtools view -Sb -o barcode01_aligned.bam barcode01_aligned.sam
```
>-Sb indicates that the input is a SAM file and the output is a BAM file
>Change the name of each file accordingly depending on what sample you are working on

**b) sort your BAM file**

Sorting reorders the reads in your alignment based on their position when aligned to the reference genome

```
samtools sort -O bam -o barcode01_aligned.sorted.bam barcode01_aligned.bam
```

>-O bam specifies the output as a BAM file
>-o specified the name of the output file

**c) index your sorted BAM file**

Indexing creates a 'contents page' of the aligned file, which is needed by many downstream applications, including tablet

The index file created will be called FILENAME.bam.bai

```
samtools index barcode01_aligned.sorted.bam 
```

>Check for generation of the indexed file using 'ls' to list all files in the current directory

***












