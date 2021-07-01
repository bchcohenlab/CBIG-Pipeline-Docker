# M file Log for CBIG
Each script is listed out that is need for CBIG and under each script is the M files needed for it. All of them are compiled. 
The code underneath is the code that is changed in the script file to call that m file or the m file itself.

The line numbers are for the scripts where the code was changed. This is based on the scripts found in github repo before the changes were put in place.
The ln commands create a new link to the new executables in a different folder. Already put into the compile script.
- Added -censor to testing.config under regression
- Censor is in the regression step. If you don't want censoring increase the threshold

**CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/CBIG_preproc_fslmcflirt_outliers.csh**
*CBIG_preproc_plot_mcflirt_par.m and CBIG_preproc_DVARS_FDRMS_Correlation.m*: (CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) Line 202 
```
set cmd = ( ${root_dir}/utilities/CBIG_preproc_plot_mcflirt_par $mc_par_file $mc_abs_rms_file $mc_rel_rms_file $qc $outname_prefix );
CBIG_preproc_DVARS_FDRMS_Correlation.m & CBIG_preproc_motion_outliers.m:(CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) Line 261 
```
```
set cmd = ( ${root_dir}/utilities/CBIG_preproc_DVARS_FDRMS_Correlation $dvars_file $fd_file $output; ${root_dir}/utilities/CBIG_preproc_motion_outliers    $dvars_file $fd_file $fd_th $dv_th $discard_seg $output);
```  

**CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/CBIG_preproc_bandpass_fft.csh** \
*CBIG_bandpass_vol.m:* (was in different folder than others and it required a link)
(CBIG/utilities/matlab/filtering) Line 126
  
    ${root_dir}/utilities/CBIG_bandpass_vol $fMRI_file $output_file $low_f $high_f $detrend $retrend $censor_file |& tee -a $LF

- Change the " " to skip set censor_file = "skip" Line 113 in script
- Took out this if statement Line 117 from m file
`if (~exist('censor_file')) error('ERROR: censor_file does not exist.')`
```
ln -s /fileserver/caladan_ssd/repos/CBIG_compiled/utilities/matlab/filtering/CBIG_bandpass_vol /fileserver/caladan_ssd/repos/CBIG_compiled/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/CBIG_bandpass_vol
```
*CBIG_bpss_by_regression.m*
(CBIG/utilities/matlab/filtering)
```
ln -s /fileserver/caladan_ssd/repos/CBIG_compiled/utilities/matlab/filtering/CBIG_bpss_by_regression /fileserver/caladan_ssd/repos/CBIG_compiled/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/CBIG_bpss_by_regression
```
**Dont need to change anything in the script but this is needed for CBIG_bandpass_vol.m** 

*CBIG_glm_regress_matrix.m* \
(CBIG/utilities/matlab/stats) 
 ```
ln -s /fileserver/caladan_ssd/repos/CBIG_compiled/utilities/matlab/stats/CBIG_glm_regress_matrix /fileserver/caladan_ssd/repos/CBIG_compiled/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/CBIG_glm_regress_matrix
```
**Dont need to change anything in the script but this is needed for CBIG_bpss_by_regression.m and CBIG_glm_regress_vol.m**

**CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/CBIG_preproc_regression.csh** \
*CBIG_preproc_create_mc_regressors.m:* \
(CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) Line 348 
```    
set cmd = ( ${root_dir}/utilities/CBIG_preproc_create_mc_regressors $mc_par_list $mc_regressor_list $detrend_method $mt_diff_flag); \
```

*CBIG_preproc_create_ROI_regressors.m:* \
(CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) Line 369
```
set cmd = ( ${root_dir}/utilities/CBIG_preproc_create_ROI_regressors $fMRI_list $ROI_regressors_list $wb_mask $wm_mask $csf_mask $other_diff_flag);
```
*CBIG_preproc_aCompCor_multipleruns.m:*
(CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) Line 399
```
set cmd = ( ${root_dir}/utilities/CBIG_preproc_aCompCor_multipleruns $fMRI_list $wm_csf_mask_comb $CompCor_regressors_list $nPCs $per_run $aCompCor_diff_flag); \
```
*CBIG_glm_regress_vol.m:* (was in different folder than others and it required a link)
(CBIG/utilities/matlab/filtering) Line 485
```
set cmd = ( ${root_dir}/utilities/CBIG_glm_regress_vol $fMRI_list $resid_list $all_regressors_list $polynomial_fit $censor_list $per_run);
```

```
ln -s /fileserver/caladan_ssd/repos/CBIG_compiled/utilities/matlab/stats/CBIG_glm_regress_vol /fileserver/caladan_ssd/repos/CBIG_compiled/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/CBIG_glm_regress_vol
```

**CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/CBIG_preproc_censor.csh**
*CBIG_preproc_censor_wrapper.m:* (CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) Line 182 
```
set cmd = (${root_dir}/utilities/CBIG_preproc_censor_wrapper ${BOLD}.nii.gz $outlier_file $TR $output_inter $output $loose_mask $max_mem);
set cmd = (${root_dir}/utilities/CBIG_preproc_censor_wrapper ${BOLD}.nii.gz $outlier_file $TR $output_inter $output $loose_mask $max_mem $low_f $high_f);
```
*CBIG_preproc_censor.m*
(CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) \
**Dont need to change anything in the script but this is needed for CBIG_preproc_censor_wrapper.m**

*CBIG_preproc_CensorQC.m*
(CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) Line 240 
 ```
 ${root_dir}/utilities/CBIG_preproc_CensorQC $qc_out_dir $subject $runfolder ${BOLD}.nii.gz ${censor_inter} $censor_final ${sub_dir}/${subject}/bold/mask/${subject}.brainmask.bin.nii.gz ${sub_dir}/${subject}/bold/mask/${subject}.func.gm.nii.gz $outlier_file |& tee -a $LF
```
*CBIG_preproc_CensorCorrAndFracDiff.m*
(CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) \
**Dont need to change anything in the script but this is needed for CBIG_preproc_CensorQC.m**

**CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/CBIG_preproc_QC_greyplot.csh**
*CBIG_preproc_create_ROI_regressors.m*
(CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) Line 168
```
set cmd = (${root_dir}/utilities/CBIG_preproc_create_ROI_regressors $fMRI_list $GS_list $wb_mask 0);
```

*CBIG_preproc_QC_greyplot.m*
(CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) Line 197
```
set cmd = (${root_dir}/utilities/CBIG_preproc_QC_greyplot $fmri_file $FD_file $DV_file $output)
set cmd = ($cmd GM_mask $gm_mask WB_mask $wb_mask grey_vox_factor $grey_vox_fac) 
set cmd = ($cmd tp_factor $tp_fac FD_thres $FD_th DV_thres $DV_th);

set cmd = (${root_dir}/utilities/CBIG_preproc_QC_greyplot $fmri_file $FD_file $DV_file $output)
set cmd = ($cmd GM_mask $gm_mask WBS_text $GS_txt grey_vox_factor $grey_vox_fac)
set cmd = ($cmd tp_factor $tp_fac FD_thres $FD_th DV_thres $DV_th);
```

**CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/CBIG_preproc_native2fsaverage.csh**
*CBIG_preproc_fsaverage_medialwall_fillin*
(CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) Line 199
```    
${root_dir}/utilities/CBIG_preproc_fsaverage_medialwall_fillin $hemi fsaverage6 $input1 $input2 $tmp_output |& tee -a $LF
```

*CBIG_preproc_set_medialwall_NaN*
(CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/utilities/) Line 297
```
${root_dir}/utilities/CBIG_preproc_set_medialwall_NaN $hemi $out_mesh $before_NaN_name $after_NaN_name |& tee -a $LF
```
