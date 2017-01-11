#SIESTA-QETSc

As defined in [SIESTA web page](http://departments.icmab.es/leem/siesta/) *"SIESTA is both a method and its computer program implementation,
to perform efficient electronic structure calculations and ab initio molecular dynamics simulations of molecules and solids."*

SIESTA-QETSc is a branch of SIESTA distinguished by
a state-of-the-art parallel sparse eigensolver, which significantly improves the
performance of the code for large calculations i.e. more than a few hundred atoms
or a few thousand basis functions.

For more information on the eigensolver you can check the following papers:

1. Zhang, H.; Smith, B.; Sternberg, M.; Zapol, P. SIPs: Shift-and-Invert Parallel Spectral Transformations. ACM Trans. Math. Softw. 2007, 33, 9–es.
2. Campos, C.; Román, J. E. Strategies for Spectrum Slicing Based on Restarted Lanczos Methods. Numer. Algorithms 2012, 60, 279–295.
3. Keçeli, M.; Zhang, H.; Zapol, P.; Dixon, D. A.; Wagner, A. F. Shift-and-Invert Parallel Spectral Transformation Eigensolver: Massively Parallel Performance for Density-Functional Based Tight-Binding. J. Comput. Chem. 2016, 37, 448–459.


This project is supported by
Laboratory Directed Research and Development (LDRD) funding from Argonne
National Laboratory, provided by the Director, Office of Science, of the
U.S. Department of Energy under Contract No. DE-AC02-06CH11357.
