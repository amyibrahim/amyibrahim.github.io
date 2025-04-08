
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

### i) Migrate to the folder where the data from MinKNOW has been transferred to

The folder will look similar to the image below:

<img width="683" alt="Screenshot 2024-12-17 at 16 18 37" src="https://github.com/user-attachments/assets/bb7334a0-7883-481f-a96b-58c677765db1" />

>[!NOTE]
>Do not change the name of this folder, it will help us to trace back results to each Nanopore run.

<br>
- Within your sequence run folder, **create a folder** for the new basecalling output that you are about to generate

```
mkdir dorado_sup_basecall
```

*mkdir = make a new directory*
<br>

- Migrate into your new directory

```
cd dorado_sup_basecall
```
*cd = change directory*

- Once you are in the folder, you can use a command to **use dorado to start basecalling your bases**

---
**NOTE**

*The block of code below will needed to be editted to contain the whole filepath for dorado, for the correct library prep kit, and to edit the name of the output file for your run specifically*

---
<br>

```
dorado basecaller \
--min-qscore 10 \
--kit-name SQK-NBD114-96 \
sup ../pod5 > Filename_output.bam
```

This creates a combined file in BAM format, that contains all of your sequence data reads in one file, each read has a header line with information, where the barcode within the reads is listed.

We can use this file to split all of the data into one file per Nanopore barcode to split our isolates into their separate pools.

This process is known as **demultiplexing**

## 3) Demultiplexing reads

```
dorado demux --output-dir ./classified_demux --no-classify ./
```

Within this folder, you will see files for barcodes that you did not use, do not worry, these files are likely empty or rubbish and can be ignored

You will want to create a separate folder containing only the barcodes that you have used, to ease all downstream analysis.

## 4) Subsetting for barcodes that were used

First create a folder for the barcodes that you have used 

```
mkdir barcodes_used
```

Create a list within this folder of the names of the barcodes that you have used (barcodes_used.txt)

Move your files into the barcodes used folder

```
cat ./barcodes_used/barcodes_used.txt | parallel -j 1 "mv ./PATH/TO/FILE/{}.bam ./barcodes_used"

```

## 5) Map your reads (bam files previously created) to a reference genome 
