## Annotation and cell type prediction of sc-RNA-seq

✔️ This pipeline is also available at [Github](https://github.com/powellgenomicslab/scPred)



#### scRNA- celltype prediction using scPred
- Required R packages: library(scPred);library(Seurat);library(dplyr);library(ggplot2); library(dittoSeq)
- Example run outputs: /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/old.data/test_scpred 
1) make reference data
  - If you have ref data, you do not need to do this spep1. Go to the step.2 predic cell types
  - Example of [input_scpredmake.txt](https://github.com/sangbaekim/scAtacQC/blob/master/data/input_scpredmake.txt) 
  - File format: 3 columns by tab, no header
    - 1st: scRNA seurat object(.rds) with full path and file_name
    - 2nd: prefix- sample name
    - 3rd: clssID- col name with cell info in the meta.data of input R object
  - Usage: sh run_makeRef10xRNA_scpred_multi.sh input_scpredmake.txt

2) predict cell type
  - Example of [input_scpredpredict.txt](https://github.com/sangbaekim/scAtacQC/blob/master/data/input_scpredpredict.txt)      
  - File format: 3 columns by tab, no header
    - 1st: query scRNA seurat object(.rds) with full path and file_name
    - 2nd: prefix-sample name
    - 3rd: scATAC seurat objec(reference_data;.rds) with full path and file_name
- Usage: sh run_predict10xRNA_scpred_multi.sh input_scpredpredict.txt


🔙 [Summary list of pipelines](https://github.com/RCHENLAB/dry-lab-standard/wiki)
