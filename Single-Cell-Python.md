## Single-Cell Analysis in Python


🖥️ This tutorial is based on Scanpy https://scanpy.readthedocs.io/en/stable/tutorials.html



1) Data acquisition

We can search for public data on https://singlecell.broadinstitute.org/single_cell or https://www.ncbi.nlm.nih.gov/geo/




2) Download - prefetch.sh

```

prefetch SRR16959387 --max-size 420000000000

```

or 


```

vi prefetch.sh

function cmd {

local id=$1

prefetch.sh "$id"

}


source env_parallel.bash

env_parallel cmd ::: $(cut -f 2 < ../../fnameinfo.txt | tail -n +2)

```



3) fasterqdump - sra2fasterq.sh

```
vi sra2fasterq.sh

#!/usr/bin/env bash




outdir=$(mrrdir.sh)




function cmd {

local id=$1

slurmtaco.sh -n g01 -m 10G -- sra2fasterq.sh --include-technical -d "$outdir" -- "$id"

}



source env_parallel.bash

env_parallel cmd ::: $(cut -f 2 < ../../fnameinfo.txt | tail -n +2)

```


4) Cell ranger counts - runcellranger1.sh

```
vi runcellranger1.sh

#!/usr/bin/bash




#To use absolute path for fastq files, if the folder was more than 1 sample, create a list as follow:

cellranger count --id=WangK_GSM4708669 \

                 --transcriptome=/storage/chen/home/u234989/run_cellranger_count/refdata-gex-mm10-2020-A \

                 --fastqs=/storage/chen/home/u234989/mrrdir/WangK/rawdata/sra2fasterq \

                 --sample=SRR12375962,SRR12375963,SRR12375964,SRR12375965 \

                 --localcores=16 \

                 --localmem=64 \

                 --expect-cells=10000
               
```



5) Quality control - cellqc - [Cell qc pipeline](https://github.com/RCHENLAB/dry-lab-standard/blob/main/sc-RNA-seqqualitycontrol.md)

6) Integration and cell annotation

https://docs.google.com/document/d/1TVm_TV8gJLEZetxBLxLztpzzawwFksPxiHtHWm3eYRQ/edit?usp=sharing

 
7) Downstream analysis - pathways and DEGs










For more info, please visit the followed documents:

 ✔️ [Pre-processing and clustering](https://scanpy-tutorials.readthedocs.io/en/latest/pbmc3k.html)
       
 ✔️ [Visualization](https://scanpy.readthedocs.io/en/latest/tutorials.html#visualization)
       
 ✔️ [Trajectory inference](https://scanpy-tutorials.readthedocs.io/en/latest/paga-paul15.html)
       
 ✔️ [Integration of  datasets](https://scanpy-tutorials.readthedocs.io/en/latest/integrating-data-using-ingest.html)
       
 ✔️ [Spatial data](https://scanpy-tutorials.readthedocs.io/en/latest/spatial/basic-analysis.html)
       
 ✔️ [Other tutorials](https://scanpy.readthedocs.io/en/latest/tutorials.html#conversion-anndata-singlecellexperiment-and-seurat-objects)


🔙 [Summary list of pipelines](https://github.com/RCHENLAB/dry-lab-standard/wiki)
