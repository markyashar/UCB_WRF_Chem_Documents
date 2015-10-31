Here I will try to guide you through some of the main steps I used to include simulated anthropogenic CO2 inert (passive) tracer emissions in a nested WRF model over the Angelo Coast Range Reserve. As of this time I have not been successful in including biogenic CO2 emissions (which I am currently working on). Maybe you and/or Percy or someone else will be able to figure out how to include the biogenic CO2 tracer emissions using a similar method as for the anthropogenic CO2 fluxes/emissions described in this e-mail, and/or you will find an easier or more efficient way of doing so! So, I will mostly focus on the anthropogenic CO2 side of things here.

I don't know if what I've done is the best possible way to do this or if the results are 100% 'correct' 
or accurate (I haven't received much feedback from Dr. Fung on this so far), it's just the only thing
I've been able to do so far that seems to have included CO2 anthropogenic tracer emissions in
the WRF model. There are other methods/techniques that you could also try that may work better
and/or give better results.

(1) The first thing I did was to use the PREP-CHEM-SRC preprocessor (separate from WRF) to generate CO2 trace gas emission fields (one file for each hour of the simulation) for the nested domains of interest
in California. This preprocessor provides CO2 emission fields interpolated onto the transport model grid
with the use of Emission  Database for Global Atmospheric Research (EDGAR,
http://edgar.jrc.ec.europa.eu) anthropogenic CO2 emission data. More details about the PREP-CHEM_SRC  preprocessor tool can be found by looking through the paper at the following link:
http://www.geosci-model-dev.net/4/419/2011/gmd-4-419-2011.pdf. Further information about PREP-CHEM-SRC can be found on pages 29-31 of the document http://www.bgc-jena.mpg.de/mpg/websiteBiogeochemie/Publikationen/Technical_Reports/tech_report25.pdf
as well as pages 79-81 of ruc.noaa.gov/wrf/WG11/Users_guide.pdf. Different versions of the  PREP_CHEM_SRC software package and data can be downloaded from ftp://aftp.fsl.noaa.gov/divisions/taq/global_emissions. I have found that an older version or PREP_CHEM_SRC (found at ftp://aftp.fsl.noaa.gov/divisions/taq/global_emissions/Old/ has worked best for my purposes, but,
if I recall correctly, I think I needed to make some modifications to the Fortran code; I can give you
specific info. on which version I downloaded and necessary modifications that needed to be made
later as necessary). For now, it would probably be best to use or copy the PREP_CHEM_SRC software
package and data that I've been currently using in the following directory on Hopper:

/scratch/scratchdirs/yashar/Prep_sources_chem_cptec_wrf/

If you want, you can probably just copy this entire directory (and all of the files and subdirectories 
contained in) to your working directory.

The first thing to do would be to look at, copy/use and modify as you need the following two files in the
/scratch/scratchdirs/yashar/Prep_sources_chem_cptec_wrf/bin/ directory:

prep_chem_sources_script.sh
prep_chem_sources.inp

I believe they are currently set up to generate properly interpolated and/or re-gridded EDGAR CO2 anthropogenic emission fields during the month of February, 2006 for the WRF model I'm working on (nested domain over angelo coast range reserve). So, if you copy 
/scratch/scratchdirs/yashar/Prep_sources_chem_cptec_wrf/ to your working directory, you can generate the CO2 EDGAR emission fields by just typing in the following command:

./prep_chem_sources 

and after all of the netcdf (*.nc) files have been generated, enter the following:


rm *.bin *.ctl *.gra *.vfm

or, equivalently

sh prep_chem_sources.sh &
(instead of './prep_chem_sources).

The files generated will have names such as the following:

EDGAR-CO2_BIO_EMISSION-T-2006-02-05-250000-g2.nc
EDGAR-CO2_BIO_EMISSION-T-2006-02-05-250000-g1.nc
EDGAR-CO2_BIO_EMISSION-T-2006-02-05-240000-g1.nc
EDGAR-CO2_BIO_EMISSION-T-2006-02-05-240000-g2.nc

or

EDGAR-CO2_EMISSION-T-2006-02-27-190000-g1.nc
EDGAR-CO2_EMISSION-T-2006-02-27-190000-g2.nc
EDGAR-CO2_EMISSION-T-2006-02-27-180000-g1.nc
EDGAR-CO2_EMISSION-T-2006-02-27-180000-g2.nc

Such files can currently be found in the /scratch/scratchdirs/yashar/Prep_sources_chem_cptec_wrf/bin/ 
directory.