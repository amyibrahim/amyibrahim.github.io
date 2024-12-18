title:null 

# Analysis of Nanopore data for custom internal barcode counting

Overview of the process for combining pools of barcoded lines and analysis:
<br>

![Screenshot 2024-12-16 at 17 58 09](https://github.com/user-attachments/assets/261030e8-6b56-40a6-959f-c978ee97aa23)
<br>
This page will take you through the basic steps of analysis involved:

**>Base calling**

**>Mapping sequencing reads to a reference genome**

**>Coverage analysis**

**>Extracting the number of reads mapping to specific internal barcodes**

**>Creating plots in R**

<br>

---

## 1) Set up your environment for sequence analysis

- Migrate to a folder containing your Nanopore data *(I might have to show you how to do this if you are using the server)*

- Activate the preapred conda environment that you have previously created using the **Computational Setup page**

*Your conda environment is an area where you can work where all necessary programmes are installed*

```
conda activate moon_code
```
<br>

---
**NOTE**

*Every time you open a new terminal you will need to run the line of code above to reactivate the conda environment*

---
<br>

The terminal will look like the image below when the environment has been activated, where **(moon_code)** will appear before your username, if you do not see this, retry activating the conda environment

<img width="571" alt="Screenshot 2024-12-18 at 13 44 50" src="https://github.com/user-attachments/assets/d742fc14-86ff-405f-ad44-53d2b2178041" />

<br>

---


## 2) Basecall your sequence data using dorado

- Migrate to the folder where the data from **MinKNOW** has been transferred to. MinKNOW outputs all seqeucing data in a folder with the name of the sequencing run, and all of the subdirectories are uniform between different runs.
  
The folder will look similar to the image below:

<img width="683" alt="Screenshot 2024-12-17 at 16 18 37" src="https://github.com/user-attachments/assets/bb7334a0-7883-481f-a96b-58c677765db1" />

---
**NOTE**

*It is good to keep this run directory saved in a permanent storage space in case we need to go back to the raw data or retrieve information from each run*

---
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
 











