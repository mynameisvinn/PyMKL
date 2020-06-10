# PieMKL
a simple example of a python wrapper around intel mkl for fast matrix multiplies.

## what is mkl?
mkl is intel's blas implementation for x64.

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