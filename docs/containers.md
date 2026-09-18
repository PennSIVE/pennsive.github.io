---
author: "Taki Shinohara"
date: 2026-07-23
---

# Containers

Image processing software is notoriously challenging to install and manage due to the extensive dependencies they have on different libraries and other software packages. To address this, the field has developed a strong reliance on containerization, which is the practice of packing operating systems, libraries, and software into portable containers that can be shared (and are stable and less affected by system upgrades). There are two main systems we employ for containerization: Docker and Apptainer (formerly Singularity).

Docker is widely used as a base for the development and use of containers. However, many high-performance computing environments use a system called apptainer as an alternative. Luckily, containers built using Docker are easily converted and run using apptainer. We use Apptainer extensively on the PMACS LPC as well as other systems.

## PennSIVE / neuro Containers

Over the years, our center has developed many image processing methods and software implementing these. To facilitate their sharing, and to simplify training new members of the community, we have assembled Docker containers with everything necessary to get started with image processing. These are distributed via Docker Hub but also are available directly on the cluster. Please note that these convenient packages contain openly available software from other developers that may be subject to licensing and use restrictions. The neurodocker effort (https://github.com/ReproNim/neurodocker) was key to the development of our containers. While for some time we used a container called "neuror" available on the pennsive Docker Hub page, our current versions of a container are simply called "pennsive" and are avaiable at https://hub.docker.com/repositories/russellshinohara and on the PMACS LPC in /project/singularity_images.

We currently have two types of containers: the base containers, pennsive_amd64 and pennsive_arm64, and the torch-enabled containers, pennsive_amd64_cputorch and pennsive_amd64_gputorch. The two base containers are nearly identical except for their expected chip architecture; the pennsive_arm64 container is for use on Apple Silicon computers, and the pennsive_amd64 version is for use on the cluster. The torch-enabled versions are for use on intel/AMD-architecture machines including our clusters, and we have both a CPU version (for standard queues) and a GPU version (for use on the pennsivegpu queue).

## Using the Docker Container on your Mac

As our group is Mac-centric, the following instructions are provided for use on a Apple Silicon-based Apple running Mac OS. However, if we are using a Linux-based laptop (or Windows, untested), we can employ similar commands using the amd64 base container (pennsive_amd64).

As step zero, we need to install Docker Desktop which is available on the Docker website.

First, to use a container we need to figure out which parts of your laptop's filesystem you want it to be able to access. By default, this includes very little - so when you run the container and enter into your turn-key environment for image processing... there are no images to process, and there is nowhere to save your output. To fix this, we need to "bind-mount" locations in your computer's (the host system) to places in the container's file system. Suppose I want to mount my desktop.

Next, we open a terminal. As we don't yet have the docker container we need, which is pennsive_arm64, we need to pull it. We can do this with `docker pull russellshinohara/pennsive_arm64`. Next, we can run our container by simply writing:

``` bash
docker run -it --rm --platform linux/arm64 \
  --mount type=bind,source="/Users/$USER/Desktop",target=/desktop \
  russellshinohara/pennsive_arm64
```

This command will run the container and give you a new prompt to do your coding within the container (and using all it's installed tools). And, you'll be able to easily access the files on your desktop on the host machine (your laptop), but they won't be in /Users/\$USER/Desktop but rather in /desktop. You can provide multiple --mount arguments to bind several different host machine directories (as is often necessary).

## Using the Docker Container on the Cluster

### Using the Docker Container on the Cluster in Interactive Mode

To use a container on the cluster, things are about as simple after logging into a compute node.

First, let's talk about the base image. To use this to conduct image processing, we need to first log into the cluster submit node, takim2. Once we're there, we can select an appropriate queue for our processing. Unless we're using deep learning methods, this is usually one of the standard queues. In interactive mode, this involves something like:

```         
bsub -Is -q taki_interactive "bash"
```

And we need to load Apptainer:

```         
module load apptainer
```

Then, we can run the container on the compute node with a simple call, and a bind mount:

```         
apptainer shell \
  --bind /project/myproject:/project_folder \
  /project/singularity_images/pennsive_amd64_cputorch.sif
```

Et voila! We can access R, etc.

### Using the GPU Docker Container on the Cluster

If we want to use or develop deep learning methods, we need access to a GPU. To do this, we can use our GPU queue:

```         
bsub -Is -q pennsivegpu -gpu num=1 bash
```

And we need to load Apptainer:

```         
module load apptainer
```

And then, we can run with the --nv flag:

```         
apptainer shell --nv \
  --bind /project/myproject:/project_folder \
  /project/singularity_images/pennsive_amd64_gputorch.sif
```

And similarly, now we can run deep learning methods interactively.

### Batch Jobs with Containers

Using a container for a batch job is just as easy. We only need to put that apptainer call into the script we run when we submit the job (with bsub) and everything will be wrapped up nicely. However, in batch mode we don't need an interactive shell to work with; we just need apptainer to run a container. For example, we may want to run a shell script that has a command like:

```         
apptainer exec --nv \
  --bind /home/rshi/testdata/sub-001_T1w.nii.gz:/in/t1.nii.gz \
  --bind /home/rshi/testdata/out:/out \
  /project/singularity_images/hd-bet-0.3.0.sif \
  hd-bet -i /in/t1.nii.gz -o /out/t1_stripped.nii.gz
```

This will take in a file testdata/sub-001_T1w.nii.gz in my home directory, and run HD-BET on it using Phil Cook's container. It will then output a masked brain image in /home/rshi/testdata/out.

## Troubleshooting Tips

If a problem arises when using a container, the easiest way to approach it is to default back into interactive mode. Often problems involve bind mounted directories where paths become complex. But don't despair - this is SO much easier than trying to figure out which version of python you need and how to get it installed with the right version of some package you've never heard but that's crucial for the task you need to complete!



