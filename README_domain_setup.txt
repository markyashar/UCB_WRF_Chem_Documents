I've run WRF/WRF-Chem simulations for the the February 2006 period 
that have included anthropogenic CO2 tracer emissions (using NARR and 
EDGAR data).I set up a course domain ("d01") covering all of California 
and a few other western states, and a nested inner domain ("d02") in Northern 
CA which includes the region around the North Coast and Angelo Coast Range
Reserve (ACRR)

I used WRF v 3.3.1 (September 2011 release) in combination with some 
components of WRF v 3.4 (including WRF-Chem 3.4). The NARR data is from 
NARR model data circulation statistics with start date of February 1, 2006, 
12:00:00:00 and end date (approximately, with some variation in end date in 
some cases) February 28, 2006, 12:00:00. 

Note that the latitude and longitude over the ACRR (near Mendocino) is 
Lat. = 39 deg 43' 45'' N,  Lon. = -123 deg 38' 40'' E. In decimal degrees 
used in WRF, this is Lat. = 39.7300, Lon. = -123.6400. The parent (coarser) 
domain is centered over the ACRR and its size is 60x60 grid points with a 
horizontal grid resolution (distance) of 30 km and a total area of about 
1.77 x 10^3 km x 1.77 x 10^3 km (corresponding to about 15.5 degrees in 
the latitude direction x 17.5 degrees in the longitude direction). The 
nested (embedded, finer mesh) domain size is 64x64 grid points with a 
horizontal grid distance of 10 km and a total area of 630 km x 630 km 
(corresonding to about 5.5 degrees in the latitude direction and about 6.3 
degrees in the longitude direction), such that there is a 1:3 grid ratio 
between the parent and nested domain.  The map of the WPS domain set-up for 
the simulations discussed here can be found in the file
WPS_ACRR -> Plots -> Low Resolution -> "angelo_coast_1way_nested_domain.pdf" in
my google drive space. The same map (PDF file) can be found in the 
/data/sequoia/datasets/yashar-WRF/wrf_edg41_lowres_diurnon_ant_bio_acrr/plots_animations directory on Ocotillo. The black square in the center of the map (labeled 
as "d02") shows the nested domain and the white circle gives the location 
of the Angelo Coast Range Reserve.

The simulation uses 35 vertical (z) grid box levels whereby the WRF model code
will calculate a set of 35 vertical levels with thinner layers at the bottom 
and thicker layers at the top. I believe, then, that the height of the 
planetary boundary layer (the WRF output variable PBLH) from the ground is 
about 35 grid boxes or points.
