---
title: 'Transcriptome assembly workflow'
collection: bioinformatics
permalink: /bioinformatics/transcriptome/
excerpt: 'Workflow for transcriptome assemblies in the Griffith University High Performance Computing (HPC) Cluster (Gowonda)'
toc: true
---

**Workflow for transcriptome assemblies in the Griffith University High Performance Computing (HPC) Cluster (Gowonda)**

Based on the workflow from Carmel McDougall, with modifications by Tessa M. Page

*License:* [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)

**First-time Gowonda/HPC users (or for those that need a detailed refresher) start with the [Beginners guide to Gowonda HPC](https://lawleyjw.github.io/bioinformatics/beginners-guide-gowonda/).**

### 1) Run a quality control with FastQC

Sequenced reads are normally provided in [FASTQ](https://en.wikipedia.org/wiki/FASTQ_format) format, which is a text-based format similar to FASTA but it also includes quality scores. Transfer the compressed FASTQ files into Gowonda (see section 3 of the "Beginners guide to Gowonda HPC" if you don' know how to do this!).

After you've transferred your FASTQ files into Gowonda, I recommend moving it into a new folder in your `scratch`, for example `transcriptome_raw`.

Now you need to run a scheduler script for FastQC, below is an example. Before running the script, I recommend creating a folder called `fastqc` for your converted output files.
```
#!/bin/bash -l
#PBS -m abe
#PBS -M your_email_adress@griffith/griffithuni.edu.au
#PBS -N fastqc
#PBS -l select=1:ncpus=1:mem=10gb,walltime=04:00:00

cd $PBS_O_WORKDIR
module load anaconda3/2021.11
source activate labmcdougallRoot

fastqc --noextract -o fastqc/ RNA_1.fastq.gz
fastqc --noextract -o fastqc/ RNA_2.fastq.gz
```
Download the the summarised FastQC reports (the HTML output files) in the `fastqc` folder to your computer and open in a web browser to check all the quality information. If you want more details and exact values for the different quality tests, check the output ZIP files.

For guides on how to interpret the FastQC outputs check [Babraham Bioinformatics](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) (the developer) and/or this [FastQC tutorial](https://rtsf.natsci.msu.edu/genomics/tech-notes/fastqc-tutorial-and-faq.aspx).

### 2) Assembly with Trinity

If paired-end reads were generated, they need to be assembled into transcripts, which Trinity does!

I recommend creating a new folder in `scratch` for the assembly, like `transcriptome_assembly`. The output folder for Trinity should also contain the program's name to avoid auto-deletion (Trinity will error out if you don't do this!), so create another folder, such as `trinity`.

Now you can run a scheduler script to run Trinity, such as the one below.
```
#!/bin/bash -l
#PBS -m abe
#PBS -M your_email_adress@griffith/griffithuni.edu.au
#PBS -N trinity
#PBS -l select=4:ncpus=4:mpiprocs=4:mem=8gb,walltime=100:00:00

cd $PBS_O_WORKDIR
module load anaconda3/2021.11
source activate labmcdougallRoot

Trinity --seqType fq --trimmomatic --no_normalize_reads --jaccard_clip --max_memory 32G --CPU 4 --inchworm_cpu 4 --SS_lib_type RF --left ~/scratch/transcriptome_raw/RNA1_1.fastq.gz --right ~/scratch/transcriptome_raw/RNA1_2.fastq.gz --output ~/scratch/transcriptome_assembly/trinity
```


*Under construction*
