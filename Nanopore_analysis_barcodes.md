
This page will take you through the basic steps of analysis involved:

**>Base calling**

**>Coverage analysis**

**> Extracting the number of reads mapping to specific internal barcodes**

![Screenshot 2024-12-16 at 17 58 09](https://github.com/user-attachments/assets/261030e8-6b56-40a6-959f-c978ee97aa23)


## 1) Set up your environment for sequence analysis

Migrate to whereever you are working with your Nanopore data (I might have to show you how to do this if you are using the server)

When in your workstation, you will have to download the pre-preapred conda environment that I have previously created

All of the information 
```
ssh username@10.18.0.26
```

When successfully logged in, activate your conda environment.

Your conda environment was previously set up by myself, and is an area where you can work where I have installed all of the programmes that you will need for these analysis steps.

The programmes installed that you will need for this conda environment are all listed in THIS FILE which you can extract and use to install this environment if you need.

```
conda activate moon_code
```

Now you have all of your packages and data that you need for the following analyses.

## 2) Basecall your sequence data using dorado

Currently we have to log in to Dan's account to use dorado, I will need to install this on our own accounts so that we can locally access all of this in one place.

Migrate to the folder where the fastqs are outputted

*insert image of folder loation here*

Create a folder for new basecalling output (mkdir) and then migrate into it (cd)

```
mkdir dorado_sup_basecall

cd dorado_sup_basecall
```

Once you are in the folder, you can use a command to use dorado to start basecalling your bases 

```
KIT_NAME="SQK-NBD114-96"

dorado basecaller \
--min-qscore 10 \
--kit-name $KIT_NAME \
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
 











