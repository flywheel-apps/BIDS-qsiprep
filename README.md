[
# BIDS-QSIPREP

## About
qsiprep configures pipelines for processing diffusion-weighted MRI (dMRI) data. The main
 features of this software are 
 
1. A BIDS-app approach to preprocessing nearly all kinds of
 modern diffusion MRI data. Automatically generated preprocessing pipelines that
 correctly group, distortion correct, motion correct, denoise, coregister and resample
 your scans, producing visual reports and QC metrics.

1. A system for running state-of-the-art reconstruction pipelines that include algorithms
from Dipy, MRTrix, DSI Studio and others.

1. A novel motion correction algorithm that works on DSI and random q-space sampling
schemes


### Preprocessing 
The preprocessing pipelines are built based on the available BIDS inputs, ensuring that fieldmaps are handled correctly. The preprocessing workflow performs head motion correction, susceptibility distortion correction, MP-PCA denoising, coregistration to T1w images, spatial normalization using ANTs and tissue segmentation.

[Read more](https://qsiprep.readthedocs.io/en/latest/)


## Overview
This gear can only be run on datasets that have been BIDS curated
and can pass the tests of the [BIDS
Validator](https://github.com/bids-standard/bids-validator).
BIDS curation can be done before uploading data to the Flywheel
platform, or DICOM files can be uploaded and BIDS curation can be
done on the platform (see Setup below).

This Gear requires a (free) Freesurfer license. The license can be
provided to the Gear in 3 ways. See [How to include a Freesurfer
license file](https://docs.flywheel.io/hc/en-us/articles/360013235453).

This gear can run at the project, subject or session
level.  Because files are in the BIDS format, all of the proper
files will be used for the given session, subject, or separately,
by subject, for the whole project.

## Setup: BIDS Curation
Before running BIDS curation on your data, you must first prepare
your data with the following steps:

1. Run the [SciTran: DICOM MR
Classifier](https://github.com/scitran-apps/dicom-mr-classifier)
gear on all the acquisitions in your dataset.  This step extracts
the DICOM header info, and store it as Flywheel Metadata.

1. Run the [DCM2NIIX: dcm2nii DICOM to NIfTI
converter](https://github.com/scitran-apps/dcm2niix) gear on all
the acquisitions in your dataset.  This step generates the Nifti
files that this gear needs from the DICOMS.  It also copies all
flywheel metadata from the DICOM to the Nifti file (In this case,
all the DICOM header information we extracted in step 1).

1. Run the [curate-bids gear](https://github.com/flywheel-apps/curate-bids)
on the project.  More information about BIDS Curation on Flywheel
can be found
[here](https://docs.flywheel.io/hc/en-us/articles/360008162154-BIDS-Overview)
and running the BIDS curation gear is described
[here](https://docs.flywheel.io/hc/en-us/articles/360009218434-BIDS-Curation-Gear).
If you need to rename sessions or subjects before curation, you may
find the gear helpful:
[bids-pre-curate](https://github.com/flywheel-apps/bids-pre-curate).

1. Run the gear on a session, subject, or project.

Steps 1 and 2 can be automatically carried out as [gear
rules](https://docs.flywheel.io/hc/en-us/articles/360008553133-Project-Gear-Rules).

These steps MUST be done in this order.  Nifti file headers have
significantly fewer fields than the DICOM headers.  File metadata
will be written to .json sidecars when the files are exported in
the BIDS format as expected by the BIDS App which is run by this gear.

## Running:
To run the gear, select a
[project](https://docs.flywheel.io/hc/en-us/articles/360017808354-EM-6-1-x-Release-Notes),
[subject](https://docs.flywheel.io/hc/en-us/articles/360038261213-Run-an-analysis-gear-on-a-subject)
or
[session](https://docs.flywheel.io/hc/en-us/articles/360015505453-Analysis-Gears).

Instead of running at the project level which will sequentially
step through each subject, you can launch multiple jobs on subjects
or sessions in parallel.  An example of running the gear on a subject
using the Flywheel SDK is in this [notebook](notebooks/run-gear.ipynb).
More details about running gears using the SDK can be found in this
[tutorial](https://gitlab.com/flywheel-io/public/flywheel-tutorials/-/blob/ad392d26131ef22408423b5a5c14104253d53cd6/python/batch-run-gears.ipynb).

# Run Level
This can run at the
[project](https://docs.flywheel.io/hc/en-us/articles/360017808354-EM-6-1-x-Release-Notes),
[subject](https://docs.flywheel.io/hc/en-us/articles/360038261213-Run-an-analysis-gear-on-a-subject) or
[session](https://docs.flywheel.io/hc/en-us/articles/360015505453-Analysis-Gears) level.

# Inputs
### bidsignore (optional)
A list of patterns (like .gitignore syntax) defining files that should be ignored by the
bids validator.

### freesurfer_license (optional)
Your FreeSurfer license file. [Obtaining a license is free](https://surfer.nmr.mgh.harvard.edu/registration.html).
This file will be copied into the $FSHOME directory.  There are [three ways](https://docs.flywheel.io/hc/en-us/articles/360013235453-How-to-include-a-Freesurfer-license-file-in-order-to-run-the-fMRIPrep-gear-)
to provide the license to this gear.  A license is required for this gear to run.
  
### recon-spec
json file specifying a reconstruction pipeline to be run after preprocessing.
  
### eddy-config
path to a json file with settings for the call to eddy. If no json is specified, a default one will be used. The current default json can be found here: https://github.com/PennBBL/qsiprep/blob/master/qsiprep/data/eddy_params.json
  
  
# Configuration Options
Note: arguments that start with "gear-" are not passed to the BIDS App.

### n_cpus (optional)
Number of CPUs/cores use.  The default is all available.

### verbose (optional)
Verbosity argument passed to the BIDS App.

### gear-run-bids-validation (optional)
Gear argument: Run bids-validator after downloading BIDS formatted data.  Default is true.

### gear-ignore-bids-errors (optional)
Gear argument: Run BIDS App even if BIDS errors are detected when gear runs bids-validator.

### gear-log-level (optional)
Gear argument: Gear Log verbosity level (INFO|DEBUG)

### gear-save-intermediate-output (optional)
Gear argument: The BIDS App is run in a "work/" directory.  Setting this will save ALL
contents of that directory including downloaded BIDS data.  The file will be named
"\<BIDS App>_work_\<run label>_\<analysis id>.zip"

### gear-intermediate-files (optional)
Gear argument: A space separated list of FILES to retain from the intermediate work
directory.  Files are saved into "\<BIDS App>_work_selected_\<run label>_\<analysis id>.zip"

### gear-intermediate-folders (optional)
Gear argument: A space separated list of FOLDERS to retain from the intermediate work
directory.  Files are saved into "\<BIDS App>_work_selected_\<run label>_\<analysis id>.zip"

### gear-dry-run (optional)
Gear argument: Do everything except actually executing the BIDS App.

### gear-keep-output (optional)
Gear argument: Don't delete output.  Output is always zipped into a single file for
easy download.  Choose this option to prevent output deletion after zipping.

### gear-FREESURFER_LICENSE (optional)
Gear argument: Text from license file generated during FreeSurfer registration.
Copy the contents of the license file and paste it into this argument.

### bids_app_args
Additional boolean arguments to be passed to QSI prep

### interactive-reports-only
create interactive report json files on already preprocessed data.
  
### acquisition-type
select a specific acquisition type to be processed.
  
### anat-only
run anatomical workflows only.
  
### dwi-only
ignore anatomical (T1w/T2w) data and process DWIs only.
  
### infant
configure pipelines to process infant brains.
  
### boilerplate
generate boilerplate only.
  
### longitudinal
treat dataset as longitudinal - may increase runtime.
  
### b0-threshold
any value in the .bval file less than this will be considered a b=0 image. Current default threshold = 100; this threshold can be lowered or increased. Note, setting this too high can result in inaccurate results.
  
### dwi-denoise-window
window size in voxels for dwidenoise. Must be odd (default: 5). If 0, dwidwenoise will not be run.
  
### unringing-method
Possible choices: none, mrdegibbs

Method for Gibbs-ringing removal.
none: no action

mrdegibbs: use mrdegibbs from mrtrix3 (default: none).

### dwi-no-biascorr
skip b0-based dwi spatial bias correction.
  
### no-b0-harmonization
skip re-scaling dwi scans to have matching b=0 intensities.
  
### denoise-after-combining
run dwidenoise after combining dwis. Requires --combine-all-dwis.
  
### combine-all-dwis
[DEPRECATED] combine dwis from across multiple runs for motion correction and reconstruction..
  
### separate-all-dwis
don’t attempt to combine dwis from multiple runs. Each will be processed separately.
  
### distortion-group-merge
Possible choices: concat, average, none

How to combine images across distorted groups.
* concatenate: append images in the 4th dimension
* average: if a whole sequence was duplicated in both PE
* directions, average the corrected images of the same q-space coordinate

none: Default. Keep distorted groups separate
### write-local-bvecs
write a series of voxelwise bvecs, relevant if writing preprocessed dwis to template space.
  
### output-space
Possible choices: T1w, template

volume and surface spaces to resample dwis into
* T1w: subject anatomical volume
* template: deprecated. Will be ignored
  
### template
Possible choices: MNI152NLin2009cAsym

volume template space (default: MNI152NLin2009cAsym)
  
### output-resolution
the isotropic voxel size in mm the data will be resampled to after preprocessing. If set to a lower value than the original voxel size, your data will be upsampled using BSpline interpolation.

### b0-to-t1w-transform
Possible choices: Rigid, Affine

Degrees of freedom when registering b0 to T1w images. 6 degrees (rotation and translation) are used by default.
  
### intramodal-template-iters
Number of iterations for finding the midpoint image from the b0 templates from all groups. Has no effect if there is only one group. If 0, all b0 templates are directly registered to the t1w image.
  
### intramodal-template-transform
Possible choices: Rigid, Affine, BSplineSyN, SyN

Transformation used for building the intramodal template.
  
### b0-motion-corr-to
Possible choices: iterative, first

align to the “first” b0 volume or do an “iterative” registration of all b0 images to their midpoint image (default: iterative)
  
### hmc-transform
Possible choices: Affine, Rigid

transformation to be optimized during head motion correction (default: affine)
  
### hmc-model
Possible choices: none, 3dSHORE, eddy

model used to generate target images for hmc. If “none” the non-b0 images will be warped using the same transform as their nearest b0 image. If “3dSHORE”, SHORELine will be used. If “eddy_ingress”, the dwis are assumed to have been run through fsls eddy.
  
### shoreline-iters
number of SHORELine iterations. (default: 2)
  
### impute-slice-threshold
impute data in slices that are this many SDs from expected. If 0 (default), no slices will be imputed

  
### skull-strip-template
Possible choices: OASIS, NKI

select ANTs skull-stripping template (default: OASIS)
  
### skull-strip-fixed-seed
do not use a random seed for skull-stripping - will ensure run-to-run replicability when used with –omp-nthreads 1
  
### skip-t1-based-spatial-normalization
skip running the t1w-based normalization to template space. Default is to run the normalization.
  
### do-reconall
Run the FreeSurfer recon-all pipeline
  
### prefer-dedicated-fmaps
forces unwarping to use files from the fmap directory instead of using an RPEdir scan from the same session.
  
### fmap-bspline
fit a B-Spline field using least-squares (experimental)
  
### fmap-no-demean
do not remove median (within mask) from fieldmap (default: True)
  
### use-syn-sdc
EXPERIMENTAL: Use fieldmap-free distortion correction. To use this option, “template” must be passed to –output-space
  
### force-syn
EXPERIMENTAL/TEMPORARY: Use SyN correction in addition to fieldmap correction, if available
  
### reports-only
only generate reports, don’t run workflows. This will only rerun report aggregation, not reportlet generation for specific nodes.
  
### participant_label
a space delimited list of participant identifiers or a single identifier (the sub- prefix can be removed)
  
 
### verbose
  "description": "increases log verbosity for each occurrence, debug level is -vvv

### mem_mb
Maximum memory to use (MB). default 10240
  
### write-graph
Write workflow graph
  
### ignore
Ignore selected aspects of the input dataset to disable corresponding parts of the workflow (a space delimited list)  Possible choices: fieldmaps, slicetiming, sbref
  
