

VPRM (Vegetation Photosynthesis and Respiratoin Model) is a diagnositc model
that calculates Net Ecosystem Exchange (NEE) based on Moderate Resolution
Imaging Spectrometer (MODIS) satellite estimates of Land Surface Water Index
(LSWI) and Enhanced Vegetation Index (EVI), short wave radiation, and sufrace
temperature.

VPRM has been coupled to the WRF software (WRF-VPRM / WRF-Chem) to compute
biospheric fluxes. These fluxes are added on CO2 tracers being transported
in the WRF-VPRM/WRF-Chem model. WRF-VPRM/WRF-Chem uses VPRM input fields
created by the VPRM pre-processor for the purposes of providing the VPRM model
with the necessary satellite indices and vegetation fraction map. The VPRM
input data (e.g., the netcdf files 'VPRM_input_LSWI_2006.nc', 
'VPRM_input_EVI_2006.nc', etc.) together with CO2 fields (i.e.,
'ana96_v3.4_mix_2006.nc' -- global 3-D spatio-temporal CO2 concentration fields
calculated by the tracer transport model TM3) are passed to WRF-Chem using
matlab tools.

See http://www.bgc-jena.mpg.de/~christian.roedenbeck/download-CO2-3D/ for more
details regarding the 3-D spatio temporal CO2 concentration fields calculated
by the TM3 model.

