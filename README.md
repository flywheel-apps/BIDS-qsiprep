[![CircleCI](https://circleci.com/gh/flywheel-apps/bids-app-template.svg?style=shield)](https://app.circleci.com/pipelines/github/flywheel-apps/bids-app-template)

# bids-qsiprep

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

# Inputs
### bidsignore (optional)
A list of patterns (like .gitignore syntax) defining files that should be ignored by the
bids validator.

### freesurfer_license (optional)
Your FreeSurfer license file. [Obtaining a license is free](https://surfer.nmr.mgh.harvard.edu/registration.html).
This file will be copied into the $FSHOME directory.  There are [three ways](https://docs.flywheel.io/hc/en-us/articles/360013235453-How-to-include-a-Freesurfer-license-file-in-order-to-run-the-fMRIPrep-gear-)
to provide the license to this gear.  A license is required for this gear to run.

# Run Level
This can run at the
[project](https://docs.flywheel.io/hc/en-us/articles/360017808354-EM-6-1-x-Release-Notes),
[subject](https://docs.flywheel.io/hc/en-us/articles/360038261213-Run-an-analysis-gear-on-a-subject) or
[session](https://docs.flywheel.io/hc/en-us/articles/360015505453-Analysis-Gears) level.

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

# Workflow

# Outputs
