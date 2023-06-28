# Instructions on how to prepare a .h5ad file for an atlas

### 1. Convert a .h5ad file to a seurat object

Make sure you have the following libraries installed: 

```library(Seurat)```

```library(SeuratDisk)```

```library(anndata)```

```library(reticulate)```

In order to do this, we need to submit a job to the Taco server. 
```slurmtaco.sh -p gpu -t 5 -m 20G -n mhgcp-g00 Rscript -- /storage/chen/data_share_folder/22_10x_hs_AnteriorSegment_data/scAtacQC/run_transform_h5seurat_to_seuratobj.R  /storage/YOUR INPUT DIRECTORY [NAME YOU WANT TO GIVE TO NEW FILE  ```
Change your node and your gig size if needed. 


*** Just a note: if you want to ever convert a seurat object back into a .h5ad file, you can use this command: ***

```Convert("D:/PATH_TO_INPUT_FILE.h5ad", dest = "h5seurat", overwrite = TRUE)```
``` NewFile <- LoadH5Seurat("Pathtotheconvertedfile.h5seurat")```


### 2. 





# Instructions to request and set up a Virtual Machine (VM) with Baylor

### 1. Submit an application delivery services request for a VM

[Use this link](https://bcm.service-now.com/com.glideapp.servicecatalog_cat_item_view.do?v=1&sysparm_id=2ad0c40c2bc9310020bc2f9319da1572&sysparm_link_parent=e15706fc0a0a0aa7007fc21e1ab70c2f&sysparm_catalog=e0d08b13c3330100c8b837659bba8fb4&sysparm_catalog_view=catalog_default&sysparm_view=catalog_default)

Use this as a sample letter to send in the application: 

```
Hi IT,

 

Our lab has developed a web app for genomic data resource. We are applying to setup a VM and test the web app internally and eventually publish to the multi-omic community outside BCM. I am summarizing the specifications for the VM, and the public facing domain will be below.

 

https://XXX.research.bcm.edu

 

Below are the resources might be needed for the VM.

 

1. OS: Redhat Linux 8
2. Core: 8-core
3. RAM: 32GB RAM
4. Hard drive: 200GB Storage
5. Admin users:
- Full name: BCM_ECA@bcm.edu
- Holder, Christopher Michael: Christopher.Holder@bcm.edu
6. Ports to open: port 80 and port 443.
For public admin, we might redirect HTTP to HTTPs.
7. We don't need to preinstall any database. We will set up web service by ourselves using cellxgene (https://github.com/chanzuckerberg/cellxgene) after our accounts have been granted admin access.

 

Thank you so much for your help.

```

##### Make sure to fill out the information. 
1.1 Requestor: Your Full Name

1.2 Sponsor/Approver: Dr. Rui Chen

1.3 Detailed description of the request. (See above)

1.4 Technical contact: Full name

1.5 Date required: (Put a date like in a month)

### 2. Submit an ADS request for a public domain

In order to request a new web service, fill in the below table. 

[https://bcm.service-now.com/bcmsp?id=bcm_cat_item&sys_id=2ad0c40c2bc9310020bc2f9319da1572](https://bcm.service-now.com/bcmsp?id=bcm_cat_item&sys_id=2ad0c40c2bc9310020bc2f9319da1572)

