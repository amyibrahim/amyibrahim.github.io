title:null

# 1. PC SET UP 

## Individual installs for packages that are not obtained through conda

### 1) Install conda 

Macbook install through download link:

```
https://www.anaconda.com/download/
```

### 2) Install dorado

Install using dorado's install link depending on what system you are working on:

```
https://github.com/nanoporetech/dorado?tab=readme-ov-file
```

*When using dorado, you have to include the whole file path to whereever you dorado version is installed, the programme itself is within the bin folder*


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

