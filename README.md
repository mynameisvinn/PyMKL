# PieMKL
a simple example of a python wrapper around intel mkl for fast matrix multiplies.

## what is mkl?
mkl is intel's blas (for linalg operations like matrix multiplies) & lapack (for linalg solvers eg eigendecomposition) implementation for x64. other implementations include [atlas](http://math-atlas.sourceforge.net/), which is used by matlab, and [openblas](https://github.com/xianyi/OpenBLAS), numpy's default blas implementation. (you can see which implementation numpy uses with `np.__config__.show()`).

## getting started with mkl
get mkl the easy way with 
```bash
conda install -c anaconda mkl
``` 
to find it, do
```python
import ctypes
ctypes.util.find_library('mkl_rt')
```