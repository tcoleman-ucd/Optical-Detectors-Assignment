Optical-Detectors-Assignment
Author:Ta'Nasia Coleman
Notebook	Data:
Complete_image_calibration.ipynb	Hubble Space Telescope (HST) WFPC2 exposures	Calibrates raw images step by step and compares the result with the archive's "science-ready" images
GoogleEarthCalobration.ipynb	Google Earth images and Sentinel-2 imagery of Hunga Tonga-Hunga Ha'apai	Measures land area and vegetated area over time and compares our own vegetation index with the one supplied by the Sentinel-2 pipeline


1. HST image calibration (Complete_image_calibration.ipynb)
Goal

Reduce three raw WFPC2 exposures (ubai2505m, ubai2507m, ubai2508m, filter F336W) using the supplied calibration reference files, then check how close our calibrated images are to the archive's science-ready *_c0m_aligned.fits files.

What the notebook does
Reads the raw exposures and the calibration files (mask, bias level, bias, dark, flat-field) from hst-data/.
Applies one function per correction: mask, bias level, bias, dark and flat-field. The ADC and shutter-shading (SHADFILE) steps are skipped.
Converts to physical units with the photometric keywords (PHOTFLAM, PHOTZPT, ZP_CORR) and exposure time, taken from the science-ready header because the raw header's photometry table is blank.
Works on a single chip (chip 0).
Estimates the sky background.
Produces a multi-panel figure showing the image at every correction stage, plus histograms and difference plots comparing our calibrated image with the science-ready one.
Reports quantitative comparison statistics for each exposure.

2. Hunga Tonga land and vegetation (GoogleEarthCalobration.ipynb)
Goal

Track how the land area and the vegetated area of Hunga Tonga-Hunga Ha'apai changed over time, and explain, quantitatively, how our own vegetation index differs from the Sentinel-2 pipeline product.

Part A: Sentinel-2 vegetated area
Computes our own NDVI = (B8 − B4) / (B8 + B4) from the raw red (B04) and near-infrared (B08) bands.
Converts the angular pixel size to metres using the image metadata: res gives the pixel size in degrees, the image's centre latitude gives the east-west scale (cos(latitude)), and 111,132 m per degree of latitude gives the north-south scale.
Counts pixels with NDVI > 0.25 as vegetation and converts the count to km².
Part B: Google Earth land area
Pixel size comes from the scale bar in the images (600 m = 309 px).
Land is anything that is not water-coloured (water is bluer than it is red). The mask is smoothed, cleaned with a morphological opening, and small blobs are removed.
The bottom strip of each image (scale bar and text overlay) is cropped, and a region of interest can exclude tile seams in the imagery.
A quality-check figure shows every image with its land outline so each mask can be checked by eye.
Part C: Compare and contrast with the pipeline product

The "NDVI" file supplied with the Sentinel-2 data is a 3-band RGB picture so values can't be subtracted pixel by pixel. So the notebook builds a vegetation mask from the pipeline image (pixels clearly greener than red and blue) and compares it with our NDVI mask:

Measure	What it tells us
Vegetated area (ours vs pipeline), km²	How far the two products disagree on the headline number
Pixel agreement
Mean NDVI inside vs outside the pipeline vegetation
Disagreement map	Where the two masks differ (red = only ours, blue = only pipeline)
Outputs

Everything is saved to sentinel-processed/:

one vegetation-comparison figure per Sentinel-2 date,
the Google Earth land-mask QA grid,
the time-series plot (land area and vegetated area, with the January 2022 eruption marked),
quantitative_comparison.txt.
Known limitations
The pipeline mask is a colour threshold, so it will not match NDVI > 0.25 exactly, and the choice of green margin affects the comparison.
White clouds and sea spray look like white sand in colour, so they add some uncertainty to the Google Earth land area.
Some Google Earth images contain tile seams where the imagery source changes


How to run
Run the Jupyter Notebook
Make sure the hst-data files are all in one file in the same folder as the notebook
For the Hunga Tonga notebook, check the Google Earth QA figure first. If a land outline is wrong, adjust the settings at the top of the notebook (WATER_BLUE_MARGIN, ROI_OVERRIDES, CROP_BOTTOM_PX) and run again.
