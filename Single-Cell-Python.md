## Single-Cell Analysis in Python


🖥️ This tutorial is based on Scanpy https://scanpy.readthedocs.io/en/stable/tutorials.html

1) Data acquisition

We can search for public data on https://singlecell.broadinstitute.org/single_cell or https://www.ncbi.nlm.nih.gov/geo/

3) Download - prefetch.sh

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

5) fasterqdump - sra2fasterq.sh
6) Cell ranger counts - runcellranger1.sh
7) Quality control - cellqc
8) Cell annotation - 
9) Downstream analysis - pathways and DEGs
10) How to generate networks with these results









For more info, please visit the followed documents:

 ✔️ [Pre-processing and clustering](https://scanpy-tutorials.readthedocs.io/en/latest/pbmc3k.html)
       
 ✔️ [Visualization](https://scanpy.readthedocs.io/en/latest/tutorials.html#visualization)
       
 ✔️ [Trajectory inference](https://scanpy-tutorials.readthedocs.io/en/latest/paga-paul15.html)
       
 ✔️ [Integration of  datasets](https://scanpy-tutorials.readthedocs.io/en/latest/integrating-data-using-ingest.html)
       
 ✔️ [Spatial data](https://scanpy-tutorials.readthedocs.io/en/latest/spatial/basic-analysis.html)
       
 ✔️ [Other tutorials](https://scanpy.readthedocs.io/en/latest/tutorials.html#conversion-anndata-singlecellexperiment-and-seurat-objects)


🔙 [Summary list of pipelines](https://github.com/RCHENLAB/dry-lab-standard/wiki)
