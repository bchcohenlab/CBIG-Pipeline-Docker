# CBIG-Pipeline-Docker

Implementing CBIG_fMRI_Preproc2016 in a docker 

This dockerfile includes CBIG_fMRI_Preproc2016, ANTS 2.2.0, AFNI, fsl-5.0.10, freesurfer-6.0.0, matlabruntime 2021a, and Connectome Workbench.
A full license for freesurfer is needed and must be put in the dockerfile. 

The necessary compiled m files as well the the scripts made specifically for the docker are included in the dockerfile.
The only thing that needs to get run before CBIG get started is the `setup_docker_CBIG.sh` file in the `/CBIG_compiled-for-MCR folder` which makes some changes that could not be cloned over from the repo. 

