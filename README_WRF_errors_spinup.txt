The website (blog) given below seems to provide some useful information 
regarding various WRF errors and problems you may encounter when running 
simulations and how to deal with and overcome them:

http://www.nusculus.com/wrf-errors

The article/blog focuses on CLF errors (see
"http://en.wikipedia.org/wiki/Courant-Friedrichs-Lewy_condition" regarding the 
Courant-Friedrichs-Lewy condition), but the information is relevant to a wide
variety of other errors/problems (such as segmentation faults) you might 
encounter during WRF model runs. In particular, I wanted to draw your 
attention to the following passage from the blog/article:

"Some people recommend that you do not use the first 8 or 12 hours of a run 
because WRF is "spinning up". It takes a while for the low resolution 
weather data that is  used to initialize WRF to smooth out. It also takes 
time for clouds to develop within the model and be a weather affecting factor. 
During that time, there are waves of changes that cross the grid several 
times, causing less than realistic phenomenon."

I found that the spin-up time was significantly longer in some cases (e.g., 
the first few or several days of a run rather than the first 8 to 12 hours) 
when  WRF-Chem simulations were run with the CO2 tracer was included -- 
especially when the VPRM biogenic CO2 fluxes were also included in the 
simulation run.