# dry-lab-standard

For datasets and tutorials:
Please visit our  [Wiki](https://github.com/RCHENLAB/dry-lab-standard/wiki)


Get started

As bioinformatician or computational biologist, sometimes we need to share and create a lot of codes. To organize this, we can use Github. To create an account, Go to https://github.com/join in a web browser. You can use any web browser on your computer, phone, or tablet to join. Now you can add your personal details. In addition to creating your username and entering an email address, you'll also have to create a password. Your password must be at least 15 characters in length or at least 8 characters with at least one number and lowercase letter. Now click in the create account button. Click the Verify email address button in the message from GitHub. This confirms your email address and returns you to the sign-up process. Well done! 

To know how to start to use slurm, please visit Slurm 4 beginners.


Prerequisites about basic tools and Linux operations

We will need several tools to start the single cell analysis such as R, Seurat, and Python. B
use Conda is a very powerful software management system, we are going to install tools via Conda. 

1. Access to the Taco server 
First of all, you need to set the taco server account
Go to the adress https://it.bcm.edu/  and to create a ticket to request access to taco server, go to **Report a Problem** and make the request described in Short description and describe your issue 


For example, 

```
Could you please help create an account for our student xxx on the Taco server? Please find the info below. Thank you so much.

Full name: 
BCM ECA: 
Email: bcm_eca@bcm.edu
xxx will be in Dr. Chen's group.

Best regards, xxx

NOTE: BCM ECA is the Educational credential assessment  (ECA) number
STARTS WITH u + 6 digit number

Example:
Paul John
u228876
```
Due to the Taco server is accessible with the BCM network only, we will need to connect BCM VPN first before logging in to the Taco server. In order to use BCM VPN, we need to set up MFA using our BCM ECA account. Official IT documents can be found at below (Need BCM account to view the page). 
https://bcm.service-now.com/bcmsp?id=kb_article&sys_id=35d6ea9ddb83cfc0729c73d78c9619d6 
 
Briefly, there are two ways to use MFA, SMS and Google Authenticator mobile app. See below. 
https://bcm.service-now.com/bcmsp?id=kb_article&sys_id=7fb760da1b3f9414d580b997cc4bcbd2 
https://www.bcm.edu/sites/default/files/2019/8a/mfa-install-brochure.pdf 
 
To install MFA via SMS, we need to link our cellphone number to our BCM account at https://password.bcm.edu. After the cellphone number is linked, we can input “SMS” as response when MFA token is required, and a token will be sent to the phone by a SMS message. 
 
Once MFA is set up, we need to set up VPN. IT help page can be found at below. 
https://bcm.service-now.com/bcmsp?id=kb_article&sys_id=e2672c664fe58780e2d104c85210c754 
 
In short, visit https://vpn.bcm.edu (select BCM-default) and follow instructions to download and install Cisco AnyConnect App. 
 
After connecting BCM VPN, run below command to log in to the Taco server. 

```
ssh BCM_ECA@taco.grid.bcm.edu

```


*Important Note: If you use windows, please download the following program: <https://www.putty.org/>. To make the loggin in taco, follow this steps:*

```

Go To Session - Host Name or IP adress
Write 10.66.4.154
Port: 22
Connection type: SSH
Now click in OPEN
One screeen requesting your loggin and password will be appear if all its well setup

```

If you are not in Windows, skip this part!!! 


Where, replace “BCM_ECA” with your ECA, and it will prompt for password. 
 
2. Install Conda on the Taco server   
 
2.1 Two directories on the Taco server 
 
There are two directories for our use currently. 
 
 ```
 /storage/chen/home/BCM_ECA 
/storage/singlecell/BCM_ECA

NOTE: BCM ECA is the Educational credential assessment  (ECA) number
STARTS WITH u + 6 digit number

Example:
Paul John
u228876
```


The hard drive of the first directory is RAID 6 with a redundant copy of files, so it is suitable for important files, such as installed software, developed scripts and source codes. The second directory is used to store large size files such as analysis results. We are going to install Conda and other open-source tools under the first directory and put the analysis results under the second directory. 
 
2.2 To install Conda on the Taco server 
 
To install conda on the Taco server, we need to first download a Conda Linux installer from the below link. 
https://docs.conda.io/en/latest/miniconda.html 
 
E.g., we want to install the “Python 3.9” Linux installer. 
 
Due to the system version Python library will be imported automatically, and it will clash with Conda Python, so we need to reset the Python library environment. Run below command to do this. 


```
echo “unset PYTHONPATH” >> ~/.bashrc 
echo “unset R_LIBS_SITE” >> ~/.bashrc
```

Relogin the Taco server to recognize the above configuration. Then run below commands to download and install Conda. 

```
mkdir -p ~/tools/miniconda3 && cd ~/tools/miniconda3 
wget https://repo.anaconda.com/miniconda/Miniconda3-py39_4.9.2-Linux-x86_64.sh 
bash Miniconda3-py39_4.9.2-Linux-x86_64.sh
```

Following the prompt instructions, we can install Miniconda3 in a local directory. After installation, we need to reopen the terminal session (open a new window, or re-login to the server) so that Conda paths will be recognized. By default, the Conda environment is not activated, but we need to activate one to install new utilities. The “base” environment is the default environment of Conda. To activate it, we can simply run 

```
conda activate

```

#Note: do not run conda at head node!

To use conda or any scripts, go to other node. To select the node, use *hls* command. To change the current node, use *sruntaco.sh nameofnode*, for example, to go to c00 node, use *sruntaco.sh c00*

2.3 Common conda commands 
 
There are several commonly used Conda commands. 

```
conda env list 
conda activate 
conda deactivate 
conda install 
conda create -n newenv 
conda search

```

A good cheat sheet has also summarized commonly used commands. See 
https://www.datamachinist.com/cheat-sheets/anaconda-cheat-sheet/ 


We can export conda env from one user to another!
Lets try:

```
#Copy cellqc env from Cristal
conda env export > environment.yml

#Download to the new user, and use this followed command:
conda env create -f environment.yml

```
 
3. Good practices to use working with Conda

```
1) Use Conda environments: One of the main benefits of Conda is its ability to create isolated environments. Always create a new environment for each project you work on. This ensures that the dependencies for each project are kept separate and avoids conflicts between packages.
 
2) Use environment.yml files: You can export the dependencies of your environment to a YAML file using conda env export > environment.yml. This file can then be used to recreate the environment on another machine or share with others.
 
3) Use descriptive names for environments: When creating a new environment, use a descriptive name that relates to the project. This will make it easier to identify which environment you need to activate when working on a specific project.
 
4) Always activate an environment before working in it: When you create a new environment, you must activate it before you can use it. To activate an environment, use the command conda activate environment_name.
 
5) Use conda deactivate to deactivate an environment: When you are finished working in an environment, always deactivate it using conda deactivate. This will return you to the base environment.
 
6) Update Conda regularly: Conda is regularly updated with bug fixes and new features. Always update to the latest version of Conda using conda update conda.
 
7) Use conda clean to free up disk space: Conda caches packages, which can take up a lot of disk space over time. Use conda clean --all to remove unused packages and free up disk space.
 
8) Use conda list to view installed packages: Use the conda list command to view the packages installed in the current environment.
 
9) Use mamba install to install packages: Use the mamba install command to install packages into the current environment. Specify the package name and version if necessary. For more info, please visit mamba documentation.

By following these good practices, you can make your workflow more efficient and avoid common pitfalls when working with Conda.
```

4. Install Seurat 

We can install R and Seurat via Conda. Namely, 

```
conda config --add channels defaults --add channels bioconda --add channels conda-forge 
conda install -y mamba 
mamba install -y r-seurat 
conda install -y r-seurat
```


The latest Seurat and dependent R will be installed automatically. To verify the installation of Seurat, run below 

```
R 
library(Seurat)
```



No error message suggests that Seurat is correctly installed, and can be imported into the R session correctly. 


5.  Several commonly used Linux commands 
- ls: to list files or directories 
- cd: to change a working directory 
- cp: copy a file or directory 
- mv: move or rename a file or directory (-i is suggested). To move a large directory, it is encouraged to copy (by cp) or sync (by rsync) it to a new place first, then delete the old one. Because a partial move will cause corrupt source and destination directories. 
- rm: remove a file or directory (always be cautious with this remove command because removed files will not be recovered. Think twice before using this command, and -i is suggested) 
- more/less: two text file peek commands 
- man: return documentation for a command, such as `man ls`. 

6. To know a text editor on Linux. It is important to edit files on Linux efficiently, and Vim or Emacs are typically used. It is important to know basic editing operations include insert, copy, paste, selection, delete, move, search, and replace. Some cheat sheets will help remember common operations. Such as https://devhints.io/vim. 


7. Tips to install the newest version of R 
To run correctly cellqc, we need to make sure if the Rbase version is the newest one. 
To install the newest version of R-base, run the following command: 

```
conda install r-base=4.2.2 
```

To know what is the newest version of a conda package, e.g., R-base, go to Anaconda website < https://anaconda.org/> and search the package 
https://anaconda.org/search?q=r-base 


8. Tips to easy download files from server to computer 

FileZilla is an open source software distributed free of charge under the terms of the GNU General Public License. < https://filezilla-project.org/ > 
For Windows users 
For Mac 
For Linux 

After installation, we need to set information about the remote machine, as follow:




Figure: Host: IP address of the remote machine; Username: login – ECA – BCM ID - same as used to connect to the taco server; Password: same as used to connect to the taco server; Port: 22 (sometimes we need to take in blank). 

Now we can simple drag and drop files from our computer to taco server, and vice-versa. We can also click with the right button in the desire file, and select the Download option. 

This section is to help use the Taco server efficiently. It first describes available computing nodes and their resources. Then, a script to submit jobs to Slurm is described. Also, several commonly used commands are listed to check jobs status. 

The taco server has 14 computing nodes, and below we can see their detailed resource.




1.These computing nodes are organized in 4 queues (or called partitions in Slurm) where we can submit interactive and batch jobs. 

1.1 The short queue. This queue has four computing nodes, i.e., d00, d01, d02, and d03. Each of them has 48 CPUs and 125GB memory. This queue has a wall-time of 12 hours. That is, a job will be terminated automatically after 12-hour limit. This queue is relatively free and very suitable for small jobs that can be finished within 12 hours. 

1.2 The mhgcp queue. This queue also has four computing nodes, i.e., c00, c01, c02, and m00. Computing nodes c00, c01, and c02 have 56 CPUs and 256GB memory. The node m00 is even larger with 112 CPUs and 1TB memory. This queue does not have a time limit for a job, and it is suitable for a large job that requires a large memory and CPUs. Due to many people are using this queue by default, this queue is typically very busy compared to the "short" queue. 

1.3 The gpu queue. This queue has two GPU nodes, i.e., g00 and g01. The node g00 has 128 CPUs, 2 GPU devices, and 1TB memory. The node g01 has 16 CPUs, 4 GPU devices, and 375GB memory. This queue is suitable for GPU tasks. Of cause, it is also suitable for a relatively large CPU jobs by its large CPUs and memory when it is free. 

1.4 The interactive queue. This queue has four small computing nodes for interactive jobs only, i.e., r00, r01, r02, and r03. Each node has 16 CPUs and 64GB memory. They are suitable for small interactive jobs. 

1.5 The headnote h00 is not used for computing jobs. It is for login purpose, and it is very suitable for data download and transfer due to it is connected to 10Gb/s network. We are safe to use all above queues by submitting jobs through Slurm. 


2.To submit interactive and batch jobs to Slurm 

Currently, the computing resource is organized via Slurm on the Taco server. In principle, we want to run interactive and batch tasks by submitting all interactive jobs and batch jobs via Slurm. Two customized scripts are helpful to submit interactive and batch jobs, i.e., `sruntaco.sh` and `slurmtaco.sh`, respectively. `sruntaco.sh` is to submit interactive jobs, and `slurmtaco.sh` is to submit batch jobs. To use the above two scripts, please add the below command to the end of the Bash configuration file `~/.bashrc`. I.e.,  

vi ~/.bashrc 
source /storage/chen/data_share_folder/jinli/script_bcm/bashrc

 
This command will add the directory of the two scripts to the PATH variable. After adding the above command, please relogin the server to invoke the configuration to update the PATH. Now, it is ready to run `sruntaco.sh` and `slurmtaco.sh` from any directories on the server. 

2.1 To submit interactive jobs via `sruntaco.sh` 

Sometimes, we need to run interactive tasks to test scripts or commands on the server. We should create an interactive Slurm job to run interactive commands. In principle, please never `ssh` to a computing node and run commands there. A direct `ssh` will bypass Slurm job management, and it will compete the computing resource with jobs submitted to the Slurm. Instead, we could use the below script `sruntaco.sh` to request an interactive job. 

sruntaco.sh -h

Options: 
  -h|--help                                           Help. 
  -j|--jobname JOBNAME                  Jobname. Default: $(basename "$(mktemp -u)"). 

Example: 
sruntaco.sh g01 
sruntaco.sh mhgcp-g01



Note: 
  1. This is to start an interactive session through SLURM. 
  2. The partition will be inferred automatically from the host. 

Date: 
  2021/12/01 

Authors: 
  Jin Li <lijin.abc@gmail.com> 


This script actually invokes a fresh bash session on a computing node by `srun` (a Slurm command), say, 

srun -p "$partition" --nodelist="$host" -J "$jobname" -n 1 --pty bash



Slurm will schedule the submitted interactive job once the computing resource is ready on the requested node. 

For example, the below command will open a new bash session on r03 by requesting 1 CPU and 2GB memory. We are ready to run interactive commands on r03. 

sruntaco.sh r03  


2.2 To submit batch jobs via `slurmtaco.sh` 

Mostly, we want to run a long batch of commands in a script, and we want to submit a batch job for it. 

slurmtaco.sh -h

Options: 




Example: 

slurmtaco.sh -- cmd 
slurmtaco.sh -p short -t 2 -m 10G -- samtools index file.bam 
slurmtaco.sh -p mhgcp -t 8 -m 40G -n mhgcp-c02 -- cmd



Note: 
  1. `-n|--node` is exclusive and superior to `-x|--exclude`. 



The above script is to request detailed computing resource for the batch command. I.e., 

-p : indicating which partition to use. Default is the short queue. 
-t  : the number of CPUs to request.
-m : the size of memory for a job 
-n : a specific node in the queue



This script is actually calling `sbatch` (a Slurm command) to submit a batch script with the configured resource requirement. 

Example 1: 

slurmtaco.sh -p short -t 2 -m 10G -- samtools index file.bam 
squeue -u $USER




To submit the running command `samtools index file.bam` (characters after `--`) to the “short” partition in Slurm (-p short). This job is requiring 2 CPUs (-t 2), and 10GB memory (-m 10G). `squeue -u $USER ` is to show the running status of all jobs submitted by user. 

Example 2: 

slurmtaco.sh -p short -t 2 -m 10G -n mhgcp-d03 -- samtools index file.bam 
squeue -u $USER




Similar to the previous example, the job will be submitted to a specific node “mhgcp-d03” (-n mhgcp-d03) of the “short” queue. This `-n` is useful when we know which node is idle to avoid those busy nodes. 

Notes 

Our dedicated machines 

Sometimes, the Slurm partition is extremely busy, our job is less likely scheduled soon, and we have emergency jobs to run immediately, we still have a last backup solution. We have our dedicated machines, c00, c02, and g01. We could directly login to these 3 machines and run a command there. Because these 3 machines are our dedicated machines, so, people from other labs will not complain about it. But please refrain from doing this unless necessary because it will compete resources and slow down all the jobs in the machine. 

3.2 Check the status of queues using sinfo 

sinfo



Show detailed status of computing nodes by sinfo 

sinfo -O nodehost,available,statecompact,cpus,cpusstate,cpusload,memory,freemem 


Show the running jobs and resources using squeue and sacct 

squeue -u $USER
squeue -u u247700 #Example from Cristal's user
sacct -u $USER -L -o JobID,JobName,State,User,Submit,ReqCPUS,ReqMem,NCPUS,Elapsed,NodeList,MaxRSS 


The column labeled ST displays each job's status: PD means "Pending" (waiting); R means "Running"; CG means "Completing" (cleaning up after exiting the job script).

How to create a good documentation: An Odyssey

Always pass on what you have learned.” — Yoda

Documentation effectively connects humans and machines. 
Why is this so important? adapted from Berkeley guide

For you: 
You will be using your code in 6 months
You want people to use your code and give you credit
You want to learn self-determination
Others would be encouraged to contribute to your code

For others: 
Others can easily use your code and build upon it
For science:
Advance the science
Encourage open science 
Allow reproducibility and transparency
What should you document about your research? Everything! All the data, notes, code, and materials someone else would need to reproduce your work

Consider the following questions:
How is your data gathered?
What variables did you use?
Did you use any code to clean/analyze your data?



To improve this, Benjamin D. Lee. create Ten simple rules article. <https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1006561>
In summary:
Write comments as you code
Include examples
Include a quickstart guide
Include a README file with basic information
Include a help command for command line interfaces
Version control your documentation
Fully document your application programming interface
Use automated documentation tools - The best type of documentation is documentation that writes itself!
Write error messages that provide solutions or point to your documentation
Tell people how to cite your software

Tips to Adding locally hosted code to GitHub - https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github

Tips to Creating your first repository using GitHub Desktop https://docs.github.com/en/desktop/installing-and-configuring-github-desktop/overview/creating-your-first-repository-using-github-desktop

Tips to Create a repository in Web - https://docs.github.com/en/get-started/quickstart/create-a-repo


