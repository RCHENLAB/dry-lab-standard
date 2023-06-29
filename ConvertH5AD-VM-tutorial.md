# Instructions on how to prepare a .h5ad file for an atlas

### 1. Concat the .h5ad files into one file
Make a new program called scrnascanpyconcat2h5ad.sh. Below is a copy of the code that you can use. 

```
#!/usr/bin/env bash
# vim: set noexpandtab tabstop=2:

source trapdebug
outdir=$(mrrdir.sh)

function cmd {
local f=$1
local bname=$(basename "$f" .txt.gz)

if fileexists.sh "$f"
then
	slurmtaco.sh -n d01 -t 7 -m 40G -- scrnascanpyconcat2h5ad.sh -d "$outdir" -b "$bname" -t 7 -- "$f"
fi
}

source env_parallel.bash
env_parallel cmd ::: ../metadata/HumanRetinaAtlas_AMD.txt.gz
```
Make sure to reference the proper text file for annotation. If you want to use the human retinal atleaas as annotation, please see this google doc for the code: 
https://docs.google.com/document/d/1TVm_TV8gJLEZetxBLxLztpzzawwFksPxiHtHWm3eYRQ/edit?usp=sharing 

### 2. Extract the raw counts from the concatenated .h5ad file

Into the taco server, we input
``` slurmtaco.sh -t 2 -m 20G -- scrnah5ad2clean.sh -r -d "$outdir" -b "$bname" -v -- "$f" ```
In order to filter out zerocount genes, we input
```scrnah5adfiltergenescellsbycounts.sh -d "$outdir" -b "$outname" -c 1 -- "$f" ```


### 3. Split the scanpy object by column
Make a new file called scrnah5adfiles2scviwkfl.sh

You will need to install this: 
```pip install git+https://bitbucket.org/lijinbio/jlscRNAworkflowpub```

Code for the scrna scvi workflow file: 

```
#!/usr/bin/env bash
# vim: set noexpandtab tabstop=2:

source trapdebug
indir=$(mrrdir.sh ..)
outdir=$(mrrdir.sh)
# slurmtaco.sh -n g01 -t 4 -m 80G -- scrnah5adfiles2scviwkfl -d "$outdir" -e scvi_gpu -t 4 -n -- "$indir"/*.h5ad
slurmtaco.sh -n g01 -t 4 -m 80G -- scrnah5adfiles2scviwkfl -d "$outdir" -e scvi_gpu -t 4 -c config.yaml -- "$indir"/*.h5ad
```

Also make another config file, which is referenced in the previous workflow file
Code for the config.yaml file
```
$ cat config.yaml 
scrnah5adsubsetbyvaluecounts:
  label: sampleid
  ncell: 10
scrnascvih5ad:
  condaenv: **name your conda env here**
  batchkey: sampleid
  nlayer: 2
  nlatent: 30
  ntop: 10000
  flavor: seurat
  seed: 12345
  epoch: null
  gpu: null
  normcounts: false
scrnascanpycombinerawcountsscvi:
  obs:
  - _scvi_batch
  - _scvi_labels
  - _scvi_local_l_mean
  - _scvi_local_l_var
  invert: true
scrnah5ad2normscale:
  scale: false
scrnah5adumapby:
  width: 5
  height: 5
  label:
  - DF.classifications
  - accession
  - age
  - donor
  - gender
  - disease
  - nCount_RNA
  - nFeature_RNA
  - pANN
  - percent.mt
  - leiden
```

### 4. Leiden clustering
Code for enumerating various resolutions for leiden clustering

```
#!/usr/bin/env bash
# vim: set noexpandtab tabstop=2:

source trapdebug
indir=$(mrrdir.sh ../scrnah5ad2clean)
files=("$indir"/*.h5ad)
outdir=$(mrrdir.sh)

function cmd {
local f=$1
local bname=$(basename "$f" .h5ad)

if fileexists.sh "$f"
then
	slurmtaco.sh -D 706695 -t 2 -m 10G -- scrnah5adscvi2leidensbyresolution -e scvi_gpu -d "$outdir" -b "${bname}_$(strjoin.sh -s _ -- "${@:2}")" $(basharr2cmdopts.sh -o -r -- "${@:2}") -- "$f"
fi
}

source env_parallel.bash
env_parallel cmd ::: ${files[@]} ::: 0.0{1..9} 0.{1..9} 1.{1..9} {1..2}
```









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

