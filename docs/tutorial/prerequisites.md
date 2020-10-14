---
title: Prerequisites
sort: 1
---


## Prerequisite: BioConda

This tutorial is based on [Ubuntu 20.04 TLS](https://releases.ubuntu.com/20.04/), but should work on any modern Linux distribution. Instructions may need to be adapted for macOS and Windows, but note this workflow has only been tested on Linux. 

Below we'll use [Conda](https://conda.io/) which can be installed for all major operating systems and will assist in smoothing out their differences as it provides an independent and versioned distribution of a wide range of bioinformatics tools through [BioConda](https://bioconda.github.io/). Conda is also used by Nextflow to install its workflow dependencies.

### Install Conda

First follow instructions for [installing conda](https://bioconda.github.io/user/install.html#install-conda). 

For Linux:

```
curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
sh Miniconda3-latest-Linux-x86_64.sh
```

Follow the instructions in the installer.

### Activate Conda environment

Next we'll activate a
[Conda environment](environment.yml) that installs Nextflow,
Java and UNIX tools needed for this tutorial:

```sh
$ conda env create -f https://raw.githubusercontent.com/stain/bco-ro-example-chipseq/master/environment.yml

Downloading and Extracting Packages
curl-7.71.1          | 139 KB    | ##################################################################################################################### | 100% 
…
Preparing transaction: done
Verifying transaction: done
Executing transaction: done
#
# To activate this environment, use
#
#     $ conda activate bco-ro
```

**Tip**: You can activate your own environment file with `-f environment.yml`, or change the environment name with `-n bco-ro`

Next we'll activate the environment and check Nextflow got installed correctly:

```sh
(base) stain@biggie:~/src/bco-ro-example-chipseq$ conda activate bco-ro

(bco-ro) stain@biggie:~/src/bco-ro-example-chipseq$ type nextflow
nextflow is /home/stain/miniconda3/envs/bco-ro/bin/nextflow

(bco-ro) stain@biggie:~/src/bco-ro-example-chipseq$ nextflow -version

      N E X T F L O W
      version 20.07.1 build 5412
      created 24-07-2020 15:18 UTC (16:18 BST)
      cite doi:10.1038/nbt.3820
      http://nextflow.io
```


### Installing Nextflow and Docker separately

If for whatever reason you are unable to use Conda, 
follow the [Nextflow: Getting started](https://www.nextflow.io/) instructions, 
including installing the expected version of Java. 

As this example let Nextflow download workflow dependencies with Conda,
you can instead install and use [Docker](https://www.docker.com/) 
or [Singularity](https://sylabs.io/docs/) containers.