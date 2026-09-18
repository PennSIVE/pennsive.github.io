---
author: "Elizabeth Horwath"
date: 2026-09-01
---

# BIDS

The [PennSIVE BIDS pipeline](https://github.com/PennSIVE/PennSIVE_neuro_pip/tree/main/pipelines/bids) is based on the Brain Imaging Data Structure (BIDS) standards. For more details about the BIDS process and guidelines, please refer to the [BIDS page](bids.md). 

This pipeline provides convenient heuristic customization through an RShiny app to organize data into BIDS format. It uses heudiconv for DICOM to NIFTI conversion.

## Usage

This pipeline contains three stages: 1) Heuristic: prepares heuristic template, 2) Customization: launches RShiny app for heuristic customization, and 3) BIDS: runs DICOM to NIfTI conversion and format into BIDS structure.

This pipeline must be run through a container, either Singularity on a cluster or Docker locally. Steps 1 and 3 can be run in individual or batch mode, meaning you can specify a certain subject and session or run the pipeline for all subjects in the folder, respectively. Step 2 must be run in batch mode.

These examples will run the pipeline in batch mode on the cluster via Singularity. To run individually or locally with Docker, set `--mode individual`, or `-c docker`, respectively.

### <u>Step 1. Heuristic</u>
This step prepares the heuristic file by copying the template to a template folder, as well as each subject and session folder. These files will be edited in Step 2: Customization.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-p or \--participant</span>: participant ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--ses</span>: session ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--step</span>: step of pipeline - heuristic, customization, bids. Default is heuristic<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is individual<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker. Default is docker<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/bids/code/bash/bids_curation.sh -m /path/to/project --mode batch -c singularity --toolpath /path/to/PennSIVE_neuro_pip
```
<br>

### <u>Step 2. Customization</u>
In this step, an RShiny app will launch to customize the heuristic template created in the last step. 

*This step only runs in batch mode and does not need a container specification. If you are unable to connect to the app from your terminal, try running this step in VSCode.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--step</span>: step of pipeline - heuristic, customization, bids. Default is heuristic. This step is customization<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/bids/code/bash/bids_curation.sh -m /path/to/project --step customization --toolpath /path/to/PennSIVE_neuro_pip
```
<br>

**Using the app:**

The Shiny app allows you to edit the heuristic file for all subjects in the original_data folder or for each subject individually.

To begin, under Choose Python Script, load in the heuristic.py file in the template folder.

To review each subjects' DICOM info and edit the heuristic on a **subject-level basis**, the DICOM Info Review will load each subject's info by clicking Next and Previous in the DICOM Selection. Edits can be made in the Update Heuristic Script section and finalized by clicking **Update Script**.

**Group-level changes** to the heuristic can be made by edits to the Update Heuristic Script section, and when finished, clicking **Update All Scripts**. This will apply those changes to all subjects in the folder.

<br>

### <u>Step 3. BIDS</u>
This step runs DICOM to NIfTI conversion and format into BIDS structure based on the heuristic files edited in Step 2.

<br>
<u>Required flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-m or \--mainpath</span>: path to parent data folder<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--step</span>: step of pipeline - heuristic, customization, bids. Default is heuristic. This step is bids<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--toolpath</span>: path to pipeline folder<br>

<u>Other flags:</u>

<span style="font-family:menlo; color:black; font-size:16px;">-p or \--participant</span>: participant ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--ses</span>: session ID (only needed for individual mode)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--mode</span>: run pipeline individually or batch. Default is individual<br>
<span style="font-family:menlo; color:black; font-size:16px;">-c or \--container</span>: which container to use: singularity, docker. Default is docker<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--sinpath</span>: path to singularity image (only needed if using singularity container - don't need to specify if using takim cluster)<br>
<span style="font-family:menlo; color:black; font-size:16px;">\--dockerpath</span>: path to docker image (only needed if using docker container)<br>
<span style="font-family:menlo; color:black; font-size:16px;">-h or \--help</span>: show help message<br>
<br>


``` sh
bash /path/to/PennSIVE_neuro_pip/pipelines/bids/code/bash/bids_curation.sh -m /path/to/project --step bids --mode batch -c singularity --toolpath /path/to/PennSIVE_neuro_pip
```