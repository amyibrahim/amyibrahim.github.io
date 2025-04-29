
As you perform your oxford nanopore sequencing run, the sequence data is being read as electrical squiggles, and this is converted to DNA bases in real time, in a process known as basecalling.

Basecalling uses an algorithmn to convert electrical signals into nucleotide sequences, read more about basecalling [here](https://nanoporetech.com/platform/technology/basecalling)

The software used by the sequencing computer, **MinKNOW**, has an inbuilt basecalling algorithmn, which is good for analysing your data rapidly.

However, we have specific questions, and want to use a more accurate basecaller, **Dorado** to basecall our raw data which we can then analyse.

***

### This worksheet will guide you through the steps in using Dorado to basecall your sequence data, please follow the links in this page for your further analysis steps.

***

## BASECALLING - DORADO

***

### 1. Familiarise yourself with the working environment

First you will need to familiarise yourself with your data output and the organisation of your files, have a look in the server in your run output folder (e.g. AINP001) using 'ls' to list the contents of directories.

Your run output folder should look something like this:

![Screenshot from 2025-04-29 13-38-47](https://github.com/user-attachments/assets/45b484f6-de02-4c43-b40d-52af269d1b80)

The basecalled reads, which MinKNOW generated using a speedy (less acurrate) basecaller are within the **fastq_pass** and **fastq_fail** folders (standard QC in basecalling has organised them inot passed and failed reads based on their quality).

The unbasecalled, raw electrical signals are saved in pod5 files and can be found in the folder **pod5**


***

### 2. Set up the files that you need for the basecaller

Dorado will require certain information to complete the basecalling programme, we can provide this in a **sample sheet**

There is a specific format for the sample sheet, the information can be found [here](https://github.com/nanoporetech/dorado/blob/release-v0.9/documentation/SampleSheets.md), and an example is in the image below:

![Screenshot from 2025-04-29 14-41-56](https://github.com/user-attachments/assets/fd390bd4-6232-46cf-9b10-f6cdea8778a9)

You can create the sample sheet above in excel and copy it over to your account in the server when you have finished.

When creating the file in excel, please use the **exact header names shown above** and save the file as a **.csv** file.

To copy the file over into your server account use:

```
scp FILENAME.csv USERNAME@10.18.0.25:nanopore_data/MY_RUN/
```


  **1. Basecalling your sequence data using Dorado**

  **2. Demultiplexing your reads into FASTQ sequence files for each barcode - Dorado**

  **3. Mapping individual FASTQ files to your reference genome using Minimap2 (built in to Dorado)**

  **4. Searching individual genes for coverage / SNPs**

***
## Set up before processing samples

Before using Dorado to basecall and map your sequence data, you will need to create a sample sheet which you pass in to dorado, which contains all of the important information about your run.

An example sample sheet looks like the one in the image below:

<img width="963" alt="Screenshot 2025-04-28 at 12 14 55" src="https://github.com/user-attachments/assets/8846c6c8-c790-4fbf-b738-153e08fc2d39" />

Create this sheet locally on your PC, specific for your run, and this can be transferred to your working folder in the server. This file should be saved in **CSV format**

```
scp sample_sheet.csv username@10.18.0.25:where/your/data/is/
```
^ you will be prompted for your password to log in and allow this file transfer.

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
--sample-sheet sample_sheet.csv \
sup ../pod5 > Filename_output.bam
```
>**Note:**
>1. The kit-name in your sample sheet will need to match the exact kit index that dorado is looking for, from the following list:
> EXP-NBD103 EXP-NBD104 EXP-NBD114 EXP-NBD114-24 EXP-NBD196 EXP-PBC001 EXP-PBC096 SQK-16S024 SQK-16S114-24 SQK-LWB001 SQK-MLK111-96-XL SQK-MLK114-96-XL SQK-NBD111-24 SQK-NBD111-96 SQK-NBD114-24 SQK-NBD114-96 SQK-PBK004 SQK-PCB109 SQK-PCB110 SQK-PCB111-24 SQK-PCB114-24 SQK-RAB201 SQK-RAB204 SQK-RBK001 SQK-RBK004 SQK-RBK110-96 SQK-RBK111-24 SQK-RBK111-96 SQK-RBK114-24 SQK-RBK114-96 SQK-RLB001 SQK-RPB004 SQK-RPB114-24 TWIST-16-UDI TWIST-96A-UDI VSK-PTC001 VSK-VMK001 VSK-VMK004 VSK-VPS001

>3. The above code uses the 'sup' basecalling alogrithm, this is the most accurate, but slowest

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

Then, copy over the inde file:

```
scp USERNAME@10.18.0.25:FOLDER/FILE/PATH/TO/BAMFILE/FILENAME.bam.bai ./
```

Now you can open Tablet in your local PC, and upload the relevant BAM file and your reference genome - the reference genome is already downloaded into your PC!



***


***
Below - For Amy to edit after easter!!




***

## 7) Calling variants and looking for SNPs / INDELs

- check the best variant caller for nanopore

***

## 8) Looking for large scale structural variants

***











