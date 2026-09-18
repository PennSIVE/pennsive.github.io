---
author: "Noah Hillman"
date: 2026-08-31
---

# R, Python, and Bash in Neuroimaging Analysis

## Overview 

In PennSIVE, we primarily use three programming languages throughout the neuroimaging analysis pipeline:

- **Bash** — useful for moving/organizing files, running command-line neuroimaging tools (FSL, FreeSurfer, ANTs, etc.), and submitting jobs on a computing cluster.
- **Python** — primarily used for image processing and machine learning 
- **R** — most commonly used programming language in PennSIVE, excels in statistical analysis but also contains wrappers for common neuroimaging processing pipelines

While the present tutorial will not be exhaustive, we hope the following resources will be a useful starting point for how to use bash, R, and Python in your own analysis workflows. 

## Bash

**Bash** (Bourne Again Shell) is a command line interpreter that translates human-readable text into commands that your computer can execute. When you open the terminal on your computer, you are typically interacting with a shell like bash. If you want to string together bash commands to improve automation, bash commands can be concatenated into a **shell script** such as `example_script_name.sh` so that commands need not be executed line by line. While neuroimaging analysis typically does not require expertise in bash, some basic proficiency is useful for tasks such as:

- Navigating and organizing project directories, since modern imaging datasets typically contain many participants, each with many files
- Running neuroimaging software installed as command-line tools (`bet`, `flirt`, `recon-all`, `antsRegistration`, etc.)
- Writing loops to batch-process many subjects
- Submitting and managing jobs on the computing cluster
- Working with software containers, which is how most modern pipelines (fMRIPrep, QSIPrep, etc.) are distributed

**Resources** 

If you are new to bash, many public resources are helpful including tutorials such as [W3 School's Bash Tutorial](https://www.w3schools.com/bash/index.php) and [Missing Semester of Your CS Education (MIT)](https://missing.csail.mit.edu/)

For PennSIVE specific uses of bash consider other tutorials in this wiki including:

- [how to login and submit batch jobs through the PennSIVE HPC cluster](wiki-jobs.md)
- [how to install and run neuroimaging preprocessing pipelines through Apptainer](containers.md)

## Python

**Python** is a general-purpose programming language that supports key components of scientific computing including data manipulation, data visualization, and statistical and machine-learning modeling. Many modern preprocessing pipelines and tools for reading/manipulating images are also Python-based. 

This tutorial points to commonly used Python packages for data science -- for further details on how to use these packages consider checking out [Python for Data Science](https://aeturrell.github.io/python4DS/) or [Python for Data Analysis](https://wesmckinney.com/book/).

#### Data Manipulation

Python requires packages for creating and manipulating data frames, of which the most popular are **numpy** and **pandas**. Most packages are hosted on PyPI, which you can directly install with the `pip` command syntax 

```python
pip install packagename
```
For more information on how to install specific versions of a package, uninstalling packages, and more, you can look at the [PyPI documentation](https://pip.pypa.io/en/stable/user_guide/). Alternatively, you can install packages via GitHub with 
```python
pip install --upgrade https://github.com/user/package
```

Once installed, **numpy** is the main package for numeric matrix operations in Python. For this reason, numpy is included as a dependency in many packages. For more information on numpy syntax, see the [documentation](https://numpy.org/doc/2.5/numpy-user.pdf). 

The **pandas** package is the primary package for data manipulation when working with data frames. This package provides utilities such as reading/writing data tables, merging data sets, and reshaping data frames between long and wide formats. Additional details on how to use pandas for data science can be found in the user guide [here](https://pandas.pydata.org/docs/user_guide/index.html).

#### Data Visualization

Python also has many libraries for visualizing data, but the two most commonly used packages are **seaborn** and **matplotlib**. Seaborn is a higher-level API that is faster, simpler to use, and has a better default aesthetic. More information about plotting with seaborn can be found in its [documentation](https://seaborn.pydata.org/tutorial.html).

Matplotlib offers a lower-level API that enables additional customization of plot elements, with the drawback that usage might not be as easy as matplotlib. For more details on matplotlib, you can find its documentation [here](https://matplotlib.org/stable/users/index.html).

#### Environments

A coding environment can be conceptualized as the software and packages (with specific version numbers) that are used when running a script. Coding environments are important for reproducible programming, as using different versions of functions may result in slightly different results. 

**Conda** is a generalized environment and package manager that works with many programming languages, including Python. Conda can be used to install packages (similar to pip) and to generate unique environments for different code you want to run or develop. For example, if you wanted to use numpy 1.4.2 for `code1.py` and numpy 1.2.1 for `code2.py`, you can create two environments (one for each version) and run each code in its corresponding environment.

Environments can also be shared with others for reproducibility by exporting the .yaml file associated with each environment.

For more documentation on the specifics of Conda syntax, you can read the Conda documentation [here](https://docs.conda.io/en/latest/).


#### Neuroimaging Analysis Packages

There are numerous python packages that are useful specifically for neuroimaging data analysis pipelines. These include:

- [NiBabel](https://nipy.org/nibabel/) — reading/writing NIfTI, GIFTI, CIFTI, and other neuroimaging file formats
- [Nilearn](https://nilearn.github.io/stable/user_guide.html) — machine learning and statistical analysis on brain images (ROI extraction, decoding, connectivity, etc.)
- [Nipype](https://miykael.github.io/nipype_tutorial/) — combining processing tools from different software packages into a single pipeline (FSL, SPM, FreeSurfer, ANTs, etc.)
- [DIPY](https://dipy.org/) — diffusion MRI analysis
- [neuromaps](https://netneurolab.github.io/neuromaps/usage.html) — tools for working with and analyzing brain maps 


## R

**R** is a statistical programming language that is particularly strong at data visualization and statistical modeling. However, many neuroimaging processing pipelines also have wrapper packages in R. 

Further details on how to perform common data analysis tasks in R can be found in [R for Data Science](https://r4ds.hadley.nz).

#### Data Manipulation

While base R contains functionality for various data wrangling and visualizations taks, installing custom packages such as `tidyverse` can enhance common computing workflows. Packages in R are hosted on CRAN, which you can directly install with a command such as 

```R
install.packages("tidyverse")
```
Alternatively, you can install packages via GitHub with 

```R
# Install remotes (only needed one time)
install.packages("remotes")
# Install a package from GitHub
remotes::install_github("username/repository")
```

`tidyverse` is an agglomeration of several R packages that provides a unified framework for numerous common data manipulation operations such as adding columns to prexisting data sets, joining data frames, and data plotting. Additional details on the tidyverse can be found in its documentation [here](https://tidyverse.org). 

#### Neuroimaging Analysis R Packages
Similar to Python, there are some packages that have become widely used for neuroimaging analysis in R such as:

- [lme4](chrome-extension://efaidnbmnnnibpcajpcglclefindmkaj/https://cran.r-project.org/web/packages/lme4/vignettes/lmer.pdf) — mixed-effects models for when you have repeated measures per participant 
- [mgcv](chrome-extension://efaidnbmnnnibpcajpcglclefindmkaj/https://cran.r-project.org/web/packages/mgcv/mgcv.pdf) — generalized additive models, popular for modeling nonlinear developmental trajectories
- [ggplot2](https://ggplot2.tidyverse.org/) — software for data visualization and plotting
- [ggseg](https://cran.r-project.org/web/packages/ggseg/vignettes/ggseg.html) — plotting ROI based data directly in ggplot2
- [ANTsR](https://github.com/ANTsX/ANTsR) — provides ANTs registration/segmentation in R
- [ciftiTools](https://github.com/mandymejia/ciftiTools) — tools for working with subcortical + cortical CIFTI (grayordinate) data 
- [Neuroconductor](https://neuroconductor.org/) — a repository of R packages created for neuroimaging analysis
