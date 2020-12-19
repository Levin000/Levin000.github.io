---
layout: post
title: "Compile & Install WRF4.1.2 On CentOS 8.2"
date: 2020-6-8
updated: 2020-6-18
categories:
- 模型模拟
tags:
- 模式
- region climate model
- Linux
- CentOS
- Weather Research and Forcast Model
- WRF 
- gfortran
---
![Weather Research and Forcast Model](/images/article/wrf/wrf_logo.jpg)

# 1. Install "GNU Tools" and "gfortran"  
=========================================================  
dnf -y install gcc  
dnf -y install cpp gcc-c++  
dnf -y install gcc-gfortran  
dnf -y install unzip bzip2 time nfs-utils perl tcsh wget git m4 mlocate.x86_64 libX11-devel.x86_64 libXext-devel.x86_64 libXrender-devel.x86_64 fontconfig-devel.x86_64 curl-devel cmake cairo-devel pixman-devel bzip2-devel byacc flex libXmu-devel libXt-devel libXaw libXaw-devel  

=========================================================  

<!-- more -->

# 2. Directory Structure  
##  2.1 Directory:  
=========================================================  
Build_WRF       (Main Directory)  
|--LIBRARIES    (Library Directory)  
|--geos         (Geography Data and mount to /data/geos)  
|--DATA         (Directory for Real-time Data)  
|--src          (Directory for Source Code)  

**Notes**:  
>1. You can create folder("Build_WRF") wherever you want as long as you are authorized.  
>2. In this tutotial, the folder("Build_WRF") was build in /root/.  

=========================================================

##  2.2 tests:  
=========================================================  
Make a new Directory as "tests" and download "Fortran_C_tests.tar" into it.
Follow the online tutotial to finish it.(https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compilation_tutorial.php)  

cd /root/Build_WRF/src  
mkdir TESTS  
cd TESTS  
wget http://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/Fortran_C_tests.tar --no-check-certificate  
tar -xf Fortran_C_tests.tar  

### Test #1: Fixed Format Fortran Test: TEST_1_fortran_only_fixed.f  
>   gfortran TEST_1_fortran_only_fixed.f  
>   ./a.out  
        
The following should print out to the screen:  
>   SUCCESS test 1 fortran only fixed format  

### Test #2: Free Format Fortran: TEST_2_fortran_only_free.f90  
>   gfortran TEST_2_fortran_only_free.f90  
>   ./a.out  

The following should print out to the screen:  
>   Assume Fortran 2003: has FLUSH, ALLOCATABLE, derived type, and ISO C Binding  
>   SUCCESS test 2 fortran only free format  
### Test #3: C: TEST_3_c_only.c  
>   gcc TEST_3_c_only.c  
>   ./a.out  
        
The following should print out to the screen:  
>   SUCCESS test 3 c only  

### Test #4: Fortran Calling a C Function (our gcc and gfortran have different defaults, so we force both to always use 64 bit [-m64] when combining them): TEST_4_fortran+c_c.c, and TEST_4_fortran+x_f.f90  
>   gcc -c -m64 TEST_4_fortran+c_c.c  
>   gfortran -c -m64 TEST_4_fortran+c_f.f90  
>   gfortran -m64 TEST_4_fortran+c_f.o TEST_4_fortran+c_c.o  
>   ./a.out  

The following should print out to the screen:  
>   C function called by Fortran  
>   Values are xx = 2.00 and ii = 1  
>   SUCCESS test 4 fortran calling c  

### Test #5: csh In the command line, type:  
>   ./TEST_csh.csh  

The result should be:  
>   SUCCESS csh test  

### Test #6: perl In the command line, type:  
>   ./TEST_perl.pl  

The result should be:  
>   SUCCESS perl test  

### Test #7: sh In the command line, type:  
>   ./TEST_sh.sh  

The result should be:  
>   SUCCESS sh test  

=========================================================  
        
# 3. Environments  
##  3.1 Set Environments  
=========================================================  
###DIR  
export DIR=/root/Build_WRF/LIBRARIES  
###MPICH  
export PATH=${DIR}/mpich/bin:$PATH  
export LD_LIBRARY_PATH=${DIR}/mpich/lib:$LD_LIBRARY_PATH  
###HDF5
export HDF5=${DIR}/hdf5  
export PATH=${DIR}/hdf5/bin:$PATH  
export LD_LIBRARY_PATH=${DIR}/hdf5/lib:$LD_LIBRARY_PATH
###NETCDF  
export PATH=${DIR}/netcdf/bin:$PATH  
export NETCDF=${DIR}/netcdf  
export LD_LIBRARY_PATH=${DIR}/netcdf/lib:$LD_LIBRARY_PATH  
###LIBPNG  
export LD_LIBRARY_PATH=${DIR}/libpng/lib:$LD_LIBRARY_PATH  
###JASPER  
export PATH=${DIR}/jasper/bin:$PATH  
export LD_LIBRARY_PATH=${DIR}/jasper/lib:$LD_LIBRARY_PATH  
export JASPERLIB=${DIR}/jasper/lib  
export JASPERINC=${DIR}/jasper/include  
###WRF  
ulimit -s unlimited  
export MALLOC_CHECK_=0  
export WRF_EM_CORE=1  
export WRFIO_NCD_LARGE_FILE_SUPPORT=1  
###WPS  
export WRF_DIR=/root/Build_WRF/src/WRF-4.1.2  

**Notes**:  
>1. Variables export above will write to ~/.bash_profile, 
    remember source .bash_profile after modify it.  
>2. If you do this module, please do not any changes 
    about ".bash_profile" file in moudle 4("4. Dependencies").  

=========================================================  

# 4. LIBRARIES  
=========================================================  
cd /root/Build_WRF/src  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  To locate to "/root/Build_WRF/LIBRARIES" faster, we export  
++  a variable as "DIR":  
++    nano ~/.bash_profile  
++-------------------------------------------  
++    ### DIR  
++    export DIR=/root/Build_WRF/LIBRARIES  
++-------------------------------------------  
++  Update PATH variable.  
++   source ~/.bash_profile  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

=========================================================  

##  4.1 MPI  
=========================================================  
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz  
tar -xzf mpich-3.3  
cd mpich-3.3  
./configure --prefix=${DIR}/mpich  
make -j2  
make install  
cd ..  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  Create PATH variable in file .bash_profile:  
++    nano ~/.bash_profile  
++-------------------------------------------  
++    ### MPICH  
++    export PATH=${DIR}/mpich/bin:$PATH  
++    export LD_LIBRARY_PATH=${DIR}/mpich/lib:$LD_LIBRARY_PATH  
++-------------------------------------------  
++  Update PATH variable.  
++    source ~/.bash_profile  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

=========================================================

##  4.2 ZLIB  
=========================================================  
wget https://zlib.net/zlib-1.2.11.tar.gz  
tar -xzf zlib-1.2.11.tar.gz  
cd zlib-1.2.11  
./configure --prefix=${DIR}/zlib  
make -j2  
make install  
cd ..  

=========================================================  

##  4.3 HDF5  
=========================================================  
wget https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.10/hdf5-1.10.5/src/hdf5-1.10.5.tar.gz  
tar xzf hdf5-1.10.5.tar.gz  
cd hdf5-1.10.5  
CC=gcc  
FC=gfortran  
CXX=g++  
./configure --with-zlib=${DIR}/zlib --prefix=${DIR}/hdf5 --enable-fortran --enable-fortran2003 --enable-cxx  
make -j2  
make install  
cd ..  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  Create PATH variable in file .bash_profile:  
++    nano ~/.bash_profile  
++-------------------------------------------  
++    ### HDF5
++    export HDF5=${DIR}/hdf5
++    export PATH=${DIR}/hdf5/bin:$PATH
++    export LD_LIBRARY_PATH=${DIR}/hdf5/lib:$LD_LIBRARY_PATH
++-------------------------------------------  
++  Update PATH variable.  
++    source ~/.bash_profile  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

=========================================================  

##  4.4 PnetCDF 
=========================================================  

=========================================================  

##  4.5 NetCDF-C  
=========================================================  
export CC=gcc  
export CFLAGS="-fPIC"  
export CPP=cpp  
export CPPFLAGS="-I${DIR}/hdf5/include"  
export LDFLAGS="-L${DIR}/hdf5/lib"  
wget ftp://ftp.unidata.ucar.edu/pub/netcdf/netcdf-c-4.7.0.tar.gz  
tar xzf netcdf-c-4.7.0.tar.gz  
cd netcdf-c-4.7.0  
./configure --prefix=${DIR}/netcdf  
make -j2  
make install  
cd ..  

=========================================================  

##  4.6 NetCDF-Fortran  
=========================================================  
export LD_LIBRARY_PATH=${NCDIR}/lib:${LD_LIBRARY_PATH}  
wget ftp://ftp.unidata.ucar.edu/pub/netcdf/netcdf-fortran-4.4.5.tar.gz  
tar xzf netcdf-fortran-4.4.5.tar.gz  
cd netcdf-fortran-4.4.5  
export CC=gcc  
export F77=gfortran  
export FC=gfortran  
export FCFLAGS="-m64"  
export CPP=cpp  
export CPPFLAGS="-I${DIR}/netcdf/include"  
export LDFLAGS="-L${DIR}/netcdf/lib"  
./configure --prefix=${DIR}/netcdf  
make -j2  
make install  
cd ..  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  Create PATH variable in file .bash_profile:  
++    nano ~/.bash_profile  
++-------------------------------------------  
++    ### NETCDF  
++    export PATH=${DIR}/netcdf/bin:$PATH  
++    export NETCDF=${DIR}/netcdf  
++    export LD_LIBRARY_PATH=${DIR}/netcdf/lib:$LD_LIBRARY_PATH  
++-------------------------------------------  
++  Update PATH variable.  
++    source ~/.bash_profile  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

**Notes**:  
    Chect whether the file("netcdf.inc") exists， do >1. or >2.:  
>1.    find / -name netcdf.inc     

    the terminal will show the find information of netcdf.inc, like below:  
    [root@localhost netcdf-fortran-4.4.5]# find / -name netcdf.inc  
    /root/Build_WRF/src/netcdf-fortran-4.4.5/fortran/netcdf.inc  
    /root/Build_WRF/LIBRARIES/netcdf/include/netcdf.inc  
   
    If you see "/root/Build_WRF/LIBRARIES/netcdf/include/netcdf.inc",  
    this mean "netcdf.inc" has been successfully generated, go to the  
    next install module.  
    Else, do 4.6 install module again(begain at "export CC=gcc").  

>2.    ls $DIR/netcdf/include/  

    the terminal will show the find information of netcdf.inc, like below:  
    [root@localhost netcdf-fortran-4.4.5]# ls $DIR/netcdf/include/  
    netcdf_aux.h     netcdf.h      netcdf_meta.h       netcdf_nc_interfaces.mod  typesizes.mod  
    netcdf_f03.mod   netcdf.inc    netcdf.mod          netcdf_nf_data.mod  
    netcdf_filter.h  netcdf_mem.h  netcdf_nc_data.mod  netcdf_nf_interfaces.mod  

    If you see "netcdf.inc", this mean "netcdf.inc" has been successfully  
    generated, go to the next install module. 
    Else, do 4.6 install module again(begain at "export CC=gcc").  

=========================================================  

##  4.7 LIBPNG  
=========================================================  
wget ftp://ftp.simplesystems.org/pub/libpng/png/src/libpng16/libpng-1.6.37.tar.gz  
tar xzf libpng-1.6.37.tar.gz  
cd libpng-1.6.37/  
export CC=gcc  
export F77=gfortran  
export FC=gfortran  
export FCFLAGS="-m64"  
export CPP=cpp  
export CPPFLAGS="-I${DIR}/netcdf/include"  
export LDFLAGS="-L${DIR}/netcdf/lib"  
./configure --prefix=${DIR}/libpng  
make -j2  
make install  
cd ..  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  Create PATH variable in file .bash_profile:  
++    nano ~/.bash_profile  
++-------------------------------------------  
++    ### libpng  
++    export LD_LIBRARY_PATH=${DIR}/libpng/lib:$LD_LIBRARY_PATH  
++-------------------------------------------  
++  Update PATH variable.  
++    source ~/.bash_profile  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

=========================================================  

##  4.8 JASPER  
=========================================================  
wget https://www.ece.uvic.ca/~frodo/jasper/software/jasper-1.900.19.tar.gz  
tar xzf jasper-1.900.19.tar.gz  
cd jasper-1.900.19  
./configure --prefix=${DIR}/jasper  
make -j2  
make install  
cd ..  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  Create PATH variable in file .bash_profile:  
++    nano ~/.bash_profile  
++-------------------------------------------  
++    ### jasper  
++    export PATH=${DIR}/jasper/bin:$PATH  
++    export LD_LIBRARY_PATH=${DIR}/jasper/lib:$LD_LIBRARY_PATH  
++-------------------------------------------  
++  Update PATH variable.  
++    source ~/.bash_profile  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

=========================================================  

##  4.9 tests  
=========================================================  
Download this tar file and place it in the TESTS directory, 
and then "cd" into the TESTS directory:  

wget http://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/Fortran_C_NETCDF_MPI_tests.tar --no-check-certificate  
tar -xf Fortran_C_NETCDF_MPI_tests.tar  

### Test #1: Fortran + C + NetCDF  
>   cp ${DIR}/netcdf/include/netcdf.inc .  
>   gfortran -c 01_fortran+c+netcdf_f.f  
>   gcc -c 01_fortran+c+netcdf_c.c  
>   gfortran 01_fortran+c+netcdf_f.o 01_fortran+c+netcdf_c.o \  
>       -L${NETCDF}/lib -lnetcdff -lnetcdf  
>   ./a.out  

The following should be displayed on your screen:  
>   C function called by Fortran  
>   Values are xx = 2.00 and ii = 1  
>   SUCCESS test 1 fortran + c + netcdf  
        
### Test #2: Fortran + C + NetCDF + MPI  
>   cp ${NETCDF}/include/netcdf.inc .  
>    mpif90 -c 02_fortran+c+netcdf+mpi_f.f  
>   mpicc -c 02_fortran+c+netcdf+mpi_c.c  
>   mpif90 02_fortran+c+netcdf+mpi_f.o \  
>   02_fortran+c+netcdf+mpi_c.o \  
>       -L${NETCDF}/lib -lnetcdff -lnetcdf  
>   mpirun ./a.out  

The following should be displayed on your screen:  
>   C function called by Fortran  
>   Values are xx = 2.00 and ii = 1  
>   status = 2  
>   SUCCESS test 2 fortran + c + netcdf + mpi  

=========================================================  

# 5. Buinding WRF  
=========================================================  
wget https://github.com/wrf-model/WRF/archive/v4.1.2.tar.gz  
mv v4.1.2.tar.gz WRFv4.1.2.tar.gz  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  ### Parameters of building WRF.  
++  Create PATH variable in file .bash_profile:  
++    nano ~/.bash_profile  
++-------------------------------------------  
++    ### WRF  
++    ulimit -s unlimited  
++    export MALLOC_CHECK_=0  
++    export WRF_EM_CORE=1  
++    export WRFIO_NCD_LARGE_FILE_SUPPORT=1  
++-------------------------------------------  
++  Update PATH variable.  
++    source ~/.bash_profile  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

tar -xzf WRFv4.1.2.tar.gz  
cd WRF-4.1.2/  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  ### Edit the default config to eneable Jasper.  
++  It's a option, not required. As below:  
++    nano arch/Config.pl  
++-------------------------------------------  
++    I_really_want_to_output_grib2_from_WRF = "TRUE" ;  
++-------------------------------------------  
++  Notes: "JASPERLIB" and "JASPERINC" must be set in  
++  ~/.bash_profile.  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  Create PATH variable in file .bash_profile:  
++    nano ~/.bash_profile  
++-------------------------------------------  
++    export JASPERLIB=${DIR}/jasper/lib  
++    export JASPERINC=${DIR}/jasper/include  
++-------------------------------------------  
++  Update PATH variable.  
++    source ~/.bash_profile  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

./configure  
>> 34  
>> 1  

./compile em_real >& log.compile &  
tail -f log.compile  
ls -lah main/*.exe  

If you see real.exe and wrf.exe then correct. 
Else check Error in log.compile file.  

cd ..  

=========================================================  

# 6. Buinding WPS  
=========================================================  
wget https://github.com/wrf-model/WPS/archive/v4.1.tar.gz  
tar xvzf v4.1.tar.gz  
cd WPS-4.1  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  ### Setting the directory of WRF installed. Not required,  
++  setting it if you modify the name or location of WRF install by default.  
++  Create PATH variable in file .bash_profile:  
++    nano ~/.bash_profile  
++-------------------------------------------  
++    ### WPS  
++    export WRF_DIR=/root/Build_WRF/src/WRF-4.1.2  
++-------------------------------------------  
++  Update PATH variable.  
++    source ~/.bash_profile  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

./configure  
>> 3  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  ### edit configure.wps file.  
++  Change DM_FC to mpif90 and Append -lgomp in WRF_LIB.  
++    nano configure.wps  
++-------------------------------------------  
++    DM_FC = mpif90  
++    WRF_LIB = -L$(WRF_DIR)/external/io_grib1 -lio_grib1 \  
++              -L$(WRF_DIR)/external/io_grib_share -lio_grib_share \  
++              -L$(WRF_DIR)/external/io_int -lwrfio_int \  
++              -L$(WRF_DIR)/external/io_netcdf -lwrfio_nf \  
++              -L$(NETCDF)/lib -lnetcdff -lnetcdf -lgomp  
++-------------------------------------------  
++    
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  


./compile >& log.compile &  
tail -f log.compile  
ls -lah *.exe  

If you see geogrid.exe metgrid.exe and ungrib.exe then correct.  
Else check Error in log.compile file.  

cd ..  

=========================================================  

# 7. Run a Case  
=========================================================  
**Notes**:   
This case referenfe from "[How to Install WRF Model On CentOS Version 7](https://drive.google.com/file/d/1RaXuWig4WM4VflD9rHolA_5MuWmiEzBn/view)".      

##  7.1 Create Directory for Geography Data.  
=========================================================  
cd /root/Build_WRF/
wget http://www2.mmm.ucar.edu/wrf/src/wps_files/geog_complete.tar.bz2
tar -xvjf geog_complete.tar.bz2

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  If above link is invaild, do as below:  
++-------------------------------------------  
++    wget https://www2.mmm.ucar.edu/wrf/src/wps_files/geog_complete.tar.gz  
++    wget https://www2.mmm.ucar.edu/wrf/src/wps_files/albedo_modis.tar.bz2  
++    wget https://www2.mmm.ucar.edu/wrf/src/wps_files/maxsnowalb_modis.tar.bz2  
++    tar -xzf geog_complete.tar.gz  
++    tar -xjf albedo_modis.tar.bz2  
++    tar -xjf maxsnowalb_modis.tar.bz2  
++    mv albedo_modis geog/  
++    mv maxsnowalb_modis geog/  
++-------------------------------------------  
++    
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

=========================================================

##  7.2 Create directory for Real-time Data.  
=========================================================  
mkdir -p /root/Build_WRF/DATA  
cd /root/Build_WRF/DATA  
wget http://hydro.haii.or.th/tmp/WRF/gfs.t00z.pgrb2.0p50.f000  
wget http://hydro.haii.or.th/tmp/WRF/gfs.t00z.pgrb2.0p50.f006  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++    
++-------------------------------------------  
++   Or update Real-time from web site ftp://ftpprd.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/  
++-------------------------------------------  
++    
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

=========================================================  

##  7.3 Running WPS.  
=========================================================  
cd /root/Build_WRF/WPS-4.1  
rm namelist.wps  
wget http://hydro.haii.or.th/tmp/WRF/namelist.wps  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  ### Edit namelist.wps section max_dom, start_date, end_date  
++  ### same Real-time Data and set geog_data_path to Geography Data.  
++    
++      nano namelist.wps  
++-------------------------------------------  
++      &share  
++       max_dom = 1,  
++       start_date = '2017-05-17_00:00:00','2006-08-16_12:00:00',  
++       end_date = '2017-05-17_06:00:00','2006-08-16_12:00:00',  
++       
++      geog_data_path = '/root/Build_WRF/geog/'
++-------------------------------------------  
++    Pay attention to the first colmun of start_date、end_date.  
++    Because max_dom is 1.  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

###  7.3.1 Create Geography Data.  
./geogrid.exe  
ls -lah geo_em.d01.nc  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++    
++-------------------------------------------  
++  ### If you see **geo_em.d01.nc** is coreect.  
++-------------------------------------------  
++    
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
 
###  7.3.2 Link Real-time Data top WPS.  
./link_grib.csh /root/Build_WRF/DATA/gfs.t00z.pgrb2.0p50.f0*  
 
###  7.3.3 Link Vtable.  
ln -sf ungrib/Variable_Tables/Vtable.GFS Vtable  
 
###  7.3.4 Create grib file.  
./ungrib.exe  
ls -lah FILE*  
  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++    
++-------------------------------------------  
++  ### If you see FILE* is coreect.  
++-------------------------------------------  
++    
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
  
###  7.3.5 Create met file.  
./metgrid.exe  
ls -lah met_em.*  
 
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++    
++-------------------------------------------  
++  ### If you see met_em.* is coreect.  
++-------------------------------------------  
++    
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
 
=========================================================  

##  7.4 Running WRF.  
=========================================================  
cd /root/Build_WRF/WRFV3/test/em_real/

###  7.4.1 Link met file from WPS to WRF.  
ln -sf /root/Build_WRF/WPS/met_em* .  

###  7.4.2 Edit namelist.input.  
rm namelist.input  
wget http://hydro.haii.or.th/tmp/WRF/namelist.input  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++  ### Edit namelist.input in section run_hours, start_year,  
++  ### start_month, start_day, start_hour, end_year, end_month,  
++  ### end_day, end_hour and num_metgrid_levels.  
++  ### nano namelist.input  
++-------------------------------------------  
++      &time_control  
++       run_days = 0,  
++       run_hours = 6,  
++       run_minutes = 0,  
++       run_seconds = 0,  
++       start_year = 2017, 2000, 2000,  
++       start_month = 05, 01, 01,  
++       start_day = 17, 24, 24,  
++       start_hour = 00, 12, 12,  
++       start_minute = 00, 00, 00,  
++       start_second = 00, 00, 00,  
++       end_year = 2017, 2000, 2000,  
++       end_month = 05, 01, 01,  
++       end_day = 17, 25, 25,  
++       end_hour = 06, 12, 12,  
++       end_minute = 00, 00, 00,  
++       end_second = 00, 00, 00,  
++       interval_seconds = 10800  
++      &domains  
++       time_step = 150,  
++       time_step_fract_num = 0,  
++       time_step_fract_den = 1,  
++       max_dom = 1,  
++       e_we = 74, 112, 94,  
++       e_sn = 61, 97, 91,  
++       e_vert = 30, 30, 30,  
++       p_top_requested = 5000,  
++       num_metgrid_levels = 32,  
++-------------------------------------------  
++    Because max_dom is 1.  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

###  7.4.3 Create real case.  
mpirun -np 1 ./real.exe  

tail rsl.error.0000  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++    
++-------------------------------------------  
++  ### If you see real_em: "SUCCESS COMPLETE REAL_EM INIT" is coreect.  
++-------------------------------------------  
++    
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

ls -alh wrfbdy_d01 wrfinput_d01  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++    
++-------------------------------------------  
++  ### If you see wrfbdy_d01 and wrfinput_d01 is coreect.  
++-------------------------------------------  
++    
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

###  7.4.4 Run WRF.  

mpirun -np 4 ./wrf.exe  

tail rsl.error.0000  
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++    
++-------------------------------------------  
++  ### If you see wrf: "SUCCESS COMPLETE WRF" is coreect.  
++-------------------------------------------  
++    
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

ls -alh wrfout_*  

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
++    
++-------------------------------------------  
++  ### If you see wrfout_* is coreect.  
++-------------------------------------------  
++    
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  

=========================================================  

=========================================================  

# Reference:
- [WRF-ARW OnLine Tutorial](https://www2.mmm.ucar.edu/wrf/OnLineTutorial/)  
- [How to Install WRF-Chem Model Version 4.1.2 and KPP On CentOS Version 7. (2019)](https://www.youtube.com/watch?v=7Y1QEJpaVD0&t=18s)  
- [How to Compile WRF: The Complete Process](https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compilation_tutorial.php)  
- [Getting and Building netCDF](https://www.unidata.ucar.edu/software/netcdf/docs/getting_and_building_netcdf.html)  
- [How to Install WRF-Chem On CentOS version 7. (2020)](https://www.youtube.com/watch?v=73Ix9B6MtWY&t=4s)  
- [Install WRF4 by Intel compiler On CentOS7.(2020)](https://www.youtube.com/watch?v=GlU-EtKAW90)  
- [How to Install WRF (part 1/2)(2018)](https://www.youtube.com/watch?v=sTtLgzWh-yg&t=16s)  
- [How to Install WRF (part 2/2)(2018)](https://www.youtube.com/watch?v=i3QF-DxXBcg&t=81s)
- [WRF Model | Users Site](https://www2.mmm.ucar.edu/wrf/users/)  
- [MeteoAdriatic YouTube](https://www.youtube.com/channel/UCqlGJ4CY6c863uVJ0URweKg/videos)

# Useful Libraries:
- [www2.mmm.ucar.edu](https://www2.mmm.ucar.edu/wrf/src/)
- [HDF5](https://support.hdfgroup.org/ftp/HDF5/releases/)
- [Curl](https://curl.haxx.se/download/)
- [libpng](https://sourceforge.net/projects/libpng/files/)
- [PnetCDF](https://parallel-netcdf.github.io/wiki/Download.html)
- [Jasper](https://www.ece.uvic.ca/~frodo/jasper/)
- [Netcdf-C](https://github.com/Unidata/netcdf-c/releases)
- [Netcdf-Fortran](https://github.com/Unidata/netcdf-fortran/releases)
- [WRF](https://github.com/wrf-model/WRF/releases)
- [WPS](https://github.com/wrf-model/WPS/releases)
