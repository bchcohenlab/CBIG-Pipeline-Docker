# CBIG-Pipeline-Docker

Implementing CBIG_fMRI_Preproc2016 in a docker 

This dockerfile includes CBIG_fMRI_Preproc2016, ANTS 2.2.0, AFNI, fsl-5.0.10, freesurfer-6.0.0, matlab 2022b, and Connectome Workbench.
A full license for freesurfer and matlab is needed and must be put in the dockerfile. 

## **Building the image:**
The image takes around 50 min to build if it is on a brand new machine
In the dockerfile there is a command at the end to copy the freesurfer license into the image. The freesurfer license file is in the folder with the dockerfile. That will need to be changed depending on the machine

## **Starting the container:**
An example command to start the container in a bash is
```
docker run --name CBIG_docker --rm -i -t  -v /fileserver/gammu/collections/GSP:/BIDS_input -v /fileserver/gammu/projects/GSP/freesurfer_v4.5.0:/freesurfer_input -v /fileserver/gammu/projects/GSP_CBIG_docker:/output -v /fileserver/caladan_ssd/repos/CBIG_compiled/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities:/extra_files [docker image id/name] bash
```

The image id/name would need be changed based on the image created
Once the container is started, run the `setup_docker_CBIG.sh` which is in the `/CBIG_compiled-for-MCR` folder
The setup script, as of writing, changes the links for compiled m files and copies large compiled m files from caladan over to the docker image
It also installs the directory for Connectome Workbench

## **Running CBIG:**
An example command to run CBIG in bash is 
    
    /BIDS_to_CBIG_fMRI_Preproc2016/BIDS_2BP_Thomas_docker_CBIG_fMRI_preprocess.sh \
    /BIDS_input \
    sub-0002 \
    /freesurfer_input \
    /output \
    /BIDS_to_CBIG_fMRI_Preproc2016/configs/testing.config 

The folders for the inputs and outputs are based on the docker run command up above
To see the logs with this command you can go to `/output/CBIG_fMRI_preprocess_testing/sub-002/logs` or on the server with `/fileserver/gammu/projects/GSP_CBIG_docker/CBIG_fMRI_preprocess_testing/sub-0002/logs`
To redo a subject, if you want to delete the entire CBIG_fMRI_preprocess_testing folder you must delete it through the bash, it won’t let you through the server.
- One thing to note which is mentioned in the `Mfile_log.md` is that `-censor` needs to be added to the config file that you are using in `CBIG_preproc_regress`
- An error will occur saying `CBIG_preproc_regress` failed

## **Things for the future**
- Remove spm and the matlab for it if it is decided that ultimately it is not needed
- Uncomment git-lfs if it is added to the main CBIG repo
- Change the `CBIG_docker_setup.sh` file in the CBIG compiled branch to remove the copy command for the files tracked by git-lfs
- In the Dockerfile remove the command to make the extra files folder since the files are not needed to copy over from the server
