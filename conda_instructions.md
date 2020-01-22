---
layout: default
title:  'Schedule - scRNAseq course'
---

#### <img border="0" src="https://hackernoon.com/hn-images/1*rW03Wtue71AKfxnx6XN_iQ.png" width="50" height="50"> Conda Instructions
***

In this workshop you will use conda environments to run the exercises. This is because conda environments allow all students to have the save computing environment, i.e. package versions. This enforces reproducibility for you to run this material without the need to re-install or change your local versions. See and graphical example below:


<img border="0" src="https://nbisweden.github.io/excelerate-scRNAseq/logos/conda_illustration.png" width="400">


[Conda environments](https://docs.conda.io/projects/conda/en/latest/user-guide/concepts/environments.html) are a self-contained directory that
you can use in order to reproduce all your results. Two of the required software are not available as Conda packages, please see the separate [instructions for installing SingleR and CHETAH](https://raw.githubusercontent.com/NBISweden/excelerate-scRNAseq/master/notes_installation.txt).

Briefly, you need to:  

1. Install Conda and download the `.yml` file
2. Create and activate the environment
3. Deactivate the environment after running your analyses

You can [read more](https://nbis-reproducible-research.readthedocs.io/en/latest/conda/) about Conda environments and other important concepts to help you make your research reproducible.

<br/>

##### Install Conda and download the environment file
***

You should start by installing Conda. We suggest installing either Miniconda, or Anaconda if storage is
not an issue. After [installing Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html),
download the course [Conda file](labs/environment_r.yml) and put it in your working folder.

After this, you should have a file named `environment_R.yml` in your directory (it does not matter where, you can save on Downloads folder for example).

<br/>

##### Create an environment from file
***

In terminal, `cd` to the folder where you saved the environment file and create an environment called `scRNAseq2020` from the
`environment_r.yml` file:

```
conda env create -p scRNAseq2020 -f environment_r.yaml
```

Several messages will show up on your screen and will tell you about the installation process. This may take a few minutes depending on how many packages are to be installed.

```
##Collecting package metadata: done
##Solving environment: done
##
##Downloading and Extracting Packages
##libcblas-3.8.0       | 6 KB      | ############################################################################# | 100%
##liblapack-3.8.0      | 6 KB      | ############################################################################# | 100%
##...
##Preparing transaction: done
##Verifying transaction: done
##Executing transaction: done
```

<br/>

##### Activate the environment
***

To activate an environment type:

```
source activate ./scRNAseq2020
```

From this point on you can run any of the contents from the course. For instance, you can directly launch RStudio by
typing `rstudio`. Here it is important to add the `&` symbol in the end to be able to use the command line at the same time if needed. You can open other files from Rstudio later as well.

```
rstudio ./labs/compiled/my_script.Rmd &
```

Similarly, you can open python notebooks by typing:

```
jupyter notebook ./labs/scapy/01_qc.ipynb
```

<br/>

##### Deactivate the environment
***

After you've ran all your analyses, deactivate the environment by typing:

```
conda deactivate
```

<br/>

<br/>

### Alternative option (VIRTUALBOX)

If by any means you see that the installations are not working as it should on your computer, you can try to create a virtual machine to run UBUNTU and install everything there.

1. Download and install on your machine VIRTUALBOX
https://www.virtualbox.org

2. Download the ISO disk of UBUNTU
https://ubuntu.com/download/desktop

3. On VIRTUALBOX, click on `Settings` (yellow engine) > `General` > `Advanced` and make sure that both settings **Shared Clipboard** and **Drag'n'Drop** are set to `Bidirectional`.

4. Completely close VIRTUALBOX and start it again to apply changes.

5. On VIRTUALBOX, create a machine called Ubuntu and add the image above
- set the memory to the maximum allowed in the GREEN bar
- set the hard disk to be dynamic allocated
- all other things can be default

6. Proceed with the Ubuntu installation as recommended. You can set to do "Minimal Installation" and deactivate to get updates during installation.

7. Inside UBUNTU, Download conda:
https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

8. Inside UBUNTU, open the TERMINAL and type the commands below. Follow the instructions for the installation there.
```
cd ~/Downloads
sh Miniconda3-latest-Linux-x86_64.sh
```

9. Close Terminal to apply the CONDA updates. Then you can create a course folder, download the environment file and create the environment:

```
mkdir ~/Desktop/course
cd ~/Desktop/course
wget https://raw.githubusercontent.com/NBISweden/workshop-scRNAseq/master/labs/environment_r.yml
conda env create -n scRNAseq2020 -f environment_r.yml
```