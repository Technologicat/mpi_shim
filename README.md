## mpi_shim

MPI support wrapper for optional dependency on mpi4py

### Introduction

This module is designed to be copied into projects that optionally depend on MPI, to unify handling of MPI and non-MPI cases.

To load MPI, import this module. The module can always be imported, regardless of whether `mpi4py` is installed. The availability of the `mpi4py` library can then be queried at runtime using `mpi_shim.has_mpi()`.

Some rudimentary wrappers (`get_size()`, `get_rank()`, `get_comm_world()`) are provided; all the rest should be done directly via `mpi4py`, and only if `has_mpi()` returns True. This way, the same code can run both with and without MPI.

Also provided is `gather_varlength_array()`, a buffer-based fast gather for rank-1 NumPy arrays of varying lengths (like `gatherv`, but determining the sizes automatically).

### Example

Based on [http://mpi4py.scipy.org/docs/usrman/tutorial.html](http://mpi4py.scipy.org/docs/usrman/tutorial.html):

```python
import mpi_shim

MPI    = mpi_shim.get_mpi()         # either a reference to mpi4py.MPI, or None if n/a
nprocs = mpi_shim.get_size()        # actual value from mpi4py, or 1    if n/a
my_id  = mpi_shim.get_rank()        # actual value from mpi4py, or 0    if n/a
comm   = mpi_shim.get_comm_world()  # actual value from mpi4py, or None if n/a

# The rest works as usual:

data = (my_id+1)**2  # <insert meaningful calculation here>
if MPI is not None:
   data = comm.gather(data, root=0)

if my_id == 0:
   for i in range(nprocs):
       assert data[i] == (i+1)**2
else:
   assert data is None
```

### License

[BSD](LICENSE.md). Copyright 2012-2017 Juha Jeronen and University of Jyväskylä.

