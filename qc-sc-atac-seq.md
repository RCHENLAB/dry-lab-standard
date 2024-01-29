## scATAC quality control

Required R packages:
library(Signac)

library(biovizBase)

library(Rsamtools) To install Rsamtools properly for specific R version: if (!require("BiocManager", quietly = TRUE)) install.packages("BiocManager") BiocManager::install("Rsamtools")

library(EnsDb.Hsapiens.v86) - need to be installed at conda install -c "bioconda/label/gcc7" bioconductor-ensdb.hsapiens.v86

In case of mice dataset - EnsDb.Mmusculus.v79 - conda install -c "bioconda/label/gcc7" bioconductor-ensdb.mmusculus.v79

library(Seurat)

library(ggplot2)

library(GenomeInfoDb)

library(cowplot)

library(rmarkdown)

library(knitr)

source('R_functions_scATAC.R') - not need to install

2.1) Create input_list file for QC:
Example of input_list file: input_has.txt
File format: 2 columns by tab, no header
1st: cellranger output dir
2nd: sample_ID
For multiple samples, you can add a row with the same format.


2.2) Run QC:
Usage: sh run_QCscATACsignac_multi.sh input_qc.txt
or, use full src path: e.g. sh /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/run_QCscATACsignac_multi.sh [/path/]input_qc.txt
```
vi input_qc.txt
/storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/data/B2_ATAC_22_0688_TM/outs/	ATAC_22_0688_TM

```


Outputs: output folder will be generated under the cellranger output dir as “out_QC_signac”.
In the out folder, all output files are saved.
Output: example of report_file_QC.html



🔙 [Summary list of pipelines](https://github.com/RCHENLAB/dry-lab-standard/wiki)
