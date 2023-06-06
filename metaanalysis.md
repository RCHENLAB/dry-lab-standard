

## Workflow to analyze sc-RNA-seq data from public databases, like:

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













