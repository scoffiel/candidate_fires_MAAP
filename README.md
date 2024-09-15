# candidate_fires_MAAP
Code for extracting active &amp; candidate fire detections on the cloud for the EIS Fire / FEDS fire tracking efforts

User guide to working with VIIRS (and MODIS) data + candidate fires on MAAP
Shane Coffield
August 2024

Jupyter notebooks

Environment: all required packages are included in the MAAP pangeo workspaces, except pyhdf which is only needed for MODIS data and can be installed from conda-forge

Standard product VIIRS detections generation - “viirs_L2_extraction.ipynb”
Download NOAA21 preliminary standard product via wget
Extract standard active fire detections from 3 sensors and save as geoparquet
Need to adjust VZA and acq_datetime columns

Candidate fire extraction & visualization for any space/time - “candidate_fire_extraction.ipynb”
Download NOAA21 swaths as needed
Fetch swaths from s3 & extract active fires + candidates; save as csv (in progress)
Optional features:
L1 IR imagery and figures
Extract candidate fires from NRT data (https) - lower priority, if needed
Need to improve day/night classification

HLS fire algorithm (in progress)
Latency should improve in future
Need to fix workspace/libraries issue

Canadian pyroCbs case study
Still need to incorporate True Color
Adapted day/night thresholds for dawn problem at high latitudes

Amazon fire detections VIIRS + MODIS 
Includes MapBiomas forest cover masking via GoogleEE
Scripts
“amazon_viirs_extraction.ipynb”
“amazon_modis_extraction.ipynb”
“amazon_combined_plots.ipynb” - lonboard interactive plots
Outputs: /projects/shared-buckets/coffield/[viirs or modis]/outputs/amazon
[year]_[month]_detections_[sat].csv contains ALL known + candidate fires, though a column indicates which are forest vs non-forest
[year]_[month]_detections_[sat]_masked_filter.csv contains only FOREST known + candidate fires, with a column indicating which candidates pass the 4km/4day filter


Western US candidate fires
Preprint: https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4887584 
Code & dataset: https://github.com/scoffiel/candidate_fires/ 

FEDS workflow - loose ends
How to use FIRMS API? Should get URT+RT+NRT automatically
Get Tess/others permission to access NOAA21 on DAAC
How to use EarthData Login and refresh tokens every 60 days
https://ladsweb.modaps.eosdis.nasa.gov/archive/allData/4014/VJ214IMG/ 
Missing fire type (presumed vegetation, volcano, static, water) produced by FIRMS archive product VNP14IMGT
Watch out for dawn cases at high latitudes (fix exists in pyroCb script)


Background

What are candidate fires - see preprint https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4887584 

Working with VIIRS data (standard product, not NRT) - what, where, how
LAADS DAAC hosts the L1 data, available on s3
L1A geolocation
Products V[NP/J1/J2]03IMG
Required to get lat/lon arrays for candidate fires or imagery
L1B science data (IR imagery)
Products V[NP/J1/J2]02IMG
LP DAAC hosts the L2 fire data, available on s3 (except NOAA21)
L2 fire product
Products V[NP/J1/J2]14IMG
Can be read via direct access permanent session with MAAP (no login)
Contain two 2D arrays: fire mask and algorithm QA
Contain vector arrays of fire pixel locations, times, FRP, etc
Note for NOAA21: VJ214IMG is not yet on s3; still in development and accessible with special permission via LAADS

Working with VIIRS data (NRT) 
FIRMS API
LANCE nrt3 
Https access to swaths on LANCE if needed

Working with MODIS data (standard product)
Available on s3, but HDF-EOS (HDF4) format does not allow for reading in-place
Must download to local and use pyhdf package to open
Future Collection 7 should be switching to HDF5!

Working with HLS data
