
1. Looking through the WRF and WRF/Chem code and documentation, it appears

as if the routines and files that are most relevant to CO2 tracer emissions,

fluxes, and concentration (not including those that are in the PREP_CHEM_SRC

pre-processing software package) are in the WRF-Chem software package. In my

case I have WRF-Chem-3.4 installed and the relevant routines and files are:


(a) /global/u2/y/yashar/hopper/WRF/WRFV3-Chem-3.4/Registry/registry.chem

The registry.chem file contains the names and dimensions of the chemical species used by the

WRF/Chem model.The gas phase chemistry variables

are in units of parts per million (ppm). The chemical emissions are in units of mole per square

kilometer per hour for the gaseous chemical species.

Some lines in this registry file that are relevant to some of the CO2 tracer emission and

concentration varaibles

(in, e.g., the various input, intermediate, and output NetCDF files) are:

# Anthropogenic emissions

state real e_co2 i+jf emis_ant 1 Z i5r "E_CO2"

"EMISSIONS" "mol km^-2 hr^-1"

# GHG emission variables

state real ebio_res ivjf eghg_bio 1 Z h "EBIO_RES"

"biospheric VPRM CO2 release" "mol km^-2 hr

^-1"

# additional arrays needed for biomass burning emissions input

state real ebu_in_co2 i]jf ebu_in 1 - i07 "ebu_in_co2"

"EMISSIONS" "mol km^-2 hr^-1"

#RACM has a few more variables to those of RADM2 (ETE is equivilant to OL2 from RADM2)

state real co2 ikjftb chem 1 - i0{12}rhusdf=(bdy_interp:dt) "co2" "CO2

concentration" "ppmv"

# GHG chem arrays, Chem Scalars for CO2 used in the GHG options

state real co2_ant ikjftb chem 1 - i0{12}rhusdf=(bdy_interp:dt)

"CO2_ANT" "mixing ratio of anthropogenic CO2" "ppmv"

etc ...


(b) /global/u2/y/yashar/hopper/WRF/WRFV3-Chem-3.4/frame/module_configure.f90

.

.


IF ( chem_index_table( PARAM_co2 , idomain ) .lt. 1 ) THEN

chem_num_table(idomain) = chem_num_table(idomain) + 1

P_co2 = chem_num_table(idomain)

chem_index_table( PARAM_co2 , idomain ) = P_co2

ELSE

P_co2 = chem_index_table( PARAM_co2 , idomain )

END IF

chem_boundary_table( idomain, P_co2 ) = .TRUE.

chem_dname_table( idomain, P_co2 ) = 'co2'

chem_desc_table( idomain, P_co2 ) = 'CO2 concentration'

chem_units_table( idomain, P_co2 ) = 'ppmv'

chem_streams_table( idomain, P_co2 )%stream(1) = 33554433

chem_streams_table( idomain, P_co2 )%stream(2) = 2097216

F_co2 = .TRUE.

....

IF (model_config_rec%chem_opt(

idomain)==16)THEN

IF ( chem_index_table( PARAM_co2_ant , idomain ) .lt. 1 ) THEN

chem_num_table(idomain) = chem_num_table(idomain) + 1

P_co2_ant = chem_num_table(idomain)

chem_index_table( PARAM_co2_ant , idomain ) = P_co2_ant

ELSE

P_co2_ant = chem_index_table( PARAM_co2_ant , idomain )

END IF

chem_boundary_table( idomain, P_co2_ant ) = .TRUE.

...

chem_dname_table( idomain, P_co2_ant ) = 'CO2_ANT'

chem_desc_table( idomain, P_co2_ant ) = 'mixing ratio of anthropogenic CO2'

chem_units_table( idomain, P_co2_ant ) = 'ppmv'

chem_streams_table( idomain, P_co2_ant )%stream(1) = 33554433

chem_streams_table( idomain, P_co2_ant )%stream(2) = 2097216

F_co2_ant = .TRUE.

etc..


(c) /global/u2/y/yashar/hopper/

WRF/WRFV3-Chem-3.4/chem/chemics_init.F

Among other things, this routine seems to set the background value for the

CO2 tracer concentration to 380 ppmv, i.e.,

.

.

!! Initialize some greenhouse gas species for 16th and 17th chemistry options:

!! CO2 mixing ratios for the background GHG tracers are set as a constant value.

!! Some spin-up is necessary to get spatial variability right!

!! Warning: initial values for CO2 components have to be subtracted from the output!

!! The purpose of this is to avoid negative values due to the biospheric uptake!

!! This subroutine is called at every initialization, however it's carried out if

!! restart=0 and chem_in_opt=0

ghg_block: IF (config_flags%gas_ic_opt==GAS_IC_GHG) THEN

IF( (.not. config_flags%restart) .AND. config_flags%chem_in_opt==0 ) THEN

! CO2 tracers:

do j=jts,jte

do k=kts,kte

do i=its,ite

chem(i,k,j,p_co2_bck)=380. ! background CO2 tracer

chem(i,k,j,p_co2_bio)=380. ! A user should subtract this from the output fields!

chem(i,k,j,p_co2_oce)=380. ! A user should subtract this from the output fields!

chem(i,k,j,p_co2_ant)=380. ! A user should subtract this from the output fields!

chem(i,k,j,p_co2_tst)=380. ! A user should subtract this from the output fields!

if (p_co2_bbu>1) chem(i,k,j,p_co2_bbu)=380. ! A user should subtract this from the

output fields

!

enddo

enddo

enddo


(d) /global/u2/y/yashar/hopper/WRF/WRFV3-Chem-3.4/chem/module_ghg_fluxes

This subroutine adds greenhouse gases to the chem species.

! March-28, this subroutine adds all type of greenhouse gases to the chem species

...

DO j=jts,jte

DO i=its,ite

! 3D anthropogenic fluxes

DO k=kts,min(config_flags%kemit,kte)

conv_rho=8.0461e-6/rho_phy(i,k,j)*dtstep/dz8w(i,k,j) ! 8.0461e-6=molar_mass(air)/

3600, [g/mol/s]

chem(i,k,j,p_co2_ant)= chem(i,k,j,p_co2_ant) + conv_rho* emis_ant(i,k,j,p_e_co2)

chem(i,k,j,p_co2_tst)= chem(i,k,j,p_co2_tst) + conv_rho* emis_ant(i,k,j,p_e_co2tst)

chem(i,k,j,p_co_ant) = chem(i,k,j,p_co_ant) + conv_rho* emis_ant(i,k,j,p_e_co)

! 2D biospheric fluxes:

if (k==1) then

chem(i,1,j,p_co2_bio)= chem(i,1,j,p_co2_bio) + conv_rho* (eghg_bio(i,1,j,p_ebio_gee) +

eghg_bio(i,1,

j,p_ebio_res)) ! both uptake and release

chem(i,1,j,p_co2_oce)= chem(i,1,j,p_co2_oce) + conv_rho* ebio_co2oce(i,j)

end if


(e) global/u2/y/yashar/hopper/WRF/WRFV3-Chem-3.4/chem/module_input_chem_data.F

case (CO2_TRACER,GHG_TRACER)

call wrf_debug("setup_profile_maps: nothing done for the GHG options")

....

ELSE IF ( si_grid%chem_opt==CO2_TRACER .OR. si_grid%chem_opt==GHG_TRACER )

THEN

! Do nothing, since for GHGs initialization is done in chemics_init.F

...

if( nch==p_co2_bck .OR. nch==p_co2_bio .OR. nch==p_co2_oce .OR. nch==p_co2_ant &

.OR. nch==p_co2_bbu .OR. nch==p_co2_tst ) then

chem = 380.

....

if (i_bdy_method .eq. 1) then

CALL bdy_chem_value ( &

chem(i,k,j), z(i,k,j), ic, numgas )

else if (i_bdy_method .eq. 9) then

CALL bdy_chem_value_racm( &

chem(i,k,j), z(i,k,j), ic, numgas,p_co2 )

else if (i_bdy_method .eq. 2) then

tempfac=(t(i,k,j)+t0)*((p(i,k,j) + pb(i,k,j))/p1000mb)**rcp

convfac=(p(i,k,j)+pb(i,k,j))/rgasuniv/tempfac

CALL bdy_chem_value_sorgam ( &

chem(i,k,j), z(i,k,j), ic, config_flags, &

.....

case (CO2_TRACER,GHG_TRACER) ! No gas chemistry or deposition for GHGs

get_last_gas = 0


2. I've installed a newer / more recent version of the PREP-CHEM-SRC pre-processing package

for generating CO2 tracer emission netcdf files for input into WRF simulations. It is in the

following directory

on hopper.

/scratch/scratchdirs/yashar/PREP-CHEM-SRC-1.3.2_zl/

The executable *.exe file and the shell script that can be used to generate the CO2 input

emission files are:


/scratch/scratchdirs/yashar/PREP-CHEM-SRC-1.3.2_zl/bin/

prep_chem_sources_RADM_WRF_FIM.exe

and

/scratch/scratchdirs/yashar/PREP-CHEM-SRC-1.3.2_zl/bin/prep_chem_sources_script_good.sh


(a) Just in case this is useful, there is an R script in this same ../bin/ directory

called "nc2ascii_co2.r" that can convert CO2 EDGAR emission netcdf files (that can be

downloaded from the EDGAR database website) to ascii (*.txt) files. So, for example, if you

download files of the form v41_*CO2*_TOT.0.1x0.1.nc you can use this R script to produce the

edg_co2_YYYY_total.txt files that are requiredd by the pre-processor. It may also be possible

to modify this script to produce take general netcdf files (not just EDGAR CO2 netcdf files) and

convert them to ascii files for other purposes.

After making the necessary modifications to this script, you can run it on hopper by issuing the

following commands:

module load R

nohup R CMD BATCH nc2ascii_co2.r nc2ascii_co2.log

I've also attached this R script to this e-mail.


(b) Note that for those who might be interested in trying to generate CO2 street emissions, or

CO2 emissions at street level, etc. (Holly), there is a routine within the PREP_CHEM_SRC

software called "streets_emissions.f90" that one might be able to modify to generate CO2 street

(level) emission netcdf files for use as input into WRF model runs. This subroutine is located in

the following directory:

/scratch/scratchdirs/yashar/PREP-CHEM-SRC-1.3.2_zl/src/

-----------------------------------------------------------------

ADDITIONAL NOTES

1. All of the WRF-Chem-3.4 code/routines that I'm using (including module_ghg_fluxes.F) are

located in the following directory: /global/u2/y/yashar/hopper/WRF/WRFV3-Chem-3.4/chem/


2. The input EDGAR ASCII and HDF5 CO2 emission files that were discussed in other notes

are, e.g.,:

(a) edg_CO2_2000_total.txt (EDGAR v32), which is located in the following directory

on Hopper: /scratch/scratchdirs/yashar/PREP-CHEM-SRC-1.2/Global_emissions_v3/

Emission_data/EDGAR/anthro/,

(b) EDGAR_2005.h5 (EDGAR v41), which is a HDF5 (Hierarchical Data Format) file ( -- see

http://en.wikipedia.org/wiki/Hierarchical_Data_Format for more info. about this file format), and

is located in the following directory on Hopper: /scratch/scratchdirs/yashar/PREP-CHEM-SRC-

1.2/Global_emissions_v3/Emission_data/EDGARV4/ You can view the contents of this file by

typing in "h5dump EDGAR_2005.h5" at the command prompt.

(c) edg41_CO2_2005_total.txt (EDGAR v41), located in the following directory on Hopper:

/scratch/scratchdirs/yashar/PREP-CHEM-SRC-1.2/Global_emissions_v3/Emission_data/

EDGARV4/ I've used the EDGAR v41 input data with the PREP-CHEM-SRC-1.3.2_zl code,

located at /scratch/scratchdirs/yashar/PREP-CHEM-SRC-1.3.2_zl/src/, and I've used the

EDGAR v32 data with an earlier version of the PREP-CHEM-SRC code, located at: /scratch/

scratchdirs/yashar/Prep_sources_chem_cptec_wrf/.


3. The "namelist.input" file that I've been using with the WRF model runs (using real.exe and

wrf.exe), along with the R script (edgar41_accum_acrr_test.r), are located in the following

directory on Hopper: /global/u2/y/yashar/hopper/WRF/WRFV3-Chem-3.4/test/em_real/


4. The "namelist.wps" file used in WRF pre-processing (WPS) is located here: /global/homes/

y/yashar/hopper/WRF/WPS/


5. As mentioned in previous notes, the "registry.chem" file contains the names and dimensions

of the chemical species used by the WRF-Chem model. The gas phase chemistry variables

are in units of parts per million (ppm). The chemical emissions are in units of mole per square

kilometer per hour for the gaseous chemical species. The "registry.chem" file is located in the

following directory on hopper: /global/u2/y/yashar/hopper/WRF/WRFV3-Chem-3.4/Registry/

The registry files are text files that allow us to put in things that are to be included in the source

code. They are "active data dictionaries" for managing WRF data structures. A registry file

includes a database describing attributes for model state, intermediate, and configuration data,

including:

* Dimensionality, number of time levels, staggering.

* Association with physics

* I/O classification (history, initial, restart, boundary)

* Communication points and patterns

* Configuration lists (e.g., namelists)

* Nesting up- and down-scale interpolation

The registry files also auto-generates thousands of lines in sections of WRF code from

database. Information from the Registry also turns *.f90 code files into *.F files. * All of the

differences between *.F and *.f90 files are due to the included pieces that are manufactured

by the Registry. These additional pieces are all located in the /global/u2/y/yashar/hopper/WRF/

WRFV3-Chem-3.4/inc/ directory (for WRF-Chem 3.4).

- Note that the *.F files are original files of source code that are inside of the make files. The

*.f90 files are what we end up running the compiler on. To edit WRF code, change the *.F code,

but don't do a "./clean" when re-compiling. Only do a "./clean" when changing a Registry file.


6. More information on the WRF software and registry files can be found in the following WRF

tutorial lecture slides:

http://www.mmm.ucar.edu/wrf/users/tutorial/201301/gill_software.pdf http://

www.mmm.ucar.edu/wrf/users/tutorial/201301/gill_registry.pdf
