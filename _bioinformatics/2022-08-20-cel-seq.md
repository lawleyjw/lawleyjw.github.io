---
title: 'CEL-Seq workflow'
date: 2022-08-20
permalink: /bioinformatics/cel-seq/
excerpt_separator: <!--more-->
toc: true
---

### Workflow for bioinformatic analysis of CEL-Seq data in the Griffith University High Performance Computing (HPC) Cluster (Gowonda)
<!--more-->
*Created by:* Jonathan W. Lawley
Based on the workflow from Carmel McDougall, with modifications by Tessa M. Page

*Last updated:* 19 August 2022

*For questions or issues:*
jonathan.wanderleylawley@griffithuni.edu.au
c.mcdougall@griffith.edu.au

If you are using this workflow for your research, please cite the GitHub repository https://github.com/lawleyjw/bioinfomatics-workflows

*License:* [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)

### First-time Gowonda/HPC users (or for those that need a detailed refresher) start with the "Beginners guide to Gowonda HPC".

### 1) Convert BCL to FASTQ

The Illumina NextSeq and HiSeq Sequencing Systems and NovaSeq 6000 generate raw data files in binary base call (BCL) sequence file format. If this is your case, you'll have to convert it to [FASTQ](https://en.wikipedia.org/wiki/FASTQ_format) format, which is a text-based format similar to FASTA but it also includes quality scores.

After you've downloaded your raw sequencing file (or files), I recommend transferring it into a new folder in your `scratch`, for example `celseq_raw` (see "Beginners guide to Gowonda HPC" if you don' know how to do this!).

Then unzip it with `nohup tar -xopf your-seqdata-filename.tar &`, and a folder will appear with the same name as your file (if you are not familiar with `nohup`, also check the "Beginners guide to Gowonda HPC").

Now you need to run a scheduler script for the Illumina bcl2fastq conversion software, below is an example. I recommend creating a folder called `fastq` before running the script, for your converted output files.
```
#!/bin/bash -l
#PBS -m abe
#PBS -M your_email_adress@griffith/griffithuni.edu.au
#PBS -N bcl2fastq
#PBS -l select=1:ncpus=16:mem=64gb,walltime=100:00:00
cd $PBS_O_WORKDIR
source $HOME/.bashrc
module load anaconda3/2021.11
source activate labmcdougall
bcl2fastq -r 4 -w 4 -p 14 --runfolder-dir ~/scratch/celseq_raw/your_folder_with_seq_data/ --output-dir ~/scratch/celseq_raw/fastq --minimum-trimmed-read-length 15 
```







