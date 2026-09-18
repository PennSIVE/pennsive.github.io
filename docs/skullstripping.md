---
author: "Elizabeth Horwath"
date: 2026-03-17
---

# Skullstripping

The [skullstripping pipeline](https://github.com/PennSIVE/PennSIVE_neuro_pip/tree/main/pipelines/skullstripping) provides several options (MASS and HD-BET) to remove skull and non-brain matter from brain images.

## Usage

These examples will run the pipeline in batch mode on the cluster. To run individually or locally/with a container, set `--mode individual`, or `-c local`/`-c singularity`/`-c docker`, respectively.

This pipeline can be run with or without a container. For containerized usage, Singularity can be used on a cluster or Docker locally. This pipeline can be run in individual or batch mode, meaning you can specify a certain subject and session or run the pipeline for all subjects in the folder, respectively. 

The pipeline allows for two skullstripping options: mass (default) and hdbet. To use hdbet, set -t "hdbet".

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">-f or \--file</span>: T1 sequence name<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-p or \--participant</span>: participant ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--ses</span>: session ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-t or \--type</span>: skullstripping method - mass, hdbet. Default is mass<br>
<span style="font-family:menlo; color:black; font-size:16px;">-n or \--number</span>: number of templates. Default is 20<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is batch<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker, local, cluster. Default is cluster<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/skullstripping/code/bash/skullstripping.sh -m /path/to/project -f "*MPRAGE*.nii.gz" --toolpath /path/to/PennSIVE_neuro_pip
```