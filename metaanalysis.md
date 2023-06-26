# Meta Analysis Tutorial
## This tutorial outlines the workflow for analysis of sc-RNA-seq data from public databases. These are the resources used in this tutorial: 

* Folder with the codes /storage/chen/home/u247700/summertutorialmeta/meta
* Geo <https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE171337>

Before starting the meta analysis tutorial, in order to make all the steps more faster, download and parallel  - https://anaconda.org/conda-forge/parallel

``` conda install -c conda-forge parallel ```

Install SRA toolkit as well: 

``` conda install -c bioconda sra-tools=3.0.0 ```

## Summary of RNAseq- from 10x genomics

scRNAseq (10x Genomics) libraries were generated from FAC-sorted WT and cRE1dup mouse embryonic cranial motor neurons spanning r3 (trigeminal motor neurons) to r7 (hypoglossal and glossopharyngeal motor neurons) marked by the motor neuron-specific Isl1MN-GFP transgenic reporter. GFP negative cells from the surrounding tissue were included for transcriptional and anatomic context. Cells were collected at E9.5 (n= 17 WT, 18 cRE1dup/+ embryos from 2 litters), E10.5 (n=12 WT, 7 cRE1dup/+ embryos from 2 litters), E11.5 (n=5 WT, 2 cRE1dup/+ embryos from 1 litter), and E12.5 (n=3 WT, 5 cRE1dup/+ embryos from 1 litter). Single cell cDNA libraries were generated using a Chromium Next GEM Single Cell 3? Library Kit v3.1. Libraries from littermate WT and cRE1dup/+ preparations were sequenced on common flow cells on a NextSeq 500 DNA sequencer using a NextSeq 500/550 High Output v2.5 kit (75 cycles) (Illumina). Raw sequence data was processed, filtered and aligned on the Cell Ranger analytic pipeline (10x Genomics) to generate FASTQ and filtered feature bc matrix files for deposit.

# Steps: 

## 1. Collect raw data

To see the raw data about this dataset, go to "SRA Run Selector" which is usually located after the supplementary information.

<https://www.ncbi.nlm.nih.gov/Traces/study/?acc=PRJNA719148&o=acc_s%3Aa>

In this example, we found 8 Items

_____________________________________________________________________________________________________________________________________________
| Run         | BioSample     | Age     | Bases   | Bytes    | Experiment  | Genotype  | GEO_Accession | create_date         | Sample Name  |
| SRR23098564 | SAMN32768056  | Day 9.5 | 25.65 G | 10.03 Gb | SRX19050851 | wildtype  | GSM6938450    | 2023-01-1714:09:00Z | GSM6938450   |
| SRR23098565 | SAMN32768059  | Day 9.5 | 27.59 G | 10.69 Gb | SRX19050852 | mutant    | GSM6938451    | 2023-01-1714:47:00Z | GSM6938451   |
| SRR23098566 | SAMN32768061  | Day 10.5| 25.99 G | 10.15 Gb | SRX19050853 | wildtype  | GSM6938452    | 2023-01-1714:58:00Z | GSM6938452   |
| SRR23098567 | SAMN32768063  | Day 10.5| 25.99 G | 10.12 Gb | SRX19050854 | mutant    | GSM6938453    | 2023-01-1714:38:00Z | GSM6938453   |
| SRR23098568 | SAMN32768064  | Day 11.5| 20.18 G | 8.11 Gb  | SRX19050855 | wildtype  | GSM6938454    | 2023-01-1714:12:00Z | GSM6938454   |
| SRR23098569 | SAMN32768066  | Day 11.5| 20.42 G | 8.16 Gb  | SRX19050856 | mutant    | GSM6938455    | 2023-01-1713:51:00Z | GSM6938455   |
| SRR23098570 | SAMN32768067  | Day 12.5| 19.28 G | 7.69 Gb  | SRX19050857 | wildtype  | GSM6938456    | 2023-01-1714:04:00Z | GSM6938456   |
| SRR23098571 | SAMN32768068  | Day 12.5| 17.57 G | 7.07 Gb  |SRX19050858  | mutant    | GSM6938457    | 2023-01-1713:40:00Z | GSM6938457   |
______________________________________________________________________________________________________________________________________________


## 2. Download and prepare files 
To download each file from the NCBI website, you will need to run a prefetch code. 

Into the command line, 

``` vi prefetch.sh ```

Into this file, copy paste the following code. 

This code is the prefetch code, it downloads the content from the available dataset so that the data is ready when we call it later. 

```

#!/usr/bin/env bash

function cmd {
local id=$1
prefetch.sh "$id"
}

source env_parallel.bash
# env_parallel cmd ::: $(cut -f 2 < path/to/directory | tail -n +2)
env_parallel cmd ::: $(cut -f 2 < path/to/directory/fnameinfo.txt | tail -n +2)

```

Next, create the fnameinfo.txt document. This outlines the samples that we will be running the CELLQC/CELLRANGER tests on. 
Into the command line, type 
``` vi fnameinfo.txt ```
Once in the text document, copy paste the sample IDs and the running IDs. 
```

sampleid	run
SAMN32768061	SRR23098566
SAMN32768063	SRR23098567

```

## 3. Running the prefetch code
IMPORTANT: MAKE SURE YOU ARE NOT ON THE HEADNODE. Choose a different node: sruntaco.sh(Node)

To run prefetch.sh code (Should take 40-60 min):

```
slurmtaco.sh -p short -t 2 -m 20G -n mhgcp-d02 -- ./prefetch.sh --max-size 420000000000

# -- max size: file size - set big as possible!
# -- Instead of d02: Insert your node.
# -- If 20G is not enough, feel free to change. 
```
## 4. Prepare the .fastq files
After prefetch, prepare the .fastq files with sra2fasterq.sh - fasterqdump functions
Into the command line, 
``` vi sra2fasterq.sh ```
Copy the code below and paste into the fastq vi file. 
Make sure to change the outdirectory (outdir) to the folder you want the results to be taken to. 
Change the second location to the directory of the fnameinfo.txt file. 

```
#!/usr/bin/env bash

outdir="/storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/trigeminal_data/yangetal2022/data/"

function cmd {
local id=$1
slurmtaco.sh -n g00 -m 10G -- sra2fasterq.sh  --split-files --include-technical  -d "$outdir" -- "$id"
}

source env_parallel.bash
env_parallel cmd ::: $(cut -f 2 < /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/trigeminal_data/yangetal2022/data/fnameinfo.txt | tail -n +2)

```


## 5. Run fasterqdump
Copy paste this line of code into the command line. Just like above, make sure to change the node to the one you are on. Do not use the headnode! 

```
slurmtaco.sh -p short -t 2 -m 20G -n mhgcp-d02 -- ./sra2fasterq.sh

```

## 6. Preparation for cell ranger analysis

The .fastq files should look like this in the output directory: 

SRR23098566_1.fastq

SRR23098566_2.fastq

SRR23098566_3.fastq

Type in ``` ls-l ``` into the command line to see the file sizes. The lowest filesize corresponds to the index, the two highest ones correspond to read 1 and read 2. You can delete the index file. 


Convert to fastq.gz using:  
``` gzip [fastq filename] ```

Just a note: Converting a fastq file to a fastq.gz file is a way of compressing it, just like creating a zip file in windows. 

Make sure your files look like this: 

SRR23098566_S1_L001_R1_001.fastq.gz (and the R2 version)

SRR23098566_S1_L001_R2_001.fastq.gz



## 7. Running cell ranger

Make sure your cellranger config is ready - https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/installation

It will ask you a couple of questions if this is your first time setting it up. 

Open up your runcellranger.sh file- if you do not have this file, 

``` vi runcellranger.sh ```

Edit runcellranger.sh - we need to make sure we are using the correct reference genome!

Use this code as an example. Name the job ID with a specific name related to what we are running. 
Make sure to use the correct sample IDs. 
Make sure to create the correct path for the Transcriptome and the FASTQ files. 
```
#!/usr/bin/bash

#To use absolute path for fastq files, if the folder was more than 1 sample, create a list as follow:
JOBID="zhangetal2022"
SAMPLE_IDS="SRR23098566,SRR23098567"
TRANSCRIPTOME="/storage/chen/home/u247700/cellranger/refdata-gex-mm10-2020-A"
FASTQS="/storage/chen/home/u247700/DRG_atlas/zhangetal2022"

cellranger count --id=$JOBID \
                 --transcriptome=$TRANSCRIPTOME \
                 --fastqs=$FASTQS \
                 --sample=$SAMPLE_IDS \
                 --localcores=16 \
                 --localmem=64 \
                 --expect-cells=10000


echo "All processes for all samples were done !!"

```
Once you are done editing your runcellranger.sh file, input the run command into the command line (change your node/ GB etc.): 


```
slurmtaco.sh -p short -t 5 -m 20G -n mhgcp-d01 -- ./runcellranger.sh
```



✔️ Thats all! After this step, make the Quality Control pipeline! 


[sc-RNA-seq quality control](https://github.com/RCHENLAB/dry-lab-standard/blob/main/sc-RNA-seqqualitycontrol.md)


After Quality control - go to Rstudio server and install Seurat

**sc-RNA-seq analysis in Seurat**

1) https://satijalab.org/seurat/articles/pbmc3k_tutorial.html
2) https://satijalab.org/seurat/articles/visualization_vignette.html
3) https://satijalab.org/seurat/articles/de_vignette.html
4) https://satijalab.org/seurat/articles/essential_commands.html
5) https://satijalab.org/seurat/articles/merge_vignette.html
6) https://satijalab.org/seurat/articles/hashing_vignette.html






