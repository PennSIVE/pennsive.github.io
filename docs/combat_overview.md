# PennSIVE ComBat Overview

**Created by:** Gina Yu  
**Last Updated:** September 14, 2026

This page provides an overview of ComBat resources and methods developed by PennSIVE alumni, current members, and collaborators. Please refer to the resources below for more information on ComBat and its extensions.

## Quick ComBat Overview

ComBat is a popular harmonization tool originally created by Evan Johnson and colleagues in 2007 to address batch effects in genomic data. ComBat aims to preserve biological variation while addressing technical variation introduced through differences in data collection.

Johnson et al. (2007), *Adjusting batch effects in microarray expression data using empirical Bayes methods*. ([Paper](https://doi.org/10.1093/biostatistics/kxj037))

### neuroComBat

neuroComBat was developed by Jean-Philippe Fortin, adapting ComBat to harmonize neuroimaging data by removing site/scanner differences while preserving biological signal.

Covariates such as age and sex can be included in the ComBat model so that their biological effects are preserved during harmonization. ComBat estimates site-specific effects and aligns sites toward a common distribution while retaining the biological variation represented by these covariates.

Fortin et al. (2017), *Harmonization of multi-site diffusion tensor imaging data*. ([Paper](https://doi.org/10.1016/j.neuroimage.2017.08.047))

Fortin et al. (2018), *Harmonization of cortical thickness measurements across scanners and sites*. ([Paper](https://doi.org/10.1016/j.neuroimage.2017.11.024))

## ComBat Extensions

The ComBat family has expanded to address different harmonization settings. The methods below highlight work from PennSIVE members, alumni, and collaborators.

| Method | Designed For | PennSIVE Connection | Publication |
|---|---|---|---|
| ComBat | Standard cross-sectional batch/site harmonization | PennSIVE/collaborator/alumni | Johnson et al. (2007), *Adjusting batch effects in microarray expression data using empirical Bayes methods*. ([Paper](https://doi.org/10.1093/biostatistics/kxj037)) |
| neuroComBat | Harmonization of multi-site neuroimaging measurements | PennSIVE/collaborator/alumni | Fortin et al. (2017), *Harmonization of multi-site diffusion tensor imaging data*. ([Paper](https://doi.org/10.1016/j.neuroimage.2017.08.047)) <br><br> Fortin et al. (2018), *Harmonization of cortical thickness measurements across scanners and sites*. ([Paper](https://doi.org/10.1016/j.neuroimage.2017.11.024)) |
| ComBat-GAM | Nonlinear covariate effects | PennSIVE/collaborator/alumni | Pomponio et al. (2020), *Harmonization of large MRI datasets for the analysis of brain imaging patterns throughout the lifespan*. ([Paper](https://doi.org/10.1016/j.neuroimage.2019.116450)) |
| Longitudinal ComBat | Repeated/longitudinal measurements | PennSIVE/collaborator/alumni | Beer et al. (2020), *Longitudinal ComBat: A method for harmonizing longitudinal multi-scanner imaging data*. ([Paper](https://doi.org/10.1016/j.neuroimage.2020.117129)) |
| CovBat | Differences in residual covariance structure across sites | PennSIVE/collaborator/alumni | Chen et al. (2022), *Mitigating site effects in covariance for machine learning in neuroimaging data*. ([Paper](https://doi.org/10.1002/hbm.25688)) |
| ComBatLS | Preserving biological effects on location and scale | PennSIVE/collaborator/alumni | Gardner et al. (2025), *ComBatLS: A Location- and Scale-Preserving Method for Multi-Site Image Harmonization*. ([Paper](https://doi.org/10.1002/hbm.70197)) |
| DeepComBat | Deep learning-based harmonization | PennSIVE/collaborator/alumni | Hu et al. (2024), *DeepComBat: A statistically motivated, hyperparameter-robust, deep learning approach to harmonization of neuroimaging data*. ([Paper](https://doi.org/10.1002/hbm.26708)) |
| Generalized ComBat | Multimodal distributions and multiple batch effects | PennSIVE/collaborator/alumni | Horng et al. (2022), *Generalized ComBat harmonization methods for radiomic features with multi-modal distributions and multiple batch effects*. ([Paper](https://doi.org/10.1038/s41598-022-08412-9)) <br><br> Horng et al. (2022), *Improved generalized ComBat methods for harmonization of radiomic features*. ([Paper](https://doi.org/10.1038/s41598-022-23328-0)) |
| Distributed ComBat | Harmonization when data cannot be centrally pooled | PennSIVE/collaborator/alumni | Chen et al. (2022), *Privacy-preserving harmonization via distributed ComBat*. ([Paper](https://doi.org/10.1016/j.neuroimage.2021.118822)) |
| FC Harmony | Harmonization of functional connectivity matrices | PennSIVE/collaborator/alumni | Yu et al. (2018), *Statistical harmonization corrects site effects in functional connectivity measurements from multi-site fMRI data*. ([Paper](https://doi.org/10.1002/hbm.24241)) <br><br> Chen et al. (2021), *Harmonizing Functional Connectivity Reduces Scanner Effects in Community Detection*. ([Paper](https://doi.org/10.1101/2021.12.03.469269)) |
| ComBat-Predict | Generalization/prediction at new sites | PennSIVE/collaborator/alumni | Xin et al. (2026), *ComBat-Predict Enhances Generalizability of Neuroimaging Models to New Sites*. ([Paper](https://doi.org/10.1002/hbm.70546)) |

# Further Resources

## ComBatFamQC

ComBatFamQC is an R package that integrates batch effect diagnosis, data harmonization, and post-harmonization analysis. It includes ComBat, Longitudinal ComBat, ComBat-GAM, and CovBat, along with interactive visualizations that are helpful for going through different ComBat models for the first time.

ComBatFamQC. ([Website](https://zheng206.github.io/ComBatQC-Web/))

## ComBatFamily

ComBatFamily provides information on installation, usage, and publications for several ComBat-family packages, including ComBat, CovBat, ComBat-GAM, Longitudinal ComBat, ComBatLS, and ComBat-Predict.

ComBatFamily is currently maintained by Andrew Chen.

ComBatFamily. ([GitHub](https://github.com/andy1764/ComBatFamily))

## FC Harmony

FC Harmony provides methods for harmonization of functional connectivity matrices and is currently maintained by Andrew Chen.

FC Harmony. ([GitHub](https://github.com/andy1764/FCHarmony))

## Distributed ComBat

Distributed ComBat provides an implementation of ComBat for settings in which data are distributed across sites and cannot be centrally pooled. It is currently maintained by Andrew Chen.

Distributed ComBat. ([GitHub](https://github.com/andy1764/Distributed-ComBat))

# Ongoing and Future Work

Neel M. Desai and collaborators have developed a ComBat-based method for harmonizing longitudinal multi-scanner neuroimaging data with non-linear biological associations.

Desai et al., Harmonization of Longitudinal Multi-Scanner Neuroimaging Data with Non-Linear Biological Associations. (Paper)