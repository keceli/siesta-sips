Title: How to install & run?
Category: How to?

# How to install?

Siesta-QETSc uses the spectrum slicing eigensolver in SLEPc package.
SLEPc is an extension of PETSc. Hence, we need to install PETSc initially.
PETSc also manages the installation of other libraries that are required for
the eigensolver, i.e. MUMPS, METIS, ScaLAPACK etc.


## PETSc installation:
```
git clone https://bitbucket.org/petsc/petsc
cd petsc
export PETSC_DIR=$PWD
export PETSC_ARCH=arch-gcc
git checkout c616f458c34eeb4fc62c # Temporary solution to avoid recent changes in PETSc
./configure  --download-mpich --download-fblaslapack --download-scalapack --download-mumps --download-metis --download-ptscotch --download-scalapack --with-shared-libraries=0 --with-debugging=0
make all test
```
For more information on PETSc installation check https://www.mcs.anl.gov/petsc/documentation/installation.html
For better performance use vendor specific libraries, especially for blas and lapack.
However, we do not recommend MKL, since we encountered some problems with older versions. (More info needed)

## SLEPc installation:
```
git clone https://bitbucket.org/keceli/slepcfork
cd slepcfork
export SLEPC_DIR=$PWD
./configure
make all test
```
## Siesta installation:
```
git clone https://bitbucket.org/keceli/siesta-qetsc.git
cd siesta-qetsc
export SIESTA_DIR=$PWD
mkdir $PETSC_ARCH
cd $PETSC_ARCH
sh ../Src/obj_setup.sh
../Src/configure --enable-mpi CC=${PETSC_DIR}/${PETSC_ARCH}/bin/mpicc FC=${PETSC_DIR}/${PETSC_ARCH}/bin/mpif90 
```
Edit `arch.make` file to change LIBS and FFLAGS as:
```
LIBS="${SLEPC_EPS_LIB} $(SCALAPACK_LIBS) $(BLACS_LIBS) $(LAPACK_LIBS) $(BLAS_LIBS) $(NETCDF_LIBS)" 
FFLAGS=-g -I${PETSC_DIR}/include -I${PETSC_DIR}/${PETSC_ARCH}/include -I${SLEPC_DIR}/${PETSC_ARCH}/include -I${SLEPC_DIR}/include
```
Check arch.make file to make sure LIBS includes ${SLEPC_EPS_LIB}.

# How to run?

Create input file as described in Siesta manual, or see examples in Tests or Examples folder
Currently, you can only use INPUT_DEBUG as your input file due to a problem with fdf and petsc.
Once you have INPUT_DEBUG file ready, you can simply use
```
mpiexec -n 2 $SIESTA_DIR/$PETSC_ARCH/siesta
```
to run siesta without specifying the input file, since siesta will read INPUT_DEBUG
as the default input file.

## How to use QETSc solver?

You have to set `SolutionMethod` to `qetsc` in INPUT_DEBUG, i.e.:
```
SolutionMethod  qetsc
```
Run with the following runtime options
```
mpiexec -np 2 `siesta` -options_file options.txt
```
`options.txt` file contains command line options that can be set at run time for SIESTA-QETSc runs.
Here is a sample `options.txt` file with explanations on their usage.
You can also find options.txt file and sample siesta input files in `$SIESTA_DIR/Tests/qetsc`
directory.

``` bash
# SLEPc eigensolver options
# -eps_interval a,b: where a and b are real numbers setting the global interval [a,b] for the eigenvalue search.
-eps_interval -3.0,1.0
# -eps_krylovschur_partitions n: where n is an interger setting the number of bins (slices) for dividing the global interval
# n should evenly divide the number of processors. This number has an impact on the performance.
# In general it can be set equal to the number of processors. i.e. for `mpirun -np 16 siesta -options_file options.txt`
-eps_krylovschur_partitions 16

# PETSc options
-mat_type mpisbaij # use a sym. matrix, reducing memory footprint
-log_view # Prints out a very useful profile log
-memory_view # Prints out memory usage for petsc operations. (On some systems, it doesn't work)
-mat_mumps_icntl_7 5 # Sets Metis for ordering rows/columns (Serial)

# QETSc options

# Below are the options set as default, so the user does not need to change any
#-ioptbin 7 # Sets the binning algorithm, use 0 for uniform slicing, 7 is the best nonuniform slicing
#-ioptdensity 0 # Sets the method for density matrix calculation
#-ioptinertia 1 # Perform inertia calculation until eigenvalues starts to converge
#-ioptwrite 1 # Write eigenvalues in log file, use 2 to write matrices to disk
#-roptbuffer 0.1 # Buffer value for end points of the interval
#-roptdiff 0.02 # Convergence threshold for eigenvalue, to stop inertia calculations
```

## Troubleshooting

### Missing eigenvalues
If there are missing eigenvalues, i.e SIESTA-QETSc reports:
`Not enough eigenvalues`

You can try any or all of the following:

* Increase global interval for eigenvalue range,
`-eps_interval -15,5`
* Increase roptbuffer
`-roptbuffer 0.5`
* Increase ioptinertia
`-ioptinertia 100`
* Decrease roptdiff
`-roptdiff 0.0001`

### Factorization problem
If you get an error from MUMPS during factorization, i.e.
`Error reported by MUMPS in numerical factorization phase: INFOG(1)=-9, `

You can try any or all of the following.

* Decrease number of bins (slices, partitions) using  `-eps_krylovschur_partitions`
* Change to parallel symbolic factorization
```
-mat_mumps_icntl_28 2
-mat_mumps_icntl_29 1 # ptscotch
#-mat_mumps_icntl_29 2 # parmetis
```
* Set available memory per core in MB:
`-mat_mumps_icntl_23 2500`
If none of these help, the problem might be insufficient memory for the machine you use, either 
decrease the problem size or switch to a computer with larger memory per core.


## More configure options from Siesta manual:

* -DMPI_TIMING: to obtain the accounting of MPI communication times in parallel executions
* -DGRID_SP: to use single-precision for all the grid
magnitudes, including the orbitals array and charge densities and potentials. This will
cause some numerical dierences and will have a negligible eect on memory consumption,
since the orbitals array is the main user of memory on the grid, and it is single-precision
by default. This setting will recover the default behavior of previous versions of Siesta.
* -DGRID_DP: to use double-precision for all the grid
magnitudes, including the orbitals array. This will significantly increase the memory used
for large problems, with negligible diferences in accuracy.
* -DBROYDEN_DP: to use double-precision arrays for the
Broyden historical data sets. (Remember that the Broyden mixing for SCF convergence
acceleration is an experimental feature.)
* -DON_DP: to use double-precision for all the arrays
in the O(N) routines.
