# Analysis of Nanopore data for custom internal barcode counting

![Screenshot 2024-12-16 at 17 58 09](https://github.com/user-attachments/assets/261030e8-6b56-40a6-959f-c978ee97aa23)

This page will take you through the basic steps of analysis involved:

**>Base calling**

**>Coverage analysis**

**> Extracting the number of reads mapping to specific internal barcodes**



## 1) Set up your environment for sequence analysis

Migrate to whereever you are working with your Nanopore data *(I might have to show you how to do this if you are using the server)*

When in your workstation, you will have to download the pre-preapred conda environment that I have previously created, and you will have previously installed using the **Computational Setup page.**


Your conda environment is an area where you can work where I have installed all of the programmes that you will need for these analysis steps.

The programmes installed that you will need for this conda environment are all listed in a XXX.yml file, which I will email to you with the link to this site. Please download and save this file on your local PC.

```
conda activate moon_code
```

This should change your command line so that it starts with (moon_code):

<img width="474" alt="Screenshot 2024-12-17 at 16 49 30" src="https://github.com/user-attachments/assets/dcd9909b-405d-4193-86c7-1ccceeb15740" />

Now you have all of your packages and data that you need for the following analyses.

## 2) Basecall your sequence data using dorado

Migrate to the folder where the fastqs are outputted

<img width="683" alt="Screenshot 2024-12-17 at 16 18 37" src="https://github.com/user-attachments/assets/3a866d2c-fcd4-4de3-b60c-784c2489daff" />

Create a folder for new basecalling output (mkdir) and then migrate into it (cd)

```
mkdir dorado_sup_basecall
```

```
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













