
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

The folder will look similar to the image below, and the name of the folder will contian information on the date of the run and the flow cell used:

<img width="683" alt="Screenshot 2024-12-17 at 16 18 37" src="https://github.com/user-attachments/assets/bb7334a0-7883-481f-a96b-58c677765db1" />


>Do not change the name of this folder, it will help us to trace back results to each Nanopore run.



## ii) Within this folder, create a folder for the new basecalling output 

```
mkdir dorado_sup_basecall
```

>[!NOTE]
>mkdir = make a new directory

## iii) Migrate into yor new directory

```
cd dorado_sup_basecall
```
>[!NOTE]
>cd = change directory

## ii) Use dorado to start basecalling

>[!CAUTION]
>Basecalling is a slow process that takes a lot of memory, this may not run if other large process are running in the server at the same time

```
dorado basecaller \
--min-qscore 10 \
--kit-name SQK-NBD114-96 \
sup ../pod5 > Filename_output.bam
```
>[!NOTE]
>1. change the kit-name to the relevant kit name, is it NBD or RBK??
>2. The above code uses the 'sup' basecalling alogrithm, this is the most accurate, but slowest

This creates a combined file in BAM format, that contains all of your sequence data reads in one file, each read has a header line with information, where the barcode within the reads is listed.

We can use this file to split all of the data into one file per Nanopore barcode to split our isolates into their separate pools.

This process is known as **demultiplexing**

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


>[!NOTE]
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

### i) 
