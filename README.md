# MPI DBSCAN Implementation
This repository has a running version of the Disjoint-Set Data Structure based Parallel DBSCAN clustering implementation (MPI version) by **Md. Mostofa Ali Patwary** from *EECS Department, Northwestern University* (mpatwary@eecs.northwestern.edu)

The code from [dbscan-v1.0.0.tar.gz](http://cucis.ece.northwestern.edu/projects/Clustering/download_code_dbscan.html) is used with the changes applyed by @dhoule in [Parallel-DBSCAN](https://github.com/dhoule/Parallel-DBSCAN) repository and also several memory leak fixes of my own.

## Why this version
The major differences in this repository are:
- The `Makefile` that is now more comprehensive and easy to use
- Fixed the runtime error with optimization flags like `-O3`
- Several memory leaks solved with *valgrind*
- Tested parallel and serial with:
    | Library  | Version  |
    | -------- | -------- |
    | GCC      | 9.3.1    |
    | Valgrind | 3.15.0   |
    | MPICH    | 3.3.1    |
    | Open MPI | 4.0.1    |
    | PnetCDF  | 1.12.1   |
    | Boost    | 1.68.0   |

# Dependencies
The code has some clear dependencies:

### MPI
Two different MPI libraries were tested with this code. The first one is OpenMPI that can be downloaded [here](https://www.open-mpi.org/software/ompi/v4.0/) and the second one is MPICH that can be downloaded [here](https://www.mpich.org/downloads/2/).
The example below is for OpenMPI library:
```sh
$ wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.5.tar.gz
$ tar -xf openmpi-4.0.5.tar.gz
$ cd openmpi-4.0.5
$ mkdir build
$ cd build
# Replace <YourInstalationDir> with a new directory to install MPI to
$ ../configure --prefix=<YourInstalationDir>
$ make -j
$ make install
```

### PnetCDF
The code can be downloaded [here](https://parallel-netcdf.github.io/wiki/Download.html).
To install for this code usage, the following steps and configurations will do:
```sh
$ wget https://parallel-netcdf.github.io/Release/pnetcdf-1.12.1.tar.gz
$ tar -xf pnetcdf-1.12.1.tar.gz
$ cd pnetcdf-1.12.1
$ mkdir build
$ cd build
# Replace <YourInstalationDir> with a new directory to install PnetCDF to
# Replace <YourMpiInstalationDir> for the folder containing the MPI instalation
$ ../configure --prefix=<YourInstalationDir> --with-mpi=<YourMpiInstalationDir> CC=mpicc --enable-shared
$ make -j
$ make install
```

### Boost
The code can be downloaded [here](https://www.boost.org/users/download/). And the instalation steps are something like the following:
```sh
$ wget https://dl.bintray.com/boostorg/release/1.74.0/source/boost_1_74_0.tar.gz
$ tar -xf boost_1_74_0.tar.gz
$ cd boost_1_74_0
# Replace <YourInstalationDir> with a new directory to install Boost to
$ ./bootstrap.sh --prefix=<YourInstalationDir>
$ ./b2 install --with-atomic --with-chrono --with-thread --with-system --with-filesystem
```

Uppon installing these libraries one should add the instalation paths to the Makefile like:
```makefile
# Dependencies
# ----------------
PNET_DIR = /opt/apps/pnetcdf/1.12.1-gcc-9.1.3-mpich-3.3.1
MPI_DIR = /opt/apps/mpich/3.3.1-gcc-9.1.3
BOOST_DIR = /opt/apps/boost/1.68.0-gcc-9.1.3
```

# Build and Run
The build is straight forward and can be done using the original instructions

1. Compile the source files using the following command
```
make -j
```
2. Test serial (1 process) with available dataset:
```
make test
```
3. Run parallel in command line with something like:
```
mpirun -n 4 ./mpi_dbscan -i ../datasets/clus50k.bin -b -m 5 -e 25 -o out_clusters.nc
```

# File Structure
1. Input file:
	- Binary file format
    - Written in a single column with number of points (4 bytes `N`), number of dimensions ( 4 bytes`D`)  followed by the points coordinates (N x D floating point numbers).

2. Output file:
    - Optional
    - netCDF file format
	    The coodinates are named as columns (position_col_X1, position_col_X2, ...) and then one additional column named cluster_id for the corresponding cluster id the point belong to. 

3. Script `bin/createBinaryFile.py`
    - Can be used to create the data sets needed to use this code. The comments in the script explain how to set up the file, before converting it to binary format. You will have to manually change the "input" and "output" file variables, though.

# Reference
> Md. Mostofa Ali Patwary, Diana Palsetia, Ankit Agrawal, Wei-keng Liao,
Fredrik Manne, and Alok Choudhary, "A New Scalable Parallel DBSCAN
Algorithm Using the Disjoint Set Data Structure", Proceedings of the
International Conference on High Performance Computing, Networking,
Storage and Analysis (Supercomputing, SC'12), pp.62:1-62:11, 2012.
