---
author: "Noah Hillman"
date: 2026-08-31
---

# Hosts and Queues on the HPC Cluster

## Overview

The PennSIVE cluster is managed by [IBM Spectrum LSF](https://www.ibm.com/docs/en/spectrum-lsf/10.1.0?topic=overview-lsf-introduction), which can be useful to know when looking online for more information about the cluster environment . A **host** (or **node**) is a physical or virtual machine that LSF schedules jobs to be run on. This page covers common commands for checking host status, evaluating queues, and targeting jobs for specific hosts.

## `bhosts`

`bhosts` shows the current status and load of every host on the computing cluster

```bash
bhosts
```

The output from running `bhosts` typically looks like
```
HOST_NAME          STATUS       JL/U    MAX  NJOBS    RUN  SSUSP  USUSP    RSV
pennsive01            ok          -     96     96     96      0      0      0
pennsive02         closed         -     96     96     10      0      0      0
```

which gives you important information such as the cluster status, max number of jobs on a host, and how many jobs are currently running on that host. Common `STATUS` values include

| Status | Meaning |
|---|---|
| `ok` | Host is available and accepting jobs |
| `closed` | Host is at its job-slot limit or manually closed by the cluster maintainer|
| `unavail` | LSF daemon is unreachable |

If you want information on a set of hosts or more detail about a particular host, you can use the commands

```bash
# Only show host pennsive01
bhosts pennsive01   
# Get more details on pennsive01
bhosts -l pennsive01   
```

## `lshosts`

The `lshosts` command shows information about the construction of the host itself such as CPU count, architecture, and memory load. 

```bash
lshosts
# Get more info for host pennsive01
lshosts -l pennsive01     
```

The output from running `lshosts` typically looks like
```
HOST_NAME          type       model    cpuf  ncpus    maxmem  maxswp  server    Resources
pennsive01        X86_64     Intel_EM  60.0   96      565.6G  99.9G    Yes          (mg)
pennsive02        X86_64     Intel_EM  60.0   96      565.6G  99.9G    Yes          (mg)
```

## Host groups

Hosts are often organized into groups so you can target a set of machines instead of a single host.

```bash
# List host groups and their members
bmgroup      
# Get details about taki_exe group
bmgroup -l taki_exe      
```

## Submitting jobs to specific hosts

Use `bsub -m` to restrict a job to one or more hosts or host groups:

```bash
# Restrict job to hosts pennsive01 and pennsive02
bsub -m "pennsive01 pennsive02" ./myscript.sh
# Restrict job to group taki_exe
bsub -m "taki_exe" ./myscript.py
```

## Parallel jobs

A parallel job in LSF asks for multiple job slots (`-n`). LSF has to decide which hosts supply those slots, which is controlled by `span` in `-R`.

```bash
bsub -n 4 ./parallel_job_script.sh
```

By default LSF will spread those four jobs across as many hosts as needed to maximize computation speed. However, if you want all cores to come from the same host, use

```bash
# All 4 jobs on a single host
bsub -n 4 -R "span[hosts=1]" ./myjob.sh
# Exactly 2 slots per host, spread across as many hosts is necessary
bsub -n 16 -R "span[ptile=2]" ./mpi_job.sh
```
`span` options:

| Value | Meaning |
|---|---|
| `span[hosts=1]` | All requested slots go to a single host |
| `span[ptile=N]` | Allocate N slots per host |


To see where a job actually landed, you can use the command

```bash
bjobs -l <jobID> 
```

To check what is currently running on a host, use

```bash
# Find jobs currently running on pennsive01
bjobs -m pennsive01         
# Get static information about the node
bhosts -l pennsive01          
```

## `bqueues`

Every job gets submitted to a **queue** that decides which jobs are higher priority and get to run first, as well as per-user slot and run-time limits. To see the available queues, run

```bash
bqueues
```

which creates output such as 

```
QUEUE_NAME      PRIO STATUS          MAX  JL/U  JL/P  JL/H  NJOBS  PEND   RUN   SUSP
taki_normal      30  Open:Active      -    -      -     -   38550 37200  1350     0
```

To get more information about the queues, you can run

```bash
# Get details about max jobs, run limit, etc. on the queue
bqueues -l normal      
# Tells you what queues can dispatch jobs to pennsive01
bqueues -m pennsive01  
```