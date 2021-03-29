# Compiling WPS on `expanse`

## Obtaining and compiling WPS on expanse

### Download WPS from `github`

You should download your WPS code to a directory "next to" your WRF directory.  The WPS code will look for code 

```bash
git clone https://github.com/wrf-model/WPS.git
```

This will download the code into a subdirectory called `WPS` in the current directory. Enter this directory:

```bash
cd WPS
```

```{caution}
Note that the steps below must be done in one ssh session.  Library paths on `expanse` change from session to session, so you must start from here if you lose your connection.
```

### Configuring WPS

We will now configure WPS for compilation.  First, we need to set up the software environment for WRF.  `expanse` uses the `modules` package to configure software.  Here is the software environment we need for WRF:

```bash
module purge

module load cpu intel mvapich2

module load netcdf-c netcdf-cxx netcdf-fortran

module load libpng jasper zlib
```

Next we need to find the NETCDF library path, and set an environment variable for it.  Run the following command:

```bash
echo $LD_LIBRARY_PATH | grep netcdf

```

Grab the path that looks like this, containing `netcdf-fortran`:

```bash
/cm/shared/apps/spack/cpu/opt/spack/linux-centos8-zen2/intel-19.1.1.217/netcdf-fortran-4.5.3-2wjlrztnogahr6sgpaxuwwd2mfl5ligr/
```

and set the environment variable `NETCDF` to that path:

```bash
export NETCDF=/cm/shared/apps/spack/cpu/opt/spack/linux-centos8-zen2/intel-19.1.1.217/netcdf-fortran-4.5.3-2wjlrztnogahr6sgpaxuwwd2mfl5ligr/
```

Also, for performance increase over data output size, set the following:

```bash
export NETCDF_classic=1
```

Finally, we must point `WPS` to the `jasper` libraries and include files:

```bash
export JASPERLIB=/usr/lib64
export JASPERINC=/usr/include
```

Now run `configure`:

```bash
./configure

```

Select option 19, and it should complete.

### Compiling WPS

Now, you can compile WPS.  

```bash
./compile 
```

This should take less than 5 minutes.  At the end, you should have `geogrid.exe`, `ungrib.exe`, and `metgrid.exe`.

```{note}
In case something goes wrong, as in the WRF compile, in the top level `WPS` directory, run `./clean -a` to remove all compiled files, and configure and compile again.
```

