

# 1. PC SET UP FOR LINUX SERVER USERS

An account has been made for you as an individual user on the Linux server systems, here we can analyse data and run large processes.

First we will need to install the software that we need for our analyses on each account, then we can head to the nanopore analysis pages.

**We will only need to install these packages the first time that we log in to the server!**ls

### 1. Logging in to your account on the server

   **i) Open the terminal on your PC, this will look something like:**

![Screenshot from 2025-04-04 13-49-13](https://github.com/user-attachments/assets/bbac7c99-387e-40e8-9c17-92ca8cbd9805)

   **ii) log in to your server account using the format below, with your unique username**

```
ssh -X USERNAME@10.18.0.25
```

   **iii) You will be prompted for your password, this is the same as your username, then enter**



### 2. Installing conda / mamba

Conda is a package manager that simplifies the installs of multiple packages, there are various versions of this e.g. conda / anaconda / mamba

*see https://mamba.readthedocs.io/en/latest/installation/mamba-installation.html for info on installs*

   **i) Whilst logged in to your account in the terminal, download the install file for mamba package installer**

```
wget "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
```

   **ii) run the script to install mamba from the downloaded file**

```
bash Miniforge3-$(uname)-$(uname -m).sh
```
*Note: this will install mamba in the miniforge3 folder which will be created in your top directory (pic below). When using conda, you will have to use the FULL FILEPATH rather than just 'conda' (~/miniforge3/bin/conda)* 

![Screenshot from 2025-04-04 14-08-12](https://github.com/user-attachments/assets/48784b3d-a8f8-4897-b822-118a4481ddc5)






### 2. Install dorado

Install using [dorado's install link](https://github.com/nanoporetech/dorado?tab=readme-ov-file) depending on what system you are working on

*When using dorado, you have to include the whole file path to whereever you dorado version is installed, the programme itself is within the bin folder*

![Screenshot 2024-12-16 at 17 54 45](https://github.com/user-attachments/assets/a5f6f51a-c42f-419c-9ef0-c2b5870004ae)

Now that both dorado and anaconda are installed on your system, you can use anaconda (a package manager) to create working environments with the essential programmes installed.

I have created a configuration file which contains all of the essential programmes needed for our code within the Moon lab.

This environment is called **moon_code**

## Install all other packages through conda file

### 3) Download the and activate the moon_code work environment

Firstly, I will email you the appropriate configuration file (.yml)

Download this and save it locally, (I tend to create a folder in my top directory called software, and store configuration files in here)

Then execute the following command to use the configuration file and install all require packages 

```
conda env create -f ~/PATH/TO/FILE/filename.yml
```

This should have a successful install message, let me know if not.

Then to check the code has installed the required environment, execute the following command:

```
conda activate moon_code
```

This should change your extension in the terminal so that it has the following order:

(moon_code)USER@PC:folder_location/

You are now working in an environment where all packages are installed.

**Every time that you leave the terminal, you will need to REACTIVATE YOUR WORKING ENVIRONMENT**

To reactivate your working environment, use:

```
conda activate moon_code
```


## Code editors

You will probably want to download a text editor for code editting, such as Sublime text

```
https://www.sublimetext.com/download
```
