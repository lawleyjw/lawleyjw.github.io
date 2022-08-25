# Beginners guide to Gowonda HPC

### A quickstart for bioinformatic analysis in the Griffith University High Performance Computing (HPC) Cluster (Gowonda)

*Created by:* Jonathan W. Lawley

*Last updated:* 19 August 2022

*For questions or issues:*
jonathan.wanderleylawley@griffithuni.edu.au

If you are using this workflow for your research, please cite the GitHub repository https://github.com/lawleyjw/bioinfomatics-workflows

*License:* [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)

### 1) First things first
Request permission to access Gowonda [here](https://www.griffith.edu.au/eresearch-services/hpc) (click on "Create HPC account").

### 2) To access Gowonda

If you are not connected directly to the Griffith University network (cable or WiFi), you need to set up a virtual private network (VPN) to access it remotely (click [here](https://intranet.secure.griffith.edu.au/computing/remote-access/virtual-private-network) for instructions).

Open a [UNIX shell](https://en.wikipedia.org/wiki/Unix_shell) (for Linux and Mac users, search for *Terminal*; for Windows users you need to install one, such as *[Gitbash](https://gitforwindows.org)*) and type in:
`ssh your_snumber@gc-prd-hpclogin1.rcs.griffith.edu.au`

Use your Griffith University password for access.

*Voilà, you're in!* From now on, everything will be executed in Gowonda through this UNIX shell (I know, it might look scary at first, but keep going!).

### 3) Transferring files to/from Gowonda

**From your computer:**

Open the UNIX shell we just talked about (if you are logged into Gowonda, log out by typing `exit`) and use the *secure copy* command `scp`:
`scp /folder_in_local_computer/another_folder/file.txt your_snumber@gc-prd-hpclogin1.rcs.griffith.edu.au:~/`

*You'll be asked for your Griffith University password to allow file transfers.*

> **Tip:** To enter the local path to your file (`/folder_in_local_computer/another_folder/file.txt`), simply drag and drop the file into the command line and the path should appear printed. Easy peasy!

This will transfer the file into your home directory in Gowonda (the `~/` at the end of the command line above). If you have many files to transfer, I recommend compressing them (as .zip or .tar) and uncompressing once in Gowonda.

**To your computer:**

Merely swap the order of the arguments from the command above (remember to be logged out of Gowonda):
`scp your_snumber@gc-prd-hpclogin1.rcs.griffith.edu.au:~/file.txt /folder_in_local_computer/another_folder/`

**From the internet:**

*You can use this to transfer files from a link directly into Gowonda. This link can be the URL sent by the sequencing facility to download your raw reads or a link to your file in a cloud service.*

Log into Gowonda and run `source /usr/local/bin/s3proxy.sh` to allow internet access from the cluster.

Now run `nohup wget download_url > download_log.txt &` to download files directly into the cluster. The download command is `wget`, while `nohup` puts the command you are running in the background, so you can keep doing other stuff in the cluster.

> **Tip:** To see the download's progress check the download log text file. One way to do this is by running `tail download_log.txt` from the log file's folder, this will show the last 10 lines of that text file.

**To the internet (cloud storage):**

Once you are done with your analyses, I recommend compressing your files and downloading from Gowonda into your favourite backup spot, which can be a cloud storage service.

You can backup files directly from Gowonda to CloudStor (and perhaps other cloud services - Google is your friend!). Click [here](https://mri.sbollmann.net/index.php/2021/08/03/cloudstor-and-high-performance-computing-systems/) for CloudStor backup instructions and remember to first run `source /usr/local/bin/s3proxy.sh` to allow internet access through Gowonda.

*After you have your backup in place, delete it from Gowonda to make space for other users.*

### 4) Navigating Gowonda

When you log into Gowonda you land in your home directory (`/exports/home/your_snumber/`, or simply `~/`)

You can use the *print working directory* command `pwd` to find out the path of the directory you are in.

Now have a look around your home directory. The *list* command `ls` should give you a list of files and folders in the directory you are in.

> **Tip:** Try the *list* command with the options of long format and readable file size `ls -lh`. This way it's a bit easier to see your files and to check file sizes. You'll notice this doesn't show the size of folders, for that purpose use `du -hs your_folder` .

Your working directory is `scratch` (located within your home directory) and this is where you will add files and scripts that you want to run in Gowonda. Files you are not using at the moment but might still need in the near future can be transferred to your home directory to free up scratch space (remember to backup your files and delete them from Gowonda once you are done).

If you are working with the file you just transferred in the previous step, *move* it to `scratch` using the `mv` command:
`mv file.txt ~/scratch`

When running commands on files, you either have to be in the same folder as your file or tell the command where your file is. For example, if you are now in your home directory and want to move your file back from `scratch`, you have two options:
- A) *Change your working directory* `cd ~/scratch` and then `mv file.txt ~/`
- B) `mv ~/scratch/file.txt ~/`

>**Tip:** A good coding practice is to use as few commands as possible (think lazy!), so always go for option B.

You can replace the `mv` command with `cp` if you want to create a *copy* instead of moving. 

You can also use the `mv` command to rename a file:
`mv file.txt new-name.txt`

To *make a new directory* (AKA create a folder) and start organising your files use `mkdir`:
`mkdir new-folder`

To *remove* a file use `rm`:
`rm file.txt`

>**Tip:** If you want to run a command on a folder (for example, remove a folder), you normally only need to add the option `-r` to a command. For example, you can remove the new folder you created above with  `rm -r new-folder`.

If you want to check which arguments or options a certain command requires, the *manual* option usually works for the base commands. For example, `man rm` will show you how to run the *remove* command and the options it accepts. Alternatively try using the *help* option `-h` after a command, especially for commands from installed programs.

### 4) Running jobs in Gowonda

To run bioinformatic analyses in Gowonda you need to create a scheduler script that tells the cluster what [bash](https://en.wikipedia.org/wiki/Bash_%28Unix_shell%29) commands to run on which files and what computational resources to use.

Here is an example of script to run FastQC on paired-end raw reads:
```
#!/bin/bash
#PBS -m abe
#PBS -M your_email_adress@griffith/griffithuni.edu.au
#PBS -N name_you_choose_for_job
#PBS -l select=1:ncpus=2:mem=8gb,walltime=10:00:00

cd $PBS_O_WORKDIR
module load anaconda3/2021.11
source activate labmcdougall

fastqc --noextract -o QC/ Undetermined_S0_L001_R1_001.fastq
fastqc --noextract -o QC/ Undetermined_S0_L001_R2_001.fastq
```

In this script you are specifying your email (the cluster will email you about the job run), the name you choose for the job (so you can identify it) and the computational resources to use (for more details on these resources click [here](https://gu-eresearch.github.io/hpcWorkshop/content/23-resources.html)). You are also specifying that the job should run on your current working directory (which needs to be within `scratch`), and the `module` and `source` are loading the McDougall lab *[Anaconda](https://anaconda.org)* environment where most of the programs needed (and the associated commands) are installed (more on *Anaconda* on the Appendix).

To run this job in Gowonda you can either:
- A) Copy that text into a text editor such as Sublime, BBEdit, VSCode or Notepad++ (to name a few), and save it with a bash extension `your_job.sh`. Then, upload it to the desired folder in Gowonda. ***Windows users***: the bash script needs to be saved as UNIX, so open up Notepad++ and create your scheduler script, then go to “Edit” - “EOL conversion” - select “UNIX” then save as a .sh file).
- B) Use `nano` text editor within Gowonda (there are other command line text editors if you have another preference). After you run `nano` the screen will change into a text editor. You can paste the script text here and press Ctrl+O to save (you'll see a guide to keyboard shortcuts at the bottom). Write down the file name (don't forget to add .sh as the extension) then hit Enter. Exit with Ctrl+X.

>**Tip:** I recommend option B as it is the fastest, unless you have multiple scripts ready to go that you have already edited in your computer's text editor.

Finally, go to the folder where your job script is and *submit your job to the queue* with `qsub your_job.sh`. Remember that the files you are running analysis on must be in the same folder as your script, or otherwise specified in the commands within.

To check your the *status of your queued job* use `qstat -u your_snumber`, or just `qstat` to see all jobs running or queued (you'll see an R or a Q, respectively, on your job's information row, as well as the elapsed time). 

### For more information and guides about Gowonda check out the [Griffith HPC Cluster Documentation](https://griffith.atlassian.net/wiki/spaces/GHCD/overview?homepageId=4030474) and/or the [Griffith HPC Workshop](https://gu-eresearch.github.io/hpcWorkshop/).
### You can find many bash command cheat sheets around (again, Google is your friend!), but [here](https://github.com/RehanSaeed/Bash-Cheat-Sheet) is one I like.

---

### Appendix - *Anaconda*

*[Anaconda](https://en.wikipedia.org/wiki/Anaconda_%28Python_distribution%29)* is a software distribution for scientific computing that houses *conda*, the package manager underlying *Anaconda* that makes it easier for users to install and manage packages/programs, especially in the HPC setting.

I’ve created a *conda* environment called *labmcdougall* and installed all the packages/programs that I have used to date for transcriptomics and protein data. If you need to install other packages, please feel free to get in touch, but I've also shown below how to do it.

**To access the McDougall lab environment:**

Run `module load anaconda3/2021.11`, then `source activate labmcdougall` .

Use `conda env list` to check all environments available in that Anaconda build and `conda list` to see all packages/programs available in the *labmcdougall* environment.

**To install other packages:**

Run `source /usr/local/bin/s3proxy.sh` to allows access to the internet from the cluster. Then `module load anaconda3/2021.11` and `source activate labmcdougall`.

To search for packages you need to install, go to the [Anaconda website](https://anaconda.org) and type in the package you are looking for. For example, if you are searching for bowtie2, [this](https://anaconda.org/search?q=bowtie2) is what you would find. Give preference to packages that are within the *bioconda* channel and click on the package name ([this](https://anaconda.org/bioconda/bowtie2) is what you would see in this example). In the page that opens, you will see a command that you can copy and paste to run the installation. In this example it's `conda install -c bioconda bowtie2`.

Follow through with the installation and you are ready to run the new package!

**To run any of the packages:**

Just modify the example scheduler script in section 4, it is already calling on the *labmcdougall* conda environment to run FastQC.

### For more information check out the [Griffith HPC Cluster Documentation](https://griffith.atlassian.net/wiki/spaces/GHCD/overview?homepageId=4030474), especially the [FAQ section](https://griffith.atlassian.net/wiki/spaces/GHCD/pages/4030751/FAQ+-+Griffith+HPC+Cluster).
