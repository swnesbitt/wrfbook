# Python on expanse

===================

## Installing `python` and `jupyter notebooks` on expanse

`python` is installed on a user basis.  Follow the directions here, under the section "Software Prerequisites": [https://hpc-training.sdsc.edu/notebooks-101/notebook-101.html#software-prerequisites](https://hpc-training.sdsc.edu/notebooks-101/notebook-101.html#software-prerequisites)

## Installing `reverse-proxy` for accessing jupyter notebooks

We'll follow the directions here: [https://hpc-training.sdsc.edu/notebooks-101/notebook-101.html#security-with-reverse-proxy-service](https://hpc-training.sdsc.edu/notebooks-101/notebook-101.html#security-with-reverse-proxy-service)

## Installing required python packages `matplotlib`, `netcdf4`, and `wrf-python` on expanse

```bash
conda install -c conda-forge matplotlib wrf-python
```

## Starting up a jupyter notebook

```bash
(base) [snesbitt@login02 reverse-proxy]$ ./start-jupyter -A uic406
Your notebook is here:
	https://census-dragging-repent.expanse-user-content.sdsc.edu?token=fef373e07c8b0736c2ecc3219fd962e9
If you encounter any issues, please email help@xsede.org and mention the Reverse Proxy Service.
No time given. Default is 30 mins
Using ./slurm_expanse/notebook.sh
Your job id is 1199869
You may occasionally run the command 'squeue -j 1199869' to check the status of your job
```

## Basic notebook for wrf-analysis

Paste the text into the notebook cells, press Control-Enter (PC) or Command-Return (Mac)

```python
# set up plotting and matplotlib
%pylab inline
# load modules
from netCDF4 import Dataset
from wrf import getvar
```


```python
# define path to file
ncfile = Dataset("/expanse/lustre/scratch/snesbitt/temp_project/WRF/test/em_quarter_ss/wrfout_d01_0001-01-01_00:00:00")
# Load a variable
p = getvar(ncfile, "P")
p
```

Basic plotting:

```python
# select the lowest model level and plot a plan view
p.sel(bottom_top=0).plot()
```
