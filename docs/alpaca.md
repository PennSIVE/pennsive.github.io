---
author: "Elizabeth Horwath"
date: 2026-08-27
---

# ALPaCA

The [ALPaCA pipeline](https://github.com/PennSIVE/PennSIVE_neuro_pip/tree/main/pipelines/alpaca) (Automated Lesion, PRL, and CVS Analysis) applies an deep learning method for white matter lesion, PRL, and CVS segmentation, developed by Dr. Fengling Hu. It provides processed T1-weighted, T2-FLAIR, T2star-magnitude, and T2star-phase images, as well lesion, CVS, and PRL masks and lesion-level probabilities.

## Usage

This pipeline contains two stages: 1) Estimation: preprocesses images and calculates lesion-level lesion, CVS, and PRL probabilities, and 2) Consolidation: consolidates all participants' results.

This pipeline can only be run with a container. Singularity/Apptainer can be used on a cluster or Docker can be used locally. This pipeline can be run in individual or batch mode, meaning you can specify a certain subject and session or run the pipeline for all subjects in the folder, respectively. Please pull the container below corresponding to your system requirements:

    AMD, CPU: russellshinohara/pennsive_amd64_cputorch:v1.2
    AMD, GPU: russellshinohara/pennsive_amd64_gputorch:v1.2
    ARM, CPU: russellshinohara/pennsive_arm64_cputorch:v1.2


These examples will run the pipeline in batch mode on with Singularity/Apptainer. To run individually or with docker, set `--mode individual`, or `-c docker`, respectively. Only Step 1 has the option of individual or batch; Step 2 will always run in batch mode. 

<br>

### <u>Step 1. Estimation</u>
This step processes raw T1, T2-FLAIR, T2star-magnitude, and T2star-phase images images to prepare for CVS probability calculation. By default, it runs bias correction, registration to EPI space, WhiteStripe normalization, MIMoSA, splitting confluent lesions, and a 3D patch convolutional neural network (CNN) to compute lesion, PRL, and CVS probabilities. Skullstripping can be turned on if input images contain non-brain tissue.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">-t or \--t1</span>: T1 sequence name<br>
<span style="font-family:menlo; color:black; font-size:16px;">-f or \--flair</span>: FLAIR sequence name<br>
<span style="font-family:menlo; color:black; font-size:16px;">-ema or \--epimag</span>: EPI magnitude image name<br>
<span style="font-family:menlo; color:black; font-size:16px;">-eph or \--epiphase</span>: unwrapped EPI phase image name<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity or docker<br>
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
<span style="font-family:menlo; color:black; font-size:16px;">\--step</span>: step of pipeline - estimation, consolidation. Default is estimation<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is batch<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockermem</span>: memory and swap allocated to docker image (optional if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--hdbetpath</span>: path to HD-BET binary (default: /opt/fsl-6.0.7.19/bin/hd-bet)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/alpaca/alpaca.sh -m /path/to/data -t "*_T1w.nii.gz" -f "*_FLAIR.nii.gz" -ema "*_part-mag_T2star.nii.gz" -eph "*_part-phase_T2star_UNWRAPPED.nii.gz" -s TRUE -c singularity --toolpath /path/to/PennSIVE_neuro_pip


```
<br>

### <u>Step 2. Consolidation</u>
This step consolidates the lesion, CVS, and PRL probability results for all participants and sessions.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--step</span>: step of pipeline - estimation, consolidation. Default is estimation. This step is consolidation<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity or docker<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/alpaca/alpaca.sh -m /path/to/data --step consolidation -c singularity --toolpath /path/to/PennSIVE_neuro_pip
```