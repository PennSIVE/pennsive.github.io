---
author: "Ethan Bouche & Yiyan Hao"
date: 2026-08-28
---

# Cluster Packages

## R

### Overview

This section explains how R packages work on the cluster, including how to:
Activate the environment before running a Python script. Loading the module in the same script or job is important because a new shell may not inherit your interactive module setup:

- See what R packages are already available

- Check if a package you need is installed

- Install packages into your own user library (from CRAN or GitHub)

- Set your .libPaths() so R finds your personal packages first

The key idea is that **each user is responsible for their own R packages**. The cluster:

- Does not connect to the public internet from compute nodes
- Does not allow users to install packages into system-wide libraries

 1. Download it (e.g., on your local machine from CRAN/GitHub)
 2. Copy it to the cluster
 3. Install it into your personal R library
 4. Manually ensure all dependencies are installed
 
### How R libraries work on the cluster
```r
.libPaths()
```

The typical output will display the cluster package directory:

```r
[1] "/misc/appl/R-4.5/lib64/R/library"
```

### Listing installed packages

There are many packages already installed on the clsuter. There is a good chance that the package you want is already installed. To list all packages available in your current R session:

```r
installed.packages()
```

To quickly check if a package is installed 

```r
"your_desired_package" %in% rownames(installed.packages())
```

If this returns `FALSE` you will need to install the package manually.

### Installing packages

Because compute nodes do not have internet access, you generally cannot run:

```r
install.packages("your_desired_package")
```

directly on the cluster and expect it to download from CRAN(or another other repository). It is recommended to create a directory in your home directory on the cluster which can be used as your personal package directory. This can be done directly in R:

```r
dir.create("/home/username/R_packages", recursive = TRUE, showWarnings = FALSE)
```


!!! note
    Dependencies: When installing a package from a tarball (CRAN or otherwise), R will still need any dependencies to be installed and available in your libraries.

    If installation fails with messages about missing packages (e.g., error: there is no package called `'rlang'`), you must repeat the same process for each missing dependency.



#### Installing from CRAN

To install a package from CRAN you will need to download the tarball file directly from CRAN. (`e.g.~dplyr_1.1.4.tar.gz`), and copy that to a directory on the cluster. You can then install your package using the code below

```r
install.packages(
  "/home/username/R_src/dplyr_1.1.4.tar.gz",
  repos = NULL,
  type = "source",
  lib   = "/home/username/R_packages"
)
```

#### Install from GitHub

To install a package from GutHub you can follow the same work flow. First download the package from GitHub and transfer it to the cluster. If the GitHub provides a built tarball you can install it using the same code as before:

```r
install.packages(
  "/home/username/R_src/github_package_0.1.0.tar.gz",
  repos = NULL,
  type  = "source",
  lib   = "/home/username/R_packages"
)
```

However, if you they do not provide it as a tarball you will have to install it from the package directory.

```r
install.packages(
  "/home/username/R_src/package_name",
  repos = NULL,
  type  = "source",
  lib   = "/home/username/R_packages"
)
```

### Loading your installed packages in scripts

In any R script run on the cluster (interactive or batch), include your personal library path early so R can find your packages. R will check the directories in the order they are supplied in the code below, this is important if you need a different version than installed on the cluster. 

```r
# Set up personal library path
.libPaths(c("/home/user/R_packages", .libPaths()))

# Load packages
library(dplyr)
library(your_desired_package)
```
### Key Points

#### Packages not on the cluster

If a package you need is not present in the system libraries:

- You will **not** be able to install it from CRAN/GitHub directly on the cluster.
- You will need to download the source, transfer it to the cluster, and install in manually to your personal library.

PMACS typically does **not** install arbitrary R packages system-wide on request, especially if they are specific to a single lab/project.

#### Don't forget the dependencies!

For packages you install yourself you must manually ensure dependencies are installed.

Installation errors often indicate missing dependencies. For example:

```r
ERROR: dependency 'rlang' is not available for package 'dplyr'
```

In this case you would need to install `rlang` before `dplyr`

Sometimes dependencies also depend on system libraries (e.g., `curl`, `openssl`, `xml2`). If installation fails due to missing system libraries or headers, PMACS may need to be involved.


!!! note
    When encountering installation problems, it is helpful to:

    Save the full R installation log.
    Note the exact package version and source (CRAN, GitHub commit/tag).
    Share any error messages in the lab #computing Slack channel. 


### When to ask for help

#### Start with the lab Slack channel

The lab `#computing` Slack channel is a good place to ask about:

- Whether a package is already installed somewhere on the cluster
- Problems installing a package from source
- Errors about missing dependencies or system libraries
- Recommended versions of R or packages for specific workflows
Include:

- The package name and version
- How you obtained the source (CRAN/GitHub)
- The command you used to install
- The full error message

#### When to contact PMACS

Contact PMACS when:

- Installation fails due to missing system libraries or compilers
- You suspect a module or environment issue with the R installation
- You need a specific R version or major system dependency (e.g., `curl`, `openssl`) installed

**Note** If you reach out to PMACS for system wide installation you may need approval from Taki/your PI. You should consult them before reaching out to PMACS.

### FAQ

#### Will packages I install be available to everyone?

No. Packages you install go into your **personal library** (e.g., `/home/username/R_packages)` and are not shared automatically with other users.


#### Can I use `install.packages()` directly on the cluster?
Only if PMACS has configured a local CRAN mirror or specific repository and the node has access. In general, assume internet-based downloads from compute nodes are not available and plan to install from local source files.


#### Do I need to set `.libPaths()` every time?
You should set `.libPaths()` in any script or R session where you rely on your personal library. Many users place a line like:


```r
.libPaths(c("/home/username/R_packages", .libPaths()))
```
in their `~/.Rprofile` so it is applied automatically.

#### How do I check which R version I am using?

```r
R.version.string
```

Some packages require specific R versions; if you run into version-related errors, note this when asking for help.

## Python

### Python packages with conda

This section explains how to create and use a Python environment on the cluster with `conda`. A conda environment keeps Python and its packages separate from the system installation and from other projects. Ideally, you would create a new environment for each project or workflow. This improves reproducibility and avoids conflicts between package versions.

### Check for conda

On the LPC, Conda is provided through `miniconda`. To create or use a conda environment, always run the following commands first:

```bash
module load miniconda
eval "\$(conda shell.bash hook)"
```

To verify that conda is available:

```bash
conda --version
```

If the module is not successfully loaded, consult PMACS as sometimes system upgrades may affect these commands.

### Create an environment

Now, you can create an environment for a project. Choose a meaningful name and, when possible, specify the Python version explicitly:

```bash
conda create --name my_project python=3.12
```

Activate the environment before installing packages or running Python:

```bash
conda activate my_project
```

Confirm that the environment is active and that Python is coming from it:

```bash
conda env list
which python
python --version
```

The active environment is marked with an asterisk in `conda env list`. The output of `which python` should point to the `my_project` environment rather than to a system Python installation.

!!! note
    ### Optional: choose a different environment location

    By default, conda stores environments in the `.conda` directory in your home directory. If your home directory has limited space, set `CONDA_ENVS_PATH` to a directory with more available storage (e.g., a project or scratch directory) before creating the environment:

    ```bash
    export CONDA_ENVS_PATH="/path/to/environment_storage"
    ```

    See [Using IDEs on Cluster](wiki-ides.md) for how to use a conda environment from a customized path on VS Code or Jupyter Notebook.


### Install packages

To install packages that are available through conda channels:

```bash
conda install numpy pandas scikit-learn
```

Sometimes you may need to install a package from a particular channel, in which case you can specify the channel with `-c`:

```bash
conda install -c conda-forge package_name
```

Packages that are not available through conda can sometimes be installed with `pip` after activating the environment. Use `python -m pip` instead of a standalone `pip` command so that pip belongs to the active environment:

```bash
python -m pip install package_name
```

Check what is installed with:

```bash
conda list
python -m pip list
```

### Use the environment

Activate the environment before running a Python script. Loading the module in the same script or job is important because a new shell may not inherit your interactive module setup:

```bash
module load miniconda
eval "\$(conda shell.bash hook)"

conda activate my_project
python path_to_my_script.py
```

### Export an environment for reproducibility

When collaborating with others on a specific project, it is helpful to share the exact environment specification. You can export the environment to a YAML file, which allows others to recreate the same environment in a different machine:

```bash
conda activate my_project
conda env export --no-builds > environment.yml
```

Then, your collaborator can create a new environment from the file with:

```bash
conda env create --name my_project --file environment.yml
conda activate my_project
```

If an environment with that name already exists, update it instead:

```bash
conda env update --name my_project --file environment.yml --prune
```

You may want to keep a separate `requirements.txt` when pip packages are part of the project:

```bash
python -m pip freeze > requirements.txt
```


### Key takeaways

- Use a separate conda environment for each project or workflow.
- Activate the environment before installing packages or running Python.
- Prefer conda for packages with compiled dependencies; use pip only after activating the environment when a package is not available through conda.
- Export the environment to a YAML file for reproducibility and collaboration.
- For more advanced usage, see the [Conda documentation](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html). The PMACS wiki also contains a [helpful page on conda](https://wiki.pmacs.upenn.edu/public/Miniconda). 

