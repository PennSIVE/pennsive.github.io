---
author: "Elizabeth Horwath"
date: 2026-03-17
---

# Lesion Count

The [Lesion Count pipeline](https://github.com/PennSIVE/PennSIVE_neuro_pip/tree/main/pipelines/lesion_count) provides two options to count the number of lesions present in MRI images using the "DworCount" method, developed by Dr. Jordan Dworkin and the connected components method.

## Usage

The pipeline allows for three count options: DworCount (set --method dworcount), connected components (set --method cc), or both (default; set --method both).

This pipeline contains three stages: 1) Preparation: preprocesses and prepares data for lesion counting, 2) Count: counts number of lesions using specified method, 3) Consolidation: consolidates all participants’ results into a single .csv file.

This pipeline can be run with or without a container. For containerized usage, Singularity can be used on a cluster or Docker locally. This pipeline can be run in individual or batch mode, meaning you can specify a certain subject and session or run the pipeline for all subjects in the folder, respectively. 

These examples will run the pipeline in batch mode on the cluster. To run individually or locally/with a container, set `--mode individual`, or `-c local`/`-c singularity`/`-c docker`, respectively.

<br>

### <u>Step 1. Preparation</u>
This step processes raw T1 and T2-FLAIR images to prepare for lesion counting. By default, it runs bias correction, registration to FLAIR space, WhiteStripe normalization, and MIMoSA. For the DworCount method, confluent lesions are split and labeled. Skullstripping can be turned on if input images contain non-brain tissue.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">-t or \--t1</span>: T1 sequence name<br>
<span style="font-family:menlo; color:black; font-size:16px;">-f or \--flair</span>: FLAIR sequence name<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-p or \--participant</span>: participant ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--ses</span>: session ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-n or \--n4</span>: run N4 bias correction. Default is TRUE<br>
<span style="font-family:menlo; color:black; font-size:16px;">-s or \--skullstripping</span>: run skullstripping. Default is FALSE<br>
<span style="font-family:menlo; color:black; font-size:16px;">-r or \--registration</span>: run registration. Default is TRUE<br>
<span style="font-family:menlo; color:black; font-size:16px;">-w or \--whitestripe</span>: run WhiteStripe normalization. Default is TRUE<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mimosa</span>: run MIMoSA segmentation. Default is TRUE<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--threshold</span>: threshold for generating MIMoSA mask. Default is 0.2<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--method</span>: cc, dworcount, both. Default is both<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--step</span>: step of pipeline - preparation, count, consolidation. Default is preparation<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is batch<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/lesion_count/code/bash/lesion_count.sh -m /path/to/data -t "*T1w*.nii.gz" -f "*FLAIR*.nii.gz" -s TRUE --toolpath /path/to/PennSIVE_neuro_pip 
```
<br>

### <u>Step 2. Count</u>
This step counts lesions based on connected components or DworCount method.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--step</span>: step of pipeline - preparation, count, consolidation. Default is preparation. This step is count<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-p or \--participant</span>: participant ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--ses</span>: session ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--method</span>: cc, dworcount, both. Default is both<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is batch<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/lesion_count/code/bash/lesion_count.sh -m /path/to/data --step count --toolpath /path/to/PennSIVE_neuro_pip 
```
<br>

### <u>Step 3. Consolidation</u>
This step consolidates the lesion counts for all participants and sessions.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--step</span>: step of pipeline - preparation, count, consolidation. Default is preparation. This step is consolidation<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">\--method</span>: cc, dworcount, both. Default is both<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/lesion_count/code/bash/lesion_count.sh -m /path/to/data --step consolidation --toolpath /path/to/PennSIVE_neuro_pip 
```