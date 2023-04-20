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

Now you need to run a scheduler script for FastQC, below is an example. I recommend creating a folder called `fastqc` before running the script, for your converted output files.
```
#!/bin/bash -l
#PBS -m abe
#PBS -M your_email_adress@griffith/griffithuni.edu.au
#PBS -N fastqc
#PBS -l walltime=20:00:00

cd $PBS_O_WORKDIR
module load anaconda3/2021.11
source activate labmcdougallRoot

fastqc --noextract -o fastqc/ RNA_1.fastq.gz
fastqc --noextract -o fastqc/ RNA_2.fastq.gz
```

*Under construction*
