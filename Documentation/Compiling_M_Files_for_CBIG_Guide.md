# Compiling M Files Guide

**How to compile m files?**
The command to compile m files is `mcc`
```
mcc -m -R -nodisplay -d [output directory] [m file]
```
This must be run in matlab.
A script can be created to compile the m files, but the script must be run on a computer that has matlab installed.
For example:
```
cat > build.m <<END
addpath(genpath('/fileserver/caladan_ssd/repos/CBIG_compiled'))
mcc -m -R -nodisplay -d stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities CBIG_preproc_plot_mcflirt_par.m
exit
END
matlab -nodisplay -nosplash -r build
echo “Done compiling!”
```
The add path can be added if the m file needs access to files in a different directory.
The compiled m files can be much bigger than the normal m files.
The compiled m files don’t have an extension at the end. 
If you want to make a change to the m file they have to be recompiled again.
Extra files and folders do created when the files get compiled but they are very small.
Once compiled, matlab does not need to be installed for the files to work.

**How to run compiled m files?**
To run a compiled m file by itself you need matlab compiler runtime or MCR installed.
For example: `./run_IO.sh /fileserver/caladan_ssd/opt/MCR/v910/`
Extra parameters can be added to the end if need be.
The run script there lets you run the file by itself.
The compiled file can be called on and run normally as it was a normal m file.
However the MCR which was installed on the original computer must be on the new machine that runs the files. 




