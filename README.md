# Moeller Lab Metagenomics Processing Pipeline

## Overview
Snakemake pipeline for basic processing of metagenomic data from the lab. It accepts raw fastq files of metagenomic data, quality filters it, removes reads that map to the host genome, then builds assemblies of each sample and generates a [sourmash](https://sourmash.readthedocs.io/en/latest/) profile. The current version also generates a taxonomic profile of each sample using [MetaPhlAn3](https://huttenhower.sph.harvard.edu/metaphlan/). Modules that are currently underdevelopment will handle automated binning procedures, as well as strain-level profiling.

## Quick Start Guide

### Install

First, clone this github repository:

```
$ git clone [https://github.com/alandersbio/sn-mg-pipeline-for-della]
cd sn-mg-pipeline
```

We recommend installing and using mamba:

```
$ conda install -c conda-forge mamba
```

Then install the snakemake version for this workflow using mamba:

```
$ mamba env create -n snakemake -f resources/env/snakemake.yaml
$ conda activate snakemake
```

### Della-specific changes:
In addition to everything in the quick start guide copied below, here are a list of tips for running this pipeline on Della:

This repository has all the snakemake wrappers cloned locally in the bio directory. However, you will need to update the file paths to each wrapper in each of the following snakemake files: qc.smk and assemble.smk. 

Other tips for della:
Run the snakemake outside of slurm until environments are created before submitting the job to slurm, where the cluster is disconnected from the internet.

If multiQC fails due to a missing wrapper, try the following:
1. Ensure snakemake_wrapper_utils is Installed
2. Activate the Conda environment that Snakemake is using for MultiQC:
       source /usr/licensed/anaconda3/2024.10/bin/activate /scratch/gpfs/al8216/sn-mg-pipeline/.snakemake/conda/f69aece39fb3135ad1942deab3a2bb4e_
4. Then, check if snakemake_wrapper_utils is installed:
      python -c "import snakemake_wrapper_utils"
6. If this fails, install the module manually:
       pip install snakemake-wrapper-utils


### Update Files

Now you can update three files that are located in the `./resources/config` directory.

The first two files are `samples.txt` and `units.txt`. The `samples.txt` file is your basic metadata file, with each row representing a sample in your dataset and each column containing the corresponding information about that sample. The first column should named "Sample" and should contain the name for each sample. Any addition columns are not used at this step.

The `units.txt` file should have only 4 columns, and each row should correspond to a sample found in the `samples.txt` file. The first column, "Sample", should be all or a subset of the "Sample" column in the `samples.txt` file. The second column, "Unit", should denote which analysis block each sample belongs to. In our case, we use sequencing run/lane, but you may use other information based on your experimental design. The third and fourth columns (named "R1" and "R2", respectively) should include the full file paths to the forward and reverse fastq files for that sample.

The last file to update is the the `config.yaml` file. This is where you can select the parameters for each step in the analysis pipeline. Refer to the documentation for each tool individually for more information. Also, be sure to change the NCBI GenBank Accession number to your host of interest.

You will also need to update the following snakemake files to point to the 



NOTE: You can select which metagenomic assembler you want to use under the the "assemblers:" header. The current options are [metaSPAdes](https://cab.spbu.ru/software/meta-spades/) and [MEGAHIT](https://github.com/voutcn/megahit) Simply delete the assembler you don't want to use. Otherwise both will run.

### Run the Pipeline

After you have updated the files described above, you can start the pipeline. First run:
```
conda install -n base -c conda-forge mamba
```

Then begin the run using:
```
snakemake --cores 8 --use-conda
```
The first time you run this, it may take longer to set up your conda environment. Be sure to select the appropriate number of cores for your analysis.
