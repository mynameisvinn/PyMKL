# PieMKL
python wrappers around intel mkl.

mkl is intel's blas (for linalg operations like matrix multiplies) and lapack (for linalg solvers eg eigendecomposition) implementation for x64. other blas/lapack implementations include [atlas](http://math-atlas.sourceforge.net/), matlab's blas implementation, and [openblas](https://github.com/xianyi/OpenBLAS), numpy's default blas implementation (you can see which implementation numpy uses with `np.__config__.show()`).

## get it get it
get mkl the easy way with 
```bash
conda install -c anaconda mkl
``` 
to find mkl binary and bring it to python land, do
```python
import ctypes
ctypes.util.find_library('mkl_rt')
```

## calling mkl with native python
lets look at a simple blas function [`scopy`](https://software.intel.com/content/www/us/en/develop/documentation/mkl-developer-reference-c/top/appendix-e-code-examples/blas-code-examples.html). the `s` in `scopy` means we are copying a vector X of single precision elements to another vector Y.

### a) bring scopy into python land
once we find the mkl binary, we can load specific methods with 
```python
mkl = cdll.LoadLibrary("./libmkl_rt.dylib")
scopy = mkl.cblas_scopy  # other methods include dgemm, etc
print(vars(mkl))
```

### b) identify function signature
according to [documentation](https://software.intel.com/content/www/us/en/develop/documentation/mkl-developer-reference-c/top/appendix-e-code-examples/blas-code-examples.html), we know `scopy` looks like `cblas_scopy(n, x, incx, y, incy)`, where `n` is an `int` specifying number of elements in vectors `X` and `Y`; `x` is a pointer (a float pointer) to vector X; `incx` is an `int` specifying how `X` increments (usually 1). 

### c) set up arguments
we will use a 10-element vector so `n=10`. 

we can set up a 10-element vector of single precision floats for `X` like so:
```python
x_vector = c_float * n  # use ctype's c_float to preallocate a 10 element array
X = x_vector(1, 1, 3, 4, 5, 6, 7, 8, 9, 10)
```
and do the same for `Y`:
```python
y_vector = c_float * n
Y = y_vector(0, 0, 0, 0, 0, 0, 0, 0, 0, 0)
```

### d) pass arguments to `scopy` but with the correct types
before we can pass python objects to mkl's c method, we use `ctypes` to recast types.

that means `n`, a python object with value = 1, needs to be converted into a c int of value = 1. 

that means we cant pass `X`, a python object that holds a c_float array, to `scopy`; we need to pass a c pointer to that c_float array to `scopy`.

```python
scopy(c_int(n), byref(X), c_int(incx), byref(Y), c_int(incy))  # results are in Y
```

### e) view results
```python
for i in Y:
    print(i)  # should be a copy of vector X
```

