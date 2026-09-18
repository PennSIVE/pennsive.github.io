---
author: "Elizabeth Horwath"
date: 2026-03-17
---

# Radiomic Feature

The [lesion radiomic feature extraction pipeline](https://github.com/PennSIVE/PennSIVE_neuro_pip/tree/main/pipelines/radiomic_feature) utilizes PyRadiomics to extract lesion features from T1-weighted, T2-FLAIR, and T2*-EPI images.

## Usage

The pipeline contains three stages: 1) Preprocessing: processes MRI images to prepare for radiomic feature extraction, 2) Feature Extraction: extract radiomic features using PyRadiomics package, and 3) Consolidation: consolidates all participants' lesion radiomic feature data.

This pipeline can be run with or without a container. For containerized usage, Singularity can be used on a cluster or Docker locally. This pipeline can be run in individual or batch mode, meaning you can specify a certain subject and session or run the pipeline for all subjects in the folder, respectively. 

These examples will run the pipeline in batch mode on the cluster. To run individually or locally/with a container, set `--mode individual`, or `-c local`/`-c singularity`/`-c docker`, respectively.

<br>

### <u>Step 1. Processing</u>
This step processes raw T1, T2-FLAIR, and T2*-EPI images to prepare for extraction of radiomic features. By default, it runs bias correction, registration to FLAIR space, WhiteStripe normalization, MIMoSA, CSF extraction, splitting confluent lesions, and registration to EPI space. Skullstripping can be turned on if input images contain non-brain tissue.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">-t or \--t1</span>: T1 sequence name<br>
<span style="font-family:menlo; color:black; font-size:16px;">-f or \--flair</span>: FLAIR sequence name<br>
<span style="font-family:menlo; color:black; font-size:16px;">-e or \--epi</span>: EPI sequence name<br>
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
<span style="font-family:menlo; color:black; font-size:16px;">\--csf</span>: extract CSF mask. Default is TRUE<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--step</span>: step of pipeline - processing, extraction, consolidation. Default is processing<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is batch<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/radiomic_feature/code/bash/pyradiomics.sh -m /path/to/project -t "*_T1w.nii.gz" -f "*_FLAIR.nii.gz" -e "*_T2star.nii.gz" --toolpath /path/to/PennSIVE_neuro_pip
```

<br>

### <u>Step 2. Feature Extraction</u>
This step extracts radiomic features.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--step</span>: step of pipeline - processing, extraction, consolidation. Default is preparation. This step is extraction<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-p or \--participant</span>: participant ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--ses</span>: session ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is batch<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/radiomic_feature/code/bash/pyradiomics.sh -m /path/to/project --step extraction --toolpath /path/to/PennSIVE_neuro_pip
```

<br>

### <u>Step 3. Consolidation</u>
This step consolidates the radiomic features for all participants and sessions.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--step</span>: step of pipeline - processing, extraction, consolidation. Default is preparation. This step is consolidation<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/radiomic_feature/code/bash/pyradiomics.sh -m /path/to/data --step consolidation --toolpath /path/to/PennSIVE_neuro_pip 
```