

## Workflow to analyze sc-RNA-seq data from public databases, like:

* Folder with the codes /storage/chen/home/u247700/summertutorialmeta/meta
* Geo <https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE171337>

**Overall design**

scRNAseq (10x Genomics) libraries were generated from FAC-sorted WT and cRE1dup mouse embryonic cranial motor neurons spanning r3 (trigeminal motor neurons) to r7 (hypoglossal and glossopharyngeal motor neurons) marked by the motor neuron-specific Isl1MN-GFP transgenic reporter. GFP negative cells from the surrounding tissue were included for transcriptional and anatomic context. Cells were collected at E9.5 (n= 17 WT, 18 cRE1dup/+ embryos from 2 litters), E10.5 (n=12 WT, 7 cRE1dup/+ embryos from 2 litters), E11.5 (n=5 WT, 2 cRE1dup/+ embryos from 1 litter), and E12.5 (n=3 WT, 5 cRE1dup/+ embryos from 1 litter). Single cell cDNA libraries were generated using a Chromium Next GEM Single Cell 3? Library Kit v3.1. Libraries from littermate WT and cRE1dup/+ preparations were sequenced on common flow cells on a NextSeq 500 DNA sequencer using a NextSeq 500/550 High Output v2.5 kit (75 cycles) (Illumina). Raw sequence data was processed, filtered and aligned on the Cell Ranger analytic pipeline (10x Genomics) to generate FASTQ and filtered feature bc matrix files for deposit.

**Collect raw data**

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


**Env parallel**

To make all the steps more faster, download and parallel  - https://anaconda.org/conda-forge/parallel

**Install sra toolkit**

```
conda install -c bioconda sra-tools=3.0.0
```

**Download and prepare files**
To download this files: prefetch.sh code

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

Structure of fnameinfo - File with the info about the SRR, as we see in the table (but we can modified as well)

```
vi fnameinfo.txt

sampleid	run
SAMN32768061	SRR23098566
SAMN32768063	SRR23098567

```

To run prefetch.sh code:

```
slurmtaco.sh -p short -t 2 -m 10G -n mhgcp-d02 -- ./prefetch.sh --max-size 420000000000

# -- max size: file size - set big as possible!
```

After prefetch, prepare the .fastq files with sra2fasterq.sh - fasterqdump functions


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


**Run fasterqdump**

```
slurmtaco.sh -p short -t 2 -m 10G -n mhgcp-d02 -- ./sra2fasterq.sh

```

**Before run cell ranger analysis**

Make sure the .fastq files have the proper structure name, as follows:


SRR18113700_1.fastq is Read 1
SRR18113700_2.fastq is Read 2
SRR18113700_3.fastq is Index 1
SRR18113700_4.fastq is Index 2

Convert to:
SRR18113700_S1_L001_R1_001.fastq.gz (and the R2 version)

SRR18113700_S1_L001_R1_001.fastq.gz
SRR18113700_S1_L001_R2_001.fastq.gz

**Cell ranger count**

Make sure your cellranger config is ready - https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/installation

Using runcellranger.sh - make sure we are using the correct reference genome!

Example:

```
#!/usr/bin/bash

#To use absolute path for fastq files, if the folder was more than 1 sample, create a list as follow:
JOBID="zhangetal2022"
SAMPLE_IDS="SRR21958167,SRR21958168,SRR21958169,SRR21958170,SRR21958171,SRR21958172,SRR21958173,SRR21958174"
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


```
slurmtaco.sh -p short -t 5 -m 20G -n mhgcp-d01 -- ./runcellranger.sh
```


✔️ Thats all! After this step, make the Quality Control pipeline! 


[sc-RNA-seq quality control](https://github.com/RCHENLAB/dry-lab-standard/blob/main/sc-RNA-seqqualitycontrol.md)


