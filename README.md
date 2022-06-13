# auto_selfcal
The goal of this code is to be able to run on an a set of ALMA or VLA *_target.ms (or other MS files with the targets split out and having the same setup in each MS) files for single-pointing data and perform self-calibration using the continuum. If a cont.dat file is present, the code will flag the non-continuum regions and perform self-calibration on the continuum only.

This code can only be executed within the CASA 6.4+ environment, and is tested on CASA 6.4.0, 6.4.1, 6.4.2, 6.4.3, 6.4.4, and 6.5.0. CASA versions earlier that 6.4.0 are likely to encounter problems due to a gaincal bug that was fixed in CASA 6.4.0.

Standard ALMA and single-band VLA data are supported as are multiple targets.
Multi-band VLA is supported.
ALMA spectral scans may work, but have not been thoroughly tested.
Mosaics have only had limited testing, but some limited functionality might work, specifically small mosaics where there is emission of the same source(s) within each pointing.
Imaging will use nterms=2 for most VLA data and nterms =2 for most ALMA data. However, on a per-target basis, nterms=2 imaging will be used if S/N > 500, otherwise the full improvement possible from self-calibration may not be realized.
Amplitude self-calibration will be attempted after phase-only self-calibration. If amplitude self-cal is not desired, set the variable 'do_amp_selfcal' to False in the auto_selfcal.py script.

Brief instructions:
1. Create an empty directory
2. Copy into this directory the *_target.ms files that have identical setups (targets and spectral windows) to be self-calibrated (must contain only the targets desired for self-calibration and only TARGET observation intents)
4. Copy auto_selfcal.py and selfcal_helpers.py into this directory (and regenerate_weblog.py if needed)
5. Run script with mpicasa -n X casa -c auto_selfcal.py; X is the number of mpi threads to use

If serial operation is desired (without mpicasa), run with casa -c auto_selfcal.py

The script will automatically find your *_target.ms files, and determine all the parameters it needs for imaging and self-calibration of the science targets.

The script will output a file 'applycal_to_orig_MSes.py' to apply the self-calibration solutions back to the original MS such that the line data can also have the self-calibration solutions applied.

The script will output a file 'uvcontsub_orig_MSes.py' if a cont.dat file exists, excluding the same spectral regions for continuum fitting as were flagged for continuum self-calibration.

When finished summaries are generated for each target in weblog/index.html. Weblog generation may fail depending on what settings are cached for the viewer. If this is a problem you can remove all your cached casaviewer settings by running 'rm -rf ~/.casa/viewer'. Then you can run casa -c regenerate_weblog.py, the regenerate_weblog.py script is included in the repository and only generates a new weblog from the already stored images and metadata pickle files.

