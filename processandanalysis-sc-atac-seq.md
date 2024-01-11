## Processing and analyzing single-cell ATAC-seq data

✔️ This pipeline is also available at [Github](https://github.com/GreenleafLab/ArchR)


🖥️ [A Brief Tutorial of ArchR](https://www.archrproject.com/articles/Articles/tutorial.html)




# scAtacQC

## How to do QC for scATAC-seq data

- Github: git@github.com:sangbaekim/scAtacQC.git 
- Src path in taco: /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/
- test data in taco: ./scAtacQC/data/

### 1) Required R packages: 
  - library(Signac)
  - library(biovizBase)
  - library(Rsamtools)
    To install Rsamtools properly for specific R version:
    if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
    BiocManager::install("Rsamtools")

  - library(EnsDb.Hsapiens.v86) - need to be installed at conda install -c "bioconda/label/gcc7" bioconductor-ensdb.hsapiens.v86
  - In case of mice dataset - EnsDb.Mmusculus.v79 - conda install -c "bioconda/label/gcc7" bioconductor-ensdb.mmusculus.v79
  - library(Seurat)
  - library(ggplot2)
  - library(GenomeInfoDb)
  - library(cowplot)
  - library(rmarkdown)
  - library(knitr)
  - source('R_functions_scATAC.R') - not need to install

### 2.1) Create input_list file for QC: 
  - Example of input_list file: [input_has.txt](https://github.com/sangbaekim/scAtacQC/blob/master/data/input_has.txt) 
  - File format: 2 columns by tab, no header
    - 1st: cellranger output dir
    - 2nd: sample_ID
  - For multiple samples, you can add a row with the same format.
  
### 2.2) Run QC: 
  - Usage: sh run_QCscATACsignac_multi.sh input_qc.txt
  - or, use full src path: e.g. sh /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/run_QCscATACsignac_multi.sh [/path/]input_qc.txt
  - Outputs: output folder will be generated under the cellranger output dir as “out_QC_signac”.
  - In the out folder, all output files are saved.
  - Output: example of [report_file_QC.html](https://github.com/sangbaekim/scAtacQC/blob/master/data/Report_QC_coembed_ATAC_22_0688_TM.html)

### 3.1) Create input_list file for embedding scRNA and scATAC data: 
  - Example of [input_coembed.txt](https://github.com/sangbaekim/scAtacQC/blob/master/data/input_coembed.txt)
  - File format: 6 columns by tab, no header
    - 1st: input dir path(dirIn)
    - 2nd: scRNA seurat object(.rds) with full path and file_name, **it should have cell type information already**.
    - 3rd: scATAC seurat objec(QC data;.rds) with full path and file_name
    - 4th: scATAC fragment file path (only out_folder) 
    - 5th: sample name
    - 6th: celltype col_name in the metadata of scRNA 
  - For multiple samples, you can add a row with the same format.

### 3.2) Run embedding: 
  - Usage: sh run_coembeddingscRNAscATAHs_multi.sh input_coemb.txt
  - Outputs: output folder will be generated under the cellranger output dir as “out_coembedding_{sample_ID}”.
  - In the output folder, all output files are saved.
  - Output: example of [report_file_coembed.html](:https://github.com/sangbaekim/scAtacQC/blob/master/data/Report_QC_coembed_ATAC_22_0688_TM.html)

### 4) Test run with sample data
- You can do a test run with sample data in command line in the server


```
# setup src path in unix
$ git clone https://github.com/sangbaekim/scAtacQC.git
$ export PATH=/path/to/scAtacQC/:$PATH
# or in Taco,
cd /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/

# use slurm in your run
# 1) QC
sh run_QCscATACsignac_multi.sh data/input_has.txt

#=> output folder location: ./data/B2_ATAC_22_0688_TM/outs/out_QC_signac_ATAC_22_0688_TM/

# 2) Coembedding scRNA and scATAC data
sh run_coembeddingscRNAscATAHs_multi.sh data/input_coemb.txt

#=> output folder location: ./data/B2_ATAC_22_0688_TM/outs/out_coembedding_ATAC_22_0688_TM/
```

## References for QC output
### QC for scATAC data
- https://www.10xgenomics.com/resources/analysis-guides/common-considerations-for-quality-control-filters-for-single-cell-rna-seq-data

- 10x output interpretation: https://cdn.10xgenomics.com/image/upload/v1660261285/support-documents/CG000202_TechnicalNote_InterpretingCellRangerATACWebSummaryFiles_RevB.pdf
=> there is expected value column for data QC.

- QC criteria: https://www.sc-best-practices.org/chromatin_accessibility/quality_control.html

- Signac: https://stuartlab.org/signac/articles/pbmc_vignette
	- cut-off: CreateChromatinAssay: 
		  - min.cells = 10,
		  - min.features = 200 

- our cut-off parameter:

### QC parameters on our pipeline - run QC for scATAc data
-# minPRK=1000  : minimum of fragments in peaks

-# maxPRK=20000 : max of fragments in peaks

-# PctRiP=15  : Fraction of fragments in peaks

-# blr=0.05 : Ratio reads in genomic blacklist regions

-# nsr=4  : nucleosome_signal

-# tssE=2 : Transcriptional start site (TSS) enrichment score



## * Other analysis
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

3) Assign cell type with new info.
 - R script to assign cell type: 
   /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/run_assignCelltype.R
 - R script to check known HAS marker: 
   /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/check_marksersHAS6tissue_v2.R
   usage example: 
  - usage: Rscript --vanilla /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/check_marksersHAS6tissue_v2.R /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scRNA_HAS/integration/predict_Robjs_rna/out_integration_ciliary_combined/obj_integ_ciliary_combined_rs_0.1_ds_30_ciliary_combined.rds ciliary_combined


### scATAC data for downstream analysis
#### DAR analysis
- /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/run_DARscATAC_v2.R
- usage(3 parameters required):
- Rscript --vanilla run_DARscATAC_v2.R /path/atac.rds prefix setGrp(e.g.predicted.id) 

#### co-accessible networks with Signac package
- src folder:/storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC  
    - R script file: run_signac_coANetwork.R
- requirement: check R_library list in the file. 
- Usage(e.g): Rscript --vanilla  run_signac_coANetwork.R /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/data/test_signac/obj_TM_atac_wGeneActivity.rds TM_combined
  - Input parameters: signac_object with the fullpath, prefix
  - Output: 'out_" folder under the working dir.
  
#### Add annotation info into the DAR output
- R script: run_annot_atacDAR.R
- src path in Taco: /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/run_annot_atacDAR.R 
- input data for a test run: /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/data/test_signac/test_annot_DAR/input_dar.csv 
- output example: /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/data/test_signac/test_annot_DAR/out_dar_wannot_test_dar2/ 

- requirements:  
  library(GenomicRanges) 
  library(GenomicFeatures) 
  library(TxDb.Hsapiens.UCSC.hg38.knownGene) 
  library(TxDb.Mmusculus.UCSC.mm10.knownGene) 
  library(org.Mm.eg.db) 
  library(org.Hs.eg.db) 
  library(ChIPseeker) 

- 4 input parameters:  
  dirIn  <- args[1] # input dir 
  fin    <- args[2] # dar file name 
  prefix <- args[3] # data description 
  gnOption <- args[4] # genome version to use 

- test run for an example data in Taco 
  slurmtaco.sh -p gpu -t 1 -m 10G -n mhgcp-g00 -- Rscript --vanilla /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/run_annot_atacDAR.R  /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/data/test_signac/test_annot_DAR input_dar.csv test_dar2 hg38 

#### Motif analysis 
- R script for motif analysis. (/storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/run_signac_motif.R) 

- usage: Rscript --vanilla run_signac_motif.R /path/to/atac.rds prefix setGrp(e.g. predicted.id or seurat_clusters)
- test run: Rscript --vanilla /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/run_signac_motif.R /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/data/test_signac/obj_TM_atac_wGeneActivity.rds M_combined predicted.id

### R script for combining scATAC data with QC_signac_Objects:
- src: /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/run_combinedAtacSignacObjs.Rtest data: /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/data/test_merge_atac_objs
Requirement: input file(with input data info), prefix(data name)1) input file: 3 columns (sampleID, raw data path, QC_signac_object path 
- e.g.) /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/data/test_merge_atac_objs/input_atacMerged.txt2) 
- output: /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/data/test_merge_atac_objs/out_merged_atac_objs_atac_TM2/
- Run-command: 
Rscript --vanilla /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/run_combinedAtacSignacObjs.R /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/data/test_merge_atac_objs/input_atacMerged.txt TM_atac



### Future work
 - Improve downstream analysis 
 - Sequence motif enrichment analysis
 - Transcription factor footprinting analysis
 - Linking peaks to correlated genes
   * Use this codes as baseline: https://github.com/stuart-lab/signac/tree/master/R

### Known marker gene list for Human anterior segment tissue
  - [HAS markers](https://github.com/sangbaekim/scAtacQC/blob/master/data/markers_HAS_SanesPaper.xlsx):  Taco- ~/22_10x_hs_AnteriorSegment_data/marker_genes_HAS/



### References
  - Signac: https://github.com/stuart-lab/signac
  - Seurat_coembedding scRNA and scATAC data: https://satijalab.org/seurat/articles/atacseq_integration_vignette.html 



🔙 [Summary list of pipelines](https://github.com/RCHENLAB/dry-lab-standard/wiki)
