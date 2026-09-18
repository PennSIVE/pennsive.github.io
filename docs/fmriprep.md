---
author: "Elizabeth Horwath"
date: 2026-04-16
---

# fMRIPrep

[fMRIPrep](https://fmriprep.org/en/stable/) was developed by Russ Poldrack and his team at Stanford University. It is an open-source functional magnetic resonance imaging (fMRI) pre-processing pipeline. This page offers a brief introduction to the software with links to useful resources.

This application was developed to perform minimal, standardized pre-processing steps with a combination of tools from state-of-the-art software packages (e.g., FSL, ANTs, FreeSurfer, and AFNI). Default pre-processing consists of motion correction, field unwarping, normalization, bias field correction, and brain extraction.

<br>

### Usage
fMRIPrep requires at least 2 inputs: a T1w image and a BOLD series. Input data must be in BIDS format. Please see the [BIDS page](pennsive_bids.md) for more details.

fMRIPrep can be used and installed through 2 methods: a containerized image (Docker or Singularity/Apptainer) and Manually Prepared Environment (Python 3.10+). A **containerized method is strongly recommended** as it minimizes the need for the tedious installation of it's many dependencies. An Apptainer is available for those with access to the takim2 host of the PMACS LPC at `/project/singularity_images/fmriprep_latest.simg`. Below is a brief example using this image:

``` sh
apptainer run --cleanenv \
    # bind project directory \
    -B <project-directory>:<project-directory> \
    # bind input directory \
    -B <input-directory>:<input-directory> \
    # bind output directory \
    -B <output-directory>:<output-directory> \
    # bind working directory \
    -B <working-directory>:<working-directory> \
    # bind scratch directory \
    -B <scratch-directory>:/scratch \
    # fMRIPrep Apptainer image \
    /project/singularity_images/fmriprep_latest.simg \
    # define input and output directories \
    <input-directory> <output-directory> \
    # process individual participant(s) \
    participant \
    # select participant(s) to process \
    --participant-label sub-<subject-ID> \
    # max threads used across workflow \
    --nthreads <max-threads> \
    # max threads available for a single process \
    --omp-nthreads <max-threads-per-process> \
    # memory limit in megabytes \
    --mem-mb <memory-limit> \
    # reduce memory usage \
    --low-mem \
    # FreeSurfer license \
    --fs-license-file <project-directory>/license.txt \
    # specify working directory \
    -w <working-directory>
```

More details and other optional arguments can be found [here](https://fmriprep.org/en/stable/usage.html).

Note that a FreeSurfer license is required for use and defined with `--fs-license-file`. You can register for free [here](https://surfer.nmr.mgh.harvard.edu/fswiki/License).

<br>

### Output
After an fMRIPrep run, an html report for each subject is created for visual inspection and quality control. Pre-processed data is saved in `<output-directory>/sub-<subject-ID>`, which can be used for further processing or subsequent analyses.

<br>

### Resources
For other useful resources and tutorials, I strongly recommend checking out [Andy's Brain Book](https://andysbrainbook.readthedocs.io/en/latest/OpenScience/OS/fMRIPrep_Demo.html)!
[Neurostars](https://neurostars.org/) is an open-access question and answer forum with a large knowledge base.

