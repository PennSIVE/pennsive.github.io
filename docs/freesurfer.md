---
author: "Elizabeth Horwath"
date: 2026-03-17
---

# FreeSurfer

The [FreeSurfer pipeline](https://github.com/PennSIVE/PennSIVE_neuro_pip/tree/main/pipelines/freesurfer) integrates [FreeSurfer](https://surfer.nmr.mgh.harvard.edu/) software to provide a full processing stream for structural MRI data. It takes a T1-weighted image as the only input and generates brain ROI segmentation masks as well as brain-related statistics.

## Usage

This pipeline contains three stages: 1) Segmentation: runs the FreeSurfer recon-all command to obtain ROI segmentation masks and brain statistics, 2) Estimation: converts the brain statistics into CSV format, and 3) Consolidation: consolidates all participants' data.

This pipeline can be run with or without a container. For containerized usage, Singularity can be used on a cluster or Docker locally. This pipeline can be run in individual or batch mode, meaning you can specify a certain subject and session or run the pipeline for all subjects in the folder, respectively. 

These examples will run the pipeline in batch mode on the cluster. To run individually or locally/with a container, set `--mode individual`, or `-c local`/`-c singularity`/`-c docker`, respectively. Only Steps 1 and 2 have the option of individual or batch; Step 3 will always run in batch mode. 

<br>

### <u>Step 1. Segmentation</u>
This step runs FreeSurfer's recon-all command on a T1 image to obtain ROI segmentation masks and brain statistics.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">-n or \--name</span>: T1 sequence name<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-p or \--participant</span>: participant ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--ses</span>: session ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-s or \--step</span>: step of pipeline - segmentation, estimation, consolidation. Default is segmentation<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is batch<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/freesurfer/code/bash/freesurfer.sh -m /path/to/data -n "*T1w*.nii.gz" --toolpath /path/to/PennSIVE_neuro_pip 
```
<br>

### <u>Step 2. Estimation</u>
This step converts the brain statistics output from Step 1 and converts to CSV format.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">-s or \--step</span>: step of pipeline - segmentation, estimation, consolidation. Default is segmentation<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-p or \--participant</span>: participant ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--ses</span>: session ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--parc</span>: parcellation - aparc, aparc.a2009s. Default is aparc<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is batch<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/freesurfer/code/bash/freesurfer.sh -m /path/to/data --step estimation --toolpath /path/to/PennSIVE_neuro_pip 
```
<br>

### <u>Step 3. Consolidation</u>
This step consolidates the brain statistics for all participants and sessions.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/freesurfer/code/bash/freesurfer.sh -m /path/to/data --step consolidation --toolpath /path/to/PennSIVE_neuro_pip 
```