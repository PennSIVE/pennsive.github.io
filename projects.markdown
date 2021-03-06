---
layout: page
title: Project setup
permalink: /project-setup/
nav_order: 3
---
# Setting up a project
Code and data should be version controlled with git and git annex, respectively. The changes code makes to data should be recorded with datalad, a powerful wrapper for git annex. Imaging data should be organized into BIDS where possible.

## Datalad
Datalad can be installed with `conda create -n datalad -c conda-forge datalad`, then enabled with `conda activate datalad`. To create a new datalad repository, run `datalad create -c text2git -c yoda repository_name`. `-c text2git -c yoda` are datalad procedures that configure git annex and initialize your repository with a boilerplate directory structure.
```
├── CHANGELOG.md
├── README.md
└── code
    └── README.md
```

To version control data, run `datalad save -r -m "commit message"`. This will move your files into the "git annex" and replace them with symlinks from the annex. This keeps your data safe so if you accidentally `rm something`, you can restore it with `git checkout something`. Using `datalad save` is useful when initially moving source data into a repository, but is otherwise an anti-pattern since files will be created or modified by scripts, which you want to run through the `datalad run` wrapper. Although it is possible to run your scripts then save the results with `datalad save` afterwords, running your code with `datalad run` is preferred because it automatically checks to make sure inputs are available, unlocks and saves outputs, as well as commits the command line used to do the processing so results can be reproduced in an automated way. For example, instead of
```sh
datalad unlock my_inputs
./my_script.sh my_inputs my_outputs
datalad save -r -m "ran my script" my_outputs
```
do
```sh
datalad run -m "ran my script" -i my_inputs -o my_outputs ./my_script.sh "{inputs}" "{outputs}"
```
Once you've run your analysis with datalad run, make sure your results are reproducible by running `datalad rerun`. Code can be unreproducible in very non-obvious ways (for example, the default way of setting a seed won't work in R if your code is multithreaded), so running twice is the only way to make sure your analysis is reproducible.

### Datalad caveats
Like many neuroimaging tools, scaling datalad to work with large datasets presents additional challenges. Git can only handle so many files per repository, so it's important to break data into subdatasets, but there's also a practical limit on the number of subdatasets per dataset, so very large projects may need to restructure flat directory trees into many layers of nested subdatasets.

To create a subdataset, run `datalad create -d . subdataset` where `.` is your parent dataset. Datalad uses git submodules to relate your parent dataset to its subdatasets, which record the path to the subdataset and which commit it's on. So when committing changes in subdatasets, you will need to make a commit in every parent dataset that you want to update the commit it's on (datalad save's `-r` flag is useful for this).

Since datalad monitors all files for changes parallelizing datalad run commands can be difficult as datalad doesn't know which files correspond to which runs. There are two workarounds. The simpler way is to use the `--explicit` flag to tell datalad to only monitor changes in the inputs and outputs provided by the `-i` and `-o` arguments. The more robust way is to checkout a new branch for every run command, and octopus merge them at the end.

Although most projects reside on network-mounted directories, datalads decentralized nature makes it easy to move files to a more performant filesystem (like `$TMPDIR`) then have results pushed back. For example,

```sh
#!/bin/bash

# fail whenever something is fishy, use -x to get verbose logfiles
set -e -u -x

ds_path=$(realpath $(dirname $0)/..) # assuming were in ./code
sub=$1 # script meant to be called with subject id as first (only) argument
# $TMPDIR is a more performant local filesystem
wrkDir=$TMPDIR/$LSB_JOBID
# or, on SGE, wrkDir=$TMPDIR/$JOB_ID
mkdir -p $wrkDir
cd $wrkDir
# get the output/input datasets
# flock makes sure that this does not interfere with another job
# finishing at the same time, and pushing its results back
# we clone from the location that we want to push the results too
# $DSLOCKFILE should be exported, it simply points to an empty file in .git used as a lock
flock $DSLOCKFILE datalad clone $ds_path ds
# all following actions are performed in the context of the superdataset
cd ds
# obtain datasets
datalad get -r sourcedata/${sub}/dicoms
datalad get nifti
datalad get -r nifti/sub-${sub}
datalad get simg/heudiconv_latest.sif
# let git-annex know that we do not want to remember any of these clones
# (we could have used an --ephemeral clone, but that might deposite data
# of failed jobs at the origin location, if the job runs on a shared
# filesystem -- let's stay self-contained)
git submodule foreach --recursive git annex dead here

# checkout new branches
# this enables us to store the results of this job, and push them back
# without interference from other jobs
git -C nifti checkout -b "sub-${sub}"
git -C nifti/sub-$sub checkout -b "sub-${sub}"

# yay time to run
datalad run -i "sourcedata/${sub}" -i "simg" -o "nifti" \
    singularity run --cleanenv \
    -B /project -B $TMPDIR \
    $PWD/simg/heudiconv_latest.sif \
    -d "$PWD/sourcedata/MSDC_{{subject}}/dicoms/*/*.dcm" \
    -o $PWD/nifti -f $PWD/code/myheuristic.py -s $sub -ss 01 -c dcm2niix -b

# selectively push outputs only
# ignore root dataset, despite recorded changes, needs coordinated
# merge at receiving end
flock $DSLOCKFILE datalad push -r -d nifti --to origin

cd ../..
chmod -R 777 $wrkDir
rm -rf $wrkDir
```

For more information, the [datalad handbook](http://handbook.datalad.org/en/latest/index.html) is an excellent resource.

## BIDS
When labs organize datasets in different ways time is often wasted rewriting scripts to expect a particular structure. BIDS solves this problem by standardizing the organization of neuroimaging data. BIDS is nothing more than a prescribed directory structure and file naming convention, but it allows labs to more easily share datasets and processing pipelines, interact with the dataset programmatically via pybids, and run all the most popular neuroimaging tools ("BIDS Apps") in a consistent way.

Although it's possible to manually organize NIFTIs into the BIDS structure, doing so is error-prone and inefficient. Manual organization may be the only option if dicoms aren't available, but otherwise you can use heudiconv to automatically format the produced NIFTIs according to BIDS.

Unlike `dcm2niix` which can simply be fed a directory of dicoms, heudiconv requires a bit more setup. First, your dicoms be organized either by StudyUID or accession_number. Then, you need to run heudiconv with the `-c none -f convertall` options to generate TSVs with dicom metadata needed to write a heuristic. Generally, the protocol_name will be the most (only?) useful field, and is used in the heuristic to determine which dicoms go together to create a NIFTI.

For example,
```sh
heudiconv \
    -d "/path/to/data/{subject}/{session}/*.dcm" \
    -o /path/to/output \
    -f convertall \
    -s 001 -ss abc -c none -b -g accession_number
```
There will now be a hidden .heudiconv directory with dicom header TSVs. Once you've determined which dicoms go where, write a heuristic then use the `-c dcm2niix` option to tell heudiconv to do the actual dcm2niix conversion.
```sh
heudiconv \
    -d "/path/to/data/{subject}/{session}/*.dcm" \
    -o /path/to/output \
    -f /path/to/your_heuristic.py \
    -s 001 -ss abc -c dcm2niix -b -g accession_number
```
