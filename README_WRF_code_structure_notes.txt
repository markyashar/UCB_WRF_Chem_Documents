
Here is a very broad and brief (and simplified) partial overview of some features of the WRF code structure and software architecture (e.g.,what the different WRF routines do
and how they are organized, etc.). This is based on the WRF tutorial slides and some of the notes I took during the WRF tutorial lectures at NCAR. It may be a bit helpful 
in getting started in the process of better understanding the WRF black box (physics, dynamics, overall code structure, what each of the subroutines do, etc.). PLEASE NOTE 
THAT THERE IS NOTHING HERE CONCERNING TRACERS, but I am also currently working on understanding the WRF code structure regarding tracers (which may include WRF-Chem as well).

*  The "main" WRF routine that handles the calls to the physics and dynamics, including boundary conditions, is (in the case WRF 3.3.1 in my home directory on Hopper):

/global/u2/y/yashar/hopper/WRF/WRFV3/dyn_em/solve_em.F

- solve_em.F is the main driver for advancing a grid a single timestep. It is a "mediation-layer" routine,
meaning that it takes a domain object and advances it one time step. This "solver" is where the tendencies are
initialized to zero, some pre-physics terms are computed, and where the Runge-Kutta and sound time steps are looped.

* The calls to the physics schemes are made from a further call down the call tree:

/global/u2/y/yashar/hopper/WRF/WRFV3/dyn_em/module_first_rk_step_part1.F

- Inside of the solve_em.F and first_rk_step_part1.F routines, all of the data is located in the "grid" structure: grid%ht.

- The dimensions in solve_em.F and first_rk_step_part1.F are "d" (domain), which is the physical size of the domain you're going to run, and "m" (memory), which relates to state
variables (from inside the Registry file); recall that i,j,k are indices for the WRF domain(s):

  ids,ide,jds,jde,kde
  ims,ime,jms,jme,kms,kme

- The "t" (tile) dimensions are computed in the first_rk_step_part1 and are passed to all the drivers.

- WRF uses global indexing, which can be observed by looking an netcdf output file with ncview, for example.

* The WRF system has organized the physics routines in the ../WRF/phys directory. In this directory, each type of physics scheme has a driver:

module_cumulus_driver.F          cu (cumulus schemes)
module_microphysics_driver.F     mp (microphysics schemes)
module_pbl_driver.F              bl  (planetary boundary layer schemes)
module_radiation_dirver.F        ra  (radiation schemes)
module_surface_driver.F          sf (surface schemes)

For example, for subgrid-scale precipitation, we have the following list of all cumulus schemes (*_cu_*.F) in the ../WRF/phys directory:

module_cu_camzm.F                
module_cu_camuwshcu.F
module_cu_kfeta.F        
module_cu_bmj.F 
module_cu_nsas.F 
module_cu_osas.F    
module_cu_g3.F
module_cu_camuwshcu_driver.F
module_cu_kf.F       
module_cu_camzm_driver.F 
module_cu_gd.F  
module_cu_sas.F 
module_cu_tiedtke.F

and the following list of boundary layer scheme routines (*_bl_*.F):

module_bl_mrf.F                  
module_bl_gwdo.F          
module_bl_camuwpbl_driver.F 
module_bl_gfs.F     
module_bl_myjpbl.F  
module_bl_ysu.F
module_bl_myjurb.F               
module_bl_boulac.F       
module_bl_temf.F           
module_bl_gfs2011.F 
module_bl_qnsepbl.F
module_bl_mfshconvpbl.F
module_cam_bl_diffusion_solver.F 
module_cam_bl_eddy_diff.F 
module_bl_qnsepbl09.F       
module_bl_acm.F     
module_bl_mynn.F


* The main WRF routine (module) that deals with advection is ../WRFV3/dyn_em/module_advect_em.F

* The main WRF routine (module) that deals with Lateral boundary conditions is ../WRFV3/dyn_em/module_bc_em.F

* The routine ../WRFV3/dyn_em/module_big_step_utilities_em.F computes various right hand side
terms such as pressure gradient, buoyancy, w damping, horizontal and vertical diffusion, Coriolis, curvature,
and Rayleigh damping.

* The routine ../WRFV3/dyn_em/module_small_step_em.F contains all of the sound step utilities to advance u, v, mu, t, w
within the small time-step loop.


--------

Bibliography/References:

1. "WRF Software: Code and Parallel Computing" by John Michalakes (Head WRF Software Architecture), Michael Duda, Dave Gill, 2013  
(http://www.mmm.ucar.edu/wrf/users/tutorial/201301/gill_software.pdf). See especially pages 33-43.

2. "WRF Software Architecture" by John Michalakes, Michael Duda, Dave Gill, 2012 (http://www.mmm.ucar.edu/wrf/users/tutorial/201207/WRF_Software.pdf).
------------------------

Further, more extensive information about the WRF code structure/architecture can be found by examining the WRF CALLING TREE web pages (for WRF Model Version 3.0.1):

http://www.cisl.ucar.edu/staff/huangwei/WRFV3/wrf_callingtree.html

http://www.cisl.ucar.edu/staff/huangwei/WRFV3/real_data_callingtree.html

http://www.cisl.ucar.edu/staff/huangwei/WRFV3/

This calling tree is a bit out-date because it is for WRF v 3.0.1 and I am using WRF v 3.3.1 and WRF v 3.4. However, there are
still many similarities and it may still be useful.

- mark 

