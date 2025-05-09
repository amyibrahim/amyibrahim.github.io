
As you perform your oxford nanopore sequencing run, the sequence data is being read as electrical squiggles, and this is converted to DNA bases in real time, in a process known as basecalling.

Basecalling uses an algorithmn to convert electrical signals into nucleotide sequences, read more about basecalling [here](https://nanoporetech.com/platform/technology/basecalling)

The software used by the sequencing computer, **MinKNOW**, has an inbuilt basecalling algorithmn, which is good for analysing your data rapidly.

However, we have specific questions, and want to use a more accurate basecaller, **Dorado** to basecall our raw data which we can then analyse.

***

**This worksheet will guide you through the steps in using Dorado to basecall your sequence data, please follow the links in this page for your further analysis steps**

***



## BASECALLING - DORADO

***

### 1. Familiarise yourself with the working environment

First you will need to familiarise yourself with your data output and the organisation of your files, have a look in the server in your run output folder (e.g. AINP001) using 'ls' to list the contents of directories.

Your run output folder should look something like this:

![Screenshot from 2025-04-29 13-38-47](https://github.com/user-attachments/assets/45b484f6-de02-4c43-b40d-52af269d1b80)

The basecalled reads, which MinKNOW generated using a speedy (less acurrate) basecaller are within the **fastq_pass** and **fastq_fail** folders (standard QC in basecalling has organised them into passed and failed reads based on their quality).

The non-basecalled, raw electrical signals are saved in pod5 files and can be found in the folder **pod5**


***

### 2. Basecalling using Dorado

Migrate to the folder containing data from MinKNOW - the folder will look similar to the image below, and the name of the folder will contain information on the date of the run and the flow cell used.

*- Do not change the name of this folder, it will help us to trace back results to each Nanopore run.*

<img width="683" alt="Screenshot 2024-12-17 at 16 18 37" src="https://github.com/user-attachments/assets/bb7334a0-7883-481f-a96b-58c677765db1" />

Within this folder, **create a folder for the new basecalling output** 

```
mkdir dorado_sup_basecall
```
*mkdir = make a new directory*

Migrate into yor new directory

```
cd dorado_sup_basecall
```
*cd = change directory*

**Use dorado to start basecalling**

>**Note:**
> Basecalling is a slow process that takes a lot of memory, this may not run if other large process are running in the server at the same time

```
dorado basecaller \
--min-qscore 10 \
--kit-name SQK-NBD114-96 \
sup ../pod5 > Filename_output.bam
```
>**Note:**
>1. The kit-name in your sample sheet will need to match the exact kit index that dorado is looking for, from the following list:
> EXP-NBD103 EXP-NBD104 EXP-NBD114 EXP-NBD114-24 EXP-NBD196 EXP-PBC001 EXP-PBC096 SQK-16S024 SQK-16S114-24 SQK-LWB001 SQK-MLK111-96-XL SQK-MLK114-96-XL SQK-NBD111-24 SQK-NBD111-96 SQK-NBD114-24 SQK-NBD114-96 SQK-PBK004 SQK-PCB109 SQK-PCB110 SQK-PCB111-24 SQK-PCB114-24 SQK-RAB201 SQK-RAB204 SQK-RBK001 SQK-RBK004 SQK-RBK110-96 SQK-RBK111-24 SQK-RBK111-96 SQK-RBK114-24 SQK-RBK114-96 SQK-RLB001 SQK-RPB004 SQK-RPB114-24 TWIST-16-UDI TWIST-96A-UDI VSK-PTC001 VSK-VMK001 VSK-VMK004 VSK-VPS001

>2. The above code uses the 'sup' basecalling alogrithm, this is the most accurate, but slowest

This creates a **combined file in BAM format**, that contains all of your sequence data reads in one file, each read has a header line with information, where the barcode within the reads is listed.

We can use this file to split all of the data into one file per Nanopore barcode to split our isolates into their separate pools.

This process is known as **DEMULTIPLEXING**

***

### 3) Demultiplexing reads using Dorado

Demultiplexing splits your sequence data (individual reads) into seperate files so that each isolate (with its own unique nanopore barcode) has all of the data in one file/folder

```
dorado demux --output-dir ./classified_demux --no-classify ./
```

Within this folder **(classified_demux)**, you will see individual bam files for each barcode.

Like below:

![Screenshot from 2025-04-08 16-11-08](https://github.com/user-attachments/assets/1607a104-d1f0-4811-9f12-57a11017c161)

You will see barcodes that you did not use, do not worry, these files are likely empty or rubbish and can be ignored


***

### 4) Map your reads (bam files previously created) to a reference genome 

I have copied references genomes into your server account, within the folder, 'genomes', see below:

![Screenshot from 2025-04-09 11-04-34](https://github.com/user-attachments/assets/97d7a462-705f-4fe7-a895-832a36bf752e)

 >Note you may have one or many of these files in your genome folder, all that you need for mapping is the GENOMENAME.fasta file


You are currently in the 'dorado_sup' directory, which contained your barcode-sorted basecalled reads

It is a good idea to **create a separate directory for the mapped reads**

move up a directory

```
cd ../
```

create a new directory for mapped reads

```
mkdir mapping
```

migrate to the mapping directory

```
cd mapping
```

**Use Minimap2 to align your individual reads to the reference genome**

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
## 6) Checking mapping statistics / QC

### Check mapping statistics using samtools bamstats

```
samtools flagstat FILENAME.bam
```

This will output the number of reads and the percentage of reads mapping to the reference genome for a basic measure of how much P. knowlesi data you have

### Genome coverage plots - to do after Easter!


***

## 7) Visualising your genome in Tablet

1. Install tablet

Once tablet is installed, you will need to copy uour BAM file (filename.bam) and indexed BAM file (FILENAME.bam.bai) over into your PC where tablet is installed


First copy over the bam file... 

```
scp USERNAME@10.18.0.25:FOLDER/FILE/PATH/TO/BAMFILE/FILENAME.bam ./
```

Then, copy over the index file:

```
scp USERNAME@10.18.0.25:FOLDER/FILE/PATH/TO/BAMFILE/FILENAME.bam.bai ./
```

Now you can open Tablet in your local PC, and upload the relevant BAM file and your reference genome - the reference genome is already downloaded into your PC!



***


***

## 8) Calling variants and looking for SNPs / INDELs

- TO DO!!!

***

## 8) Looking for large scale structural variants

***











