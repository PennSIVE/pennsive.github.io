---
author: "Elizabeth Horwath"
date: 2026-03-17
---

# JLF

The [JLF pipeline](https://github.com/PennSIVE/PennSIVE_neuro_pip/tree/main/pipelines/JLF) produces a high-resolution anatomical segmentation using the ANTs Joint Label Fusion algorithm. A T1-weighted image is the only input needed.

## Usage

The pipeline contains three stages: 1) Registration: registers atlas into participants' T1-weighted space, 2) antsjointfusion: segments T1 images using multi-atlas segmentation with joint label fusion, 3) Extraction: extracts ROI and lesion volumes.

This pipeline can be run with or without a container. For containerized usage, Singularity can be used on a cluster or Docker locally. This pipeline can be run in individual or batch mode, meaning you can specify a certain subject and session or run the pipeline for all subjects in the folder, respectively. 

These examples will run the pipeline in batch mode on the cluster. To run individually or locally/with a container, set `--mode individual`, or `-c local`/`-c singularity`/`-c docker`, respectively. Only Steps 1 and 2 have the option of individual or batch; Step 3 will always run in batch mode. 

<br>

### <u>Step 1. Registration</u>
This step registers the selected atlas to the subjects' native T1 space. 

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">-t or \--t1</span>: T1 sequence name<br>
<span style="font-family:menlo; color:black; font-size:16px;">-s or \--step</span>: step of pipeline - registration, antsjointfusion, extraction. Default is antsjointfusion. This step is registration<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-p or \--participant</span>: participant ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--ses</span>: session ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-n or \--num</span>: number of templates. Default is 9<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--type</span>: type of templates - WMGM, thal. Default is WMGM<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--lesion</span>: extract lesion volumes. Default is TRUE<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is batch<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/JLF/code/bash/JLF.sh -m /path/to/project --t1 "*T1w*.nii.gz" --step registration --toolpath /path/to/PennSIVE_neuro_pip
```
<br>

### <u>Step 2. antsJointFusion</u>
This step runs the antsJointFusion function from ANTs.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">-t or \--t1</span>: T1 sequence name<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-p or \--participant</span>: participant ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--ses</span>: session ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-n or \--num</span>: number of templates. Default is 9<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--type</span>: type of templates - WMGM, thal. Default is WMGM<br>
<span style="font-family:menlo; color:black; font-size:16px;">-s or \--step</span>: step of pipeline - registration, antsjointfusion, extraction. Default is antsjointfusion<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is batch<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/JLF/code/bash/JLF.sh -m /path/to/project --t1 "*T1w*.nii.gz" --toolpath /path/to/PennSIVE_neuro_pip
```
<br>

### <u>Step 3. Extraction</u>
This step extracts ROI and lesion volumes for all participants and sessions.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">-s or \--step</span>: step of pipeline - registration, antsjointfusion, extraction. Default is antsjointfusion. This step is extraction<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-p or \--participant</span>: participant ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--ses</span>: session ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--type</span>: type of templates - WMGM, thal. Default is WMGM<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--lesion</span>: extract lesion volumes. Default is TRUE<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is batch<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/JLF/code/bash/JLF.sh -m /path/to/project --step extraction --toolpath /path/to/PennSIVE_neuro_pip
```