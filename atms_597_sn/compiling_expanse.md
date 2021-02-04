# Chapter 1: Getting started on `expanse`

## How to login (after you have set up 2-Factor Authentication)

```bash
ssh -l username login.xsede.org
```

Answer 2FA prompts (with your phone)

```bash
gsissh expanse
```

or, once you have done the above at least once

```bash
ssh -l username login.expanse.sdsc.edu
```

---

## Storage on `expanse`

There are 3 directories you can use on `expanse`.

Your home directory is in

```bash
/home/username
```

and can be used for permanent files you want backed up.  There is limited space here (100 GB), and the compute nodes cannot see this directory.

There is shared project space (2TB) for our class XSEDE account in the directory

```bash
/expanse/lustre/projects/uic406/$USER
```

where `$USER` is your `expanse` username.  This folder appears to not support batch job use.

Scratch space is also provided at

```bash
/expanse/lustre/scratch/$USER/temp_project
```

This is the directory where you should run batch jobs.  It is not limited for space, but there is a user limit of 2 million files.  It is not backed up, so important results should be copied to other directories.

---

## Obtaining and compiling WRF on expanse

### Download WRF from `github`

You should download your WRF code do your home directory or the shared project space.

```git
git clone https://github.com/wrf-model/WRF.git
```

This will download the code into a subdirectory called `WRF` in the current directory. Enter this directory:

```bash
cd WRF
```

```{caution}
Note that the steps below must be done in one ssh session.  Library paths on `expanse` change from session to session, so you must start from here if you lose your connection.
```

### Configuring WRF

We will now configure WRF for compilation.  First, we need to set up the software environment for WRF.  `expanse` uses the `modules` package to configure software.  Here is the software environment we need for WRF:

```bash
module purge

module load cpu intel mvapich2

module load netcdf-c netcdf-cxx netcdf-fortran
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

Now run `configure`:

```bash
./configure

```

Select option 20, nesting option 1, and it should complete.

````{note}
Edit the resulting configure.wrf, on the line that says `OPTAVX`, use a text editor such as `nano configure.wrf` to change it to read:

```bash
OPTAVX          =       -march=core-avx2
```

````

Save the file and you're good to go!

### Compiling WRF

Now, you can compile WRF.  To compile the Quarter-circle hodograph supercell case on 4 processors, type

```bash
./compile -j 4 em_quarter_ss
```

After 20-30 minutes, you should get a "SUCCESS" message.  You shouldn't ever have to recompile unless you need to change something in the fortran code or need to change the "Registry" - we'll talk about this later in the semester.  If you get disconnected from the session, you will have to reconnect and start over from the configure step (due to the way the libraries are loaded on `expanse`).

## Running WRF ideal on `expanse`

All right, you're good to go.  Let's try a test run.  At this point you can configure the `namelist.input` or input_sounding to what you would like for your particular run.

You'll need a `slurm` script for running your job - here is one you can save in the `test\em_quarter_ss` directory as `wrf_sbatch.sh`.  To create this file, open it in `nano`:

```bash
nano wrf_sbatch.sh
```

and paste in the contents below (then write out the file and exit).

```bash
#!/bin/bash
#SBATCH --job-name="idealwrf" #NAME OF JOB NAME
#SBATCH --output="wrf.em_q_ss.%j.%N.out" #NAME OF LOG FILE
#SBATCH --partition=compute #COMPUTE PARTITION
#SBATCH --nodes=1 #HOW MANY NODES - EACH NODE HAS 64 PROCESSORS
#SBATCH --ntasks-per-node=16 #HOW MANY PROCESSORS, IF YOU GO OVER 64, TAKE MORE NODES
#SBATCH --mem=48G #RAM LIMIT
#SBATCH --account=uic406 #ACCOUNT
#SBATCH --export=ALL #WRITE LOGS?
#SBATCH -t 01:30:00 #WALL TIME LIMIT

#This job runs with 1 nodes, 16 cores per node for a total of 16 tasks.

#LOAD SOFTWARE MODULES
module purge
module load cpu intel mvapich2
module load netcdf-c netcdf-cxx netcdf-fortran
module load slurm

#RUN INITIALIZATION
srun --mpi=pmi2 -n 16 ideal.exe
#RUN MODEL
srun --mpi=pmi2 -n 16 wrf.exe
```

When you're ready to submit the job to the queue:

```bash
sbatch wrf_sbatch.sh
```

Which should respond with `Submitted batch job [job number]`.

To check on the job status, type

```
squeue -u $USER
```

It will have an `R` next to the job if it is running:
```
(base) [snesbitt@login01 em_quarter_ss]$ squeue -u snesbitt
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           1164268   compute idealwrf snesbitt  R       0:07      1 exp-3-17
```

When it is complete, a `C` should appear.

You can also look at the contents of a file `rsl.out.0000`, which is a running log of the simulation.  It should give the status, and when WRF is running, it will show the current time of the simulation.

Try `tail rsl.out.0000` during the simulation:
```
(base) [snesbitt@login01 em_quarter_ss]$ tail rsl.out.0000
Timing for main: time 0001-01-01_00:58:48 on domain   1:    0.00994 elapsed seconds
Timing for main: time 0001-01-01_00:59:00 on domain   1:    0.00994 elapsed seconds
Timing for main: time 0001-01-01_00:59:12 on domain   1:    0.01001 elapsed seconds
Timing for main: time 0001-01-01_00:59:24 on domain   1:    0.00996 elapsed seconds
```

And when it is completed successfully, `squeue -u ` should say a `C` status, or nothing (it is out of the queue), and `tail rsl.out.0000` should show:

```
Timing for main: time 0001-01-01_00:59:36 on domain   1:    0.00995 elapsed seconds
Timing for main: time 0001-01-01_00:59:48 on domain   1:    0.01000 elapsed seconds
Timing for main: time 0001-01-01_01:00:00 on domain   1:    0.00997 elapsed seconds
Timing for Writing wrfout_d01_0001-01-01_01:00:00 for domain        1:    0.10085 elapsed seconds
d01 0001-01-01_01:00:00 wrf: SUCCESS COMPLETE WRF
taskid: 0 hostname: exp-3-17
(base) [snesbitt@login01 em_quarter_ss]$
```

If there is an error, the log file will end prematurely before the job is completed in the queue.  Hopefully the error message you get is explanatory.  If you get stuck, message me on Slack!