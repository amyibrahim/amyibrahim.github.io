# Custom internal barcode detection from nanopore amplicon sequencing 

This page will take you through the basic steps of analysis involved:

**>Basecalling**

**>Coverage analysis**

**> Extracting the number of reads mapping to specific internal barcodes**




*Insert figure for analysis pipeline here*


## 1) Set up your environment for sequence analysis

Firstly, migrate to your terminal, and log in to your account on the server, you will be prompted for your password (ask me if you do not know)

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













