---
author: "Ethan Bouche"
date: 2026-07-15
---

# CPU GPU Computing

## Overview

This page provides a brief overview of CPU and GPU computing on the cluster. It is meant to help lab members decide when to use CPU resources, when to use GPU resources, and what information to check before submitting jobs.

The main idea is simple: **most lab analyses should start on CPUs**. GPUs are useful for specialized workloads, especially deep learning or software that explicitly supports GPU acceleration, but requesting a GPU does not automatically make code faster.

## Quick takeaways

- Use a **CPU job** for most R scripts, statistical models, data cleaning, simulations, image processing pipelines, and command-line tools.
- Use a **GPU job** only when the software is written to use a GPU, such as PyTorch, TensorFlow, CUDA-enabled tools, or another GPU-aware package.
- Use **more CPU cores** when your code can run in parallel.
- Use **more memory** when your job fails because it cannot hold the required data in memory.
- Do not request a GPU for code that is CPU-only. It will reserve a limited resource without making the job faster.

## What is a CPU?

A CPU, or central processing unit, is the main general-purpose processor on a computer. CPUs are flexible and are designed to handle many different types of tasks. Most everyday analysis code runs on CPUs by default.

CPU jobs are usually the right choice for:

- Standard R scripts
- Data cleaning and file manipulation
- Regression models and statistical analyses
- Most simulations
- Running many independent analyses across subjects, files, or parameter settings
- Most command-line tools unless the documentation specifically mentions GPU support
- Jobs that need more memory but do not need GPU acceleration

For many lab workflows, the best way to scale up work is not to use a GPU, but to submit multiple CPU jobs or an array job.

## What is a GPU?

A GPU, or graphics processing unit, is a specialized processor designed to perform many similar calculations at the same time. GPUs can be much faster than CPUs for certain workloads, but only when the software is designed to use them.

GPU jobs are usually appropriate for:

- Deep learning models
- Neural network training or inference
- PyTorch, TensorFlow, or similar frameworks configured with GPU support
- CUDA-enabled imaging or scientific computing tools
- Large matrix operations using a GPU-aware package

GPU jobs are usually **not** helpful for:

- Standard R code that does not use a GPU-aware package
- Small jobs
- Jobs where most of the time is spent reading or writing files
- Jobs that are slow because they need more memory rather than more computation
- Parallel simulations that can be split into many independent CPU jobs

## CPU vs GPU decision guide

| Question | Usually use |
|---|---|
| Am I running a normal R script? | CPU |
| Am I fitting standard statistical models? | CPU |
| Am I running many independent analyses or simulations? | CPU, often as an array job |
| Is my job failing because it runs out of memory? | CPU with more memory requested |
| Does the software documentation mention CUDA, GPU, PyTorch, TensorFlow, or GPU acceleration? | GPU may be appropriate |
| Am I training a neural network or running a GPU-supported model? | GPU |
| Am I unsure whether the software can use a GPU? | Start with CPU, or test interactively before requesting a GPU |

## More cores vs more memory vs GPU

These are different resources and they solve different problems.

**More CPU cores** help when the code can divide work across multiple cores. Examples include parallelized R code, array jobs, some image processing pipelines, and software that supports multithreading. More cores will not help much if the code is single-threaded.

**More memory** helps when the job needs to hold large objects in memory. If a job fails with an out-of-memory error, requesting additional memory is usually more helpful than requesting more cores or a GPU.

**A GPU** helps only when the software explicitly sends work to the GPU. A job can run on a GPU node while still doing all of its computation on the CPU if the code is not GPU-enabled.

## Cluster resources available to the lab

The cluster uses LSF for job scheduling. Jobs are submitted to queues, and the queue determines what type of resource the job can use.

The exact queue names and limits should be confirmed, but the lab likely has access to CPU queues and one GPU queue.

| Resource | Queue or command | Notes |
|---|---|---|
| CPU interactive queue | `taki_interactive` | For short testing and exploratory work |
| CPU batch queue | `taki_normal` | For regular non-interactive jobs |
| GPU queue | `pennsivegpu` | For jobs that explicitly require a GPU |
| GPU model(s) | `NVIDIA L40S` | `pennsivegpu01`, has 4 NVIDIA L40S GPUs, each with about 46 GB of GPU memory |


!!! note
    The GPU queue should be treated as a limited shared resource. Before submitting a GPU job, check that the software actually supports GPU acceleration.


## How to check available resources

To list available queues for your username:

```bash
bqueues -u user_name
```

To get more detail about a specific queue:

```bash
bqueues -l queue_name
```

This shows GPU-related host information. Please note you may not have access to all these queues:

```bash
bhosts -gpu
```

After starting an interactive GPU job, this command shows the GPU model, memory, driver version, and current GPU usage:

```bash
nvidia-smi
```

If CUDA modules are available, they may be visible with:

```bash
module avail cuda
```


## Practical workflow

### 1. Start with a small CPU test

Before submitting a large job, test the script on a small input, one subject, or a short run. This helps confirm that the code works and gives a rough sense of memory and runtime needs.

### 2. Use batch jobs for long-running work

Interactive jobs are useful for testing, but long-running analyses should usually be submitted as batch jobs. Batch jobs continue running even if your local connection drops.

### 3. Use array jobs for repeated independent work

If the same script needs to run across many subjects, files, seeds, or parameter settings, an array job is often better than one very large job. This is common for simulations and subject-level processing.

### 4. Use GPU jobs only for GPU-enabled software

Before requesting a GPU, check the software documentation. Look for terms such as GPU, CUDA, PyTorch, TensorFlow, CuPy, RAPIDS, or GPU acceleration.

### 5. Save logs

For batch jobs, save standard output and standard error logs. Logs make it much easier to understand whether a job failed because of memory, software, file paths, permissions, or another issue.


## When to ask for help

### Start with the lab Slack channel

For general computing questions, the lab `#computing` Slack channel is often a good place to start. Other lab members may have run into similar issues before and can usually help with questions like:

* Which queue should I use for this job?
* Does this analysis need a GPU?
* Why is my job pending?
* Why did my job run out of memory?
* Is this software package actually using the GPU?
* Has anyone used this package or workflow on the cluster before?

When posting in Slack, it is helpful to include a few details, such as the queue you used, the software or package you are running, and a short description of what happened.

### When to contact PMACS

For issues that are unresolved in Slack, appear to be cluster-level bugs, or involve installing/configuring software on the cluster, consider reaching out to [PMACS](https://helpdesk.pmacs.upenn.edu/).

This may be the better option if:

* A job fails for reasons that are not clear from the error log
* A queue or node appears to be behaving unexpectedly
* You need help setting up CUDA, GPU-enabled software, or a container
* You need software installed or updated
* You are having permissions, environment, or module issues
* A problem seems specific to the cluster rather than your code

When contacting PMACS, include enough information for them to reproduce or diagnose the issue:

* The job ID
* The queue used
* The job script
* The output log
* The error log
* The software/package you are trying to run
* A short description of what you expected to happen and what happened instead

## FAQ

### Will my R code automatically use a GPU?

Usually, no. Most R code runs on CPUs. To use a GPU, the package must support GPU acceleration and the code must be written to use that support.

### Should I use a GPU for parallel simulations?

Usually, no. If the simulation consists of many independent runs, CPU array jobs are often a better fit.

### Should I request more CPU cores if my job is slow?

Only if the code can use multiple cores. If the code is single-threaded, requesting more cores may not improve runtime.

### Should I request a GPU if my job runs out of memory?

Usually, no. Running out of memory is usually solved by requesting more memory or changing how the data are processed, not by requesting a GPU.

### What is the safest default?

Start with a small CPU job. Move to a GPU only when the software documentation or a small test confirms that GPU acceleration is supported.

