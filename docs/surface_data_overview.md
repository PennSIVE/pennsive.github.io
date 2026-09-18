---
author: "Noah Hillman"
date: 2026-08-31
---

# Surface-based Neuroimaging Analyses

## Introduction

Neuroimaging data is commonly represented in volumetric space, as brain images are stored as 3D grids of voxels. Surface-based analysis offers a complementary approach that represents the brain's cortex as a 2D sheet, which can be better suited for data visualization and capturing brain topology.

This page provides a high-level overview of working with surface neuroimaging data where we will cover:

- How the cortical surface is represented geometrically
- The GIFTI and CIFTI file formats
- Connectome Workbench for processing and visualizing surface data
- Working with surface data in Python and R

## Surface Meshes

The cortical surface is a highly folded sheet of gray matter, such that distances in voxel space may not correspond well with geodesic distance (i.e., the distance along the surface). 

- A sulcal bank and the facing gyral wall may be adjacent in voxel space but topologically distant on the surface
- To properly capture cortical topology, it is common to represent the cortical surface as a triangular mesh.

### Vertices, Edges, and Faces

A surface mesh is defined by:

- **Vertices**: a set of $N$ points in 3D space, each with coordinates $(x, y, z)$
- **Faces (triangles)**: triplets of vertex indices that define the vertices that are connected by edges to form a triangle

Common surface meshes include those from the Human Connectome Project (HCP) that contain 32k (standard) or 164k (high-resolution) vertices per hemisphere in [fsLR space](https://bids-specification.readthedocs.io/en/v1.2.2/99-appendices/08-coordinate-systems.html). 

### Surface Types

During preprocessing, multiple surface types are generated for each hemisphere:

| Surface | Description |
|---------|-------------|
| `white` | Inner boundary of cortical gray matter (white matter surface) |
| `pial` | Outer boundary of cortical gray matter |
| `midthickness` | Midpoint between white and pial |
| `inflated` | Inflated surface for visualization  |
| `very_inflated` | Further inflated |
| `sphere` | Unit sphere; used for cross-subject registration |

Metric data (e.g., cortical thickness, fMRI activation) is associated with vertices on the midthickness surface and can be projected onto any surface type for visualization.


## File Formats: GIFTI and CIFTI

### GIFTI

[GIFTI](https://www.nitrc.org/projects/gifti/) (Geometry Format for the Neuroimaging Informatics Technology Initiative) is a standard format for storing surface geometry and surface-mapped data. A GIFTI file (`.gii`) contains one or more data arrays, each tagged with an intent code indicating what the data represents.

Common GIFTI file types:

| Extension | Intent | Contents |
|-----------|--------|----------|
| `.surf.gii` | `NIFTI_INTENT_POINTSET` + `NIFTI_INTENT_TRIANGLE` | Vertex coordinates and face list |
| `.func.gii` | `NIFTI_INTENT_NONE` or `NIFTI_INTENT_TIME_SERIES` | Functional data |
| `.shape.gii` | `NIFTI_INTENT_NONE` | Shape/metric data (e.g., cortical thickness) |
| `.label.gii` | `NIFTI_INTENT_LABEL` | Parcellation labels |

A `.surf.gii` file stores the mesh geometry of vertex coordinates and triangle indices for one hemisphere. Typically, a `.func.gii` or `.shape.gii` file stores one or more scalar maps aligned to the same vertex set. See the [GIFTI specification](https://www.nitrc.org/frs/download.php/2871/GIFTI_Surface_Data_Format_Version1.0.pdf) for more details on GIFTI file specifications. 


### CIFTI

[CIFTI](https://www.nitrc.org/projects/cifti/) (Connectivity Informatics Technology Initiative) extends previous formats to represent data jointly across surface vertices and subcortical voxels. 

Some common CIFTI file types include:

| Extension | Contents |
|-----------|----------|
| `.dtseries.nii` | Dense timeseries — fMRI BOLD for every grayordinate across time |
| `.dscalar.nii` | Dense scalar map — one or more scalar values per grayordinate |
| `.dlabel.nii` | Dense label file — parcellation labels per grayordinate |
| `.dconn.nii` | Dense connectivity matrix file — correlations between grayordinates |
| `.ptseries.nii` | Parcellated timeseries — timeseries per parcel  |
| `.pscalar.nii` | Parcellated scalar map |
| `.pconn.nii` | Parcellated connectivity matrix |

The term grayordinate refers to a single unit in the CIFTI brain format, either a cortical surface vertex or a subcortical gray matter voxel. A standard `91k` dense file contains ~91,000 grayordinates (59,412 cortical vertices + ~31,000 subcortical voxels).

## Connectome Workbench

[Connectome Workbench](https://www.humanconnectome.org/software/connectome-workbench) is a useful tool for working with surface and CIFTI data. It provides:

- **`wb_view`**: a GUI for interactive visualization
- **`wb_command`**: a command-line tool with numerous operations for processing surface and CIFTI files

### Installation

Connectome Workbench can be downloaded from the [HCP website](https://www.humanconnectome.org/software/get-connectome-workbench)

---

## Visualizing Surface Data with `wb_view`

`wb_view` is a powerful GUI for visualizing surface and CIFTI data. It supports simultaneous display of multiple surfaces, overlays, and volume slices.

##### Loading Data in the GUI

1. **Open files**: Use *File → Open File* to load `.surf.gii` file that encodes the geometry of the surface. 
2. **Overlay toolbox**: Add overlays by opening `.shape.gii` or `.dscalar.nii` files in the same atlas space as the `.surf.gii` file. Adjust the presentation of the overlays by using the overlay toolbox at the bottom of `wb_view`. 

See the [Workbench Connectome tutorial](https://www.humanconnectome.org/storage/app/media/documentation/tutorials/Connectome_WB_Tutorial_v1.5.pdf) for much more information on how to use `wb_view`. 

### Common Workflows

`wb_command` is a command-line interface that can be used for numerous pre-processing steps when working with surface data. Examples include: 

- Smoothing a metric file using wb_command -metric-smoothing
- Separating a CIFTI file into two hemispheres with wb_command -cifti-separate
- Resampling to a different resolution with wb_command -metric-resample

The [wb_command reference](https://www.humanconnectome.org/software/workbench-command/-cifti-math) contains documentation for the many use cases of `wb_command`.

## Working with Surface Data in Python

### nibabel

[nibabel](https://nipy.org/nibabel/) is the main Python library for reading neuroimaging file formats, including GIFTI and CIFTI.

```python
import nibabel as nib
import numpy as np

# Load a GIFTI surface geometry file
surf = nib.load("sub-001.L.midthickness.32k_fs_LR.surf.gii")
vertices = surf.darrays[0].data 
faces    = surf.darrays[1].data 

# Load a GIFTI metric (scalar) file
metric = nib.load("sub-001.L.thickness.shape.gii")
thickness = metric.darrays[0].data

# Load a CIFTI scalar map
img = nib.load("sub-001.dscalar.nii")
```
More information on working with GIFTIs and CIFTIs can be found in the [nibabel manual](https://nipy.org/nibabel/manual.html).

### Neuromaps

**[neuromaps](https://netneurolab.github.io/neuromaps/)** provides tools for fetching and transforming brain annotation maps across different spaces, with strong CIFTI/GIFTI support:

```python
from neuromaps import datasets, transforms

# Fetch a reference annotation
annotation = datasets.fetch_annotation(source='abagen', desc='genepc1')

# Resample between surface spaces (fsaverage → fsLR)
resampled = transforms.fsaverage_to_fslr(annotation, target_density='32k')
```

## Working with Surface Data in R

### ciftiTools

[ciftiTools](https://github.com/mandymejia/ciftiTools) is the most widely used R package for reading, writing, and visualizing CIFTI files.

#### Setup

```r
#| eval: false
install.packages("ciftiTools")
library(ciftiTools)
ciftiTools.setOption("wb_path", "/path/to/workbench/bin_macosx64/wb_command")
```

#### Reading and exploring CIFTI files

```r
#| eval: false
# Read a dense timeseries
xii <- read_cifti("sub-01_bold.dtseries.nii")
# Access left cortex data only
lh_data <- xii$data$cortex_lh   
# Resample to a coarser resolution
xii_32k <- resample_cifti(xii, resamp_res = 32492)
```

For more information on how to use the ciftiTools package for surface-based analysis, see the [ciftiTools website](https://mandymejia.r-universe.dev/ciftiTools).

### gifti (R package)

The [gifti](https://cran.r-project.org/web/packages/gifti/) package provides basic support for analyzing GIFTI files in R:

    ```r
    #| eval: false
    install.packages("gifti")
    library(gifti)

    # Read a surface geometry file
    surf <- read_gifti("sub-01.L.midthickness.32k_fs_LR.surf.gii")
    vertices <- surf$data$pointset 
    faces    <- surf$data$triangle 

    # Read a metric file
    metric <- read_gifti("sub-01.L.thickness.shape.gii")
    thickness <- metric$data[[1]]
    ```
---

## Learning Resources
- [Andy's Brain Book: FreeSurfer Tutorial](https://andysbrainbook.readthedocs.io/en/latest/FreeSurfer/FreeSurfer_Introduction.html): accessible intro to surface data in FreeSurfer
- [Connectome Workbench Tutorial (HCP)](https://www.humanconnectome.org/storage/app/media/documentation/tutorials/Connectome_WB_Tutorial_v1.0.pdf): comprehensive guide to `wb_view`
- [nibabel CIFTI documentation](https://nipy.org/nibabel/reference/nibabel.cifti2.html)
- [neuromaps documentation](https://netneurolab.github.io/neuromaps/): tutorials on surface annotation, transformation, and statistical comparison
- [ciftiTools vignettes](https://htmlpreview.github.io/?https://github.com/mandymejia/ciftiTools/blob/master/vignettes/ciftiTools_vignette.html): examples of common pipelines using ciftiTools workflow in R





