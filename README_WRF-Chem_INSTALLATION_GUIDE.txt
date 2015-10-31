
Hello,

I have installed WRF-Chem 3.4 for my use on one of the NERSC supercomputers (a Cray XE6 system) called "Hopper" 
(see http://www.nersc.gov/users/computational-systems/hopper/ for more information about this system). So, the 
instructions I give below only apply to this system and may differ somewhat from the computer system that you 
are installing WRF/WRF-Chem on. In other words, the details will differ depending on the compiler(s) and overall 
system configuration that you are using. So, you can think of these instructions as more of a general guide that 
will give you some tips and pointers for installing WRF-Chem 3.4, rather than detailed exact instructions.

Please make sure to check the WRF-Chem User's Manual (http://ruc.noaa.gov/wrf/WG11/Users_guide.pdf), which gives 
general instructions on installing WRF-Chem, building the WRF-Chem emissions conversion code, and generating WRF-Chem 
emissions data in Chapters 3 and 4.

In my case, I had installed WRF 3.3.1 before installing WRF-Chem 3.4, so as I installed WRF-Chem 3.4, I also needed 
to install WRF 3.4 on top of my already existing installation of WRF 3.3.1. This may differ from what you plan to 
do or did. 

When I installed/built/compiled WRF-Chem 3.4, I first obtained the WRFV3.4 tar file, untarred it, and then untarred 
the WRF-Chem code inside the WRFV3 directory of my WRF installation. This then automatically created a sub-directory 
"chem/". In order to do all of this, I did the following (-- note: this applies to my directory structure; yours 
will be different):

1. I created a directory called "temp/" inside of my $HOME/hopper/WRF/WRFV3/ directory and untarred WRF3.4.TAR.gz 
inside of the temp/ directory (so that ../WRF/WRFV3/ wasn't overwritten), i.e., 
> mkdir ../WRF/WRFV3/temp/
> cd temp
> wget http://www.mmm.ucar.edu/wrf/src/WRF3.4.TAR.gz    --> I'm downloading WRF 3.4 here, which is a pre-requisite of WRF-Chem 3.4.
> tar xvf WRFV3.4.TAR.gz    --> this creates a ../WRF/WRFV3/temp/WRFV3/ directory.
> mv WRFV3 WRFV3-Chem-3.4     --> I'm just changing the name of the directory I just created from WRFV3 to WRFV3-Chem-3.4
> mv WRFV3-Chem-3.4 $HOME/hopper/WRF/  --> I'm just moving this newly created directory to $HOME/hopper/WRF/
> wget http://www.mmm.ucar.edu/wrf/src/WRFV3-Chem-3.4.TAR.gz 
(do this inside the $HOME/hopper/WRF/WRFV3-Chem-3.4/ directory) --> I'm downloading the WRF-Chem 3.4 software here.
> tar xvf WRFV3-Chem-3.4.TAR.gz   ---> this created a chem/ directory inside the WRFV3-Chem-3.4/ directory.

Before building the WRF-Chem-3.4 code, several environmental settings are used to specify whether certain portions 
of the code need to be included in the model build. In C-shell syntax, some important environmental settings are:

setenv WRF-CHEM 1
setenv WRF_KPP 1
setenv YACC '/usr/bin/yacc -d'
setenv FLEX_LIB_DIR /global/u2/y/yashar/hopper/
New_flex/flex-2.5.3
setenv KPP_HOME /global/u2/y/yashar/hopper/WRF/WRFV3-Chem-3.4/chem/KPP/kpp/kpp-2.1  (for my specific case)

Also, make sure that you have the correct version of the FLEX software installed on your system. See the last part of 
this e-mail if this is a problem.

2. Configure model and compile code:

From the ../WRF/WRFV3-Chem-3.4/ directory, input the following;
> ./configure 

Here, I used option #29, Cray XT CLE/Linux x86_64, PGI Compiler w/gcc (dmpar). In response to the question "Compile for nesting?",
I chose (1=basic), which is the default. Then, input the following:

> ./compile em_real >& compile_chem_kpp.log1

If you get an error message at this point, you may need to follow the instructions in the document "Outline: Problems with 
yacc & Flex when compiling WRF/CHEM v3.0 with KPP" (http://ruc.noaa.gov/wrf/WG11/KPP_yacc_flex_problems.pdf)

In my case, I needed to do some FLEX tweaking: I needed to use modern flex, and for this I needed to change two lines in the 
../WRFV3-Chem-3.4/chem/KPP/kpp/kpp-2.1/src/scan.l file
(a) Find the line 'yy_buffers[yy_buf_level]=yy_current_buffer' and replace in by 'yy_buffers[yy_buf_level]=YY_CURRENT_BUFFER' in scan.l
(b) Find the line 'oldb=yy_current_buffer' and replace it by 'oldb = YY_CURRENT_BUFFER' in scan.l
(c) Now, in this same src directory,  enter in 'flex scan.l' at the command prompt.
(d) Next, you need to edit the program 'lex.yy.c' in the same directory.  Follow the instructions in 
http://ruc.noaa.gov/wrf/WG11/KPP_yacc_flex_problems.pdf here, i.e.
copy and paste the following lines at the beginning of the "lex.yy.c" file:                #define INSTALL 0
                                                                                           # define CMD_STATE 1
                                                                                           etc....
(e) Deleting the -ll link:
Open and edit ../WRFV3-Chem-3.4/chem/KPP/kpp/kpp-2.1/src/Makefile, and delete all (there are 2) entries of 'll' there. 
Be sure to do this or linking will probably fail.

(f) Fixing paths for integr_edit.exe:
Open the file ../WRFV3-Chem-3.4/chem/KPP/util/write_decomp/Makefile for editing. Find the line 'integr_edit.exe $(MECH)" and replace
it with "./integr_edit.exe $(MECH)".

Now, you are ready to try to compile KPP and the WRF-Chem code again. Before you compile WRF-Chem code, again set up the
environmental variables as before:

setenv WRF-CHEM 1
setenv WRF_KPP 1
setenv YACC '/usr/bin/yacc -d'
setenv FLEX_LIB_DIR /global/u2/y/yashar/hopper/New_flex/flex-2.5.3
setenv KPP_HOME /global/u2/y/yashar/hopper/WRF/WRFV3-Chem-3.4/chem/KPP/kpp/kpp-2.1  (for my specific case)

Then, go to the ../WRFV3-Chem-3.4/ directory and compile the code as follows:

./compile em_real >& compile_chem_kpp.log2

If the compilation was successful, the following files should have been created in the ../WRFV3-Chem-3.4/main/ directory:
tc.exe, nup.exe, ndown.exe, real.exe, wrf.exe

Now, if you are also interested in generating WRF-Chem Emissions data (such as, for example, the standard 4-km resolution 
2005 NEI emissions data set), you will first need to build the WRF-Chem emissions conversion code (see also section 2.2.4 
of Chapter 2 of the WRF-Chem User Guide) -- convert_emiss.exe.

NOTE: IN MY CASE -- When I use WRF-Chem V 3.4, I do not need to use convert_emiss.exe for setting up and simulating the 
inert/passive CO2 tracers, but your particular case may be different. If real.exe and wrf.exe have been successfully 
built/created by/within WRF-Chem V 3.4 and they are successfully functioning, then this may be all you need for setting 
up and running the WRF-Chem model for use with the inert/passive CO2 tracer. 

However, you are using WRF-Chem to do things other than setting up inert passive tracers, then you will probably need to 
build WRF-Chem emissions conversion code (convert_emiss.exe). To do this, you might (depending on the specific details of 
your system/configuration) need to install (a different/new version of) a software package  called "FLEX" if it isn't 
already installed on your system (or the right version isn't already installed on your system). According to my notes, 
I needed to do this in order to successfully compile/build "convert_emiss.exe". This is what I did in my case:

* I downloaded and installed "flex" from source , using the one used by the NCARG team, as follows:
- In my /global/u2/y/yashar/hopper/ directory (basically, this is my home directory), I created a directory called 
"New_flex", i.e.,
> mkdir New_flex
> cd New_flex
> wget http://ww.ncl.ucar.edu/Download/files/flex.tar.gz  (At the time that I downloaded this it was version 2.5.3)
> tar -xvzf flex.tar.gz
> configure --prefix=/global/u2/y/yashar/hopper/New_flex
> make
> make install

* Then, I edited the "Makefile.defs" file in my /global/u2/y/yashar/hopper/WRF/WRFV3-Chem-3.4/chem/KPP/kpp/kpp-2.1/ 
directory by including the following lines near the end of the file:

FLEX=flex
FLEX_LIB_DIR = /global/u2/y/yashar/hopper/New_flex/flex-2.5.3

Then, in the same ../WRFV3-Chem-3.4/chem/KPP/kpp/kpp-2.1/ directory (I believe), I typed in the following:
> make clean
> make 

Then, I went back to the /global/u2/y/yashar/hopper/WRF/WRFV3-Chem-3.4/ directory (in your case this may
be the ../WRFV3/ directory) and entered the following:

> ./compile emi_conv >& chem_emi_conv.log
> ls -ls WRFV3-Chem-3.4/chem/*.exe

chem/convert_emiss.exe

> ls -ls test/em_real/*.exe

em_real/convert_emiss.exe

which shows that the "convert_emiss.exe" executable was successfully created. 


You can find some examples and tutorials of how to use WRF-Chem (including how to use the chemistry 
emissions conversion code and how to generate emission data) and to run WRF-Chem in 
Chapters 2, 3, and 4 of the User's Guide. See also http://ruc.noaa.gov/wrf/WG11/tutorialexercises_v34.htm
for various WRF-Chem 3.4 tutorial exercises (including, for example,  "Exercise 7: Building and using an NEI-2005 emissions data").
You can probably find some tutorial exercises that are relevant to your cases as well. See also http://ruc.noaa.gov/wrf/WG11/tutorialexercises.htm 
for additional tutorial exercises as well as http://ruc.noaa.gov/wrf/WG11/ for various WRF-Chem tutorial presentations and a number of other 
links/documentation that may be useful to you.

I hope at least some of the information I've written above will help you a little in getting started with WRF-Chem 3.4  :-)
If you have more questions let me know and I will try to help.

Good luck!
