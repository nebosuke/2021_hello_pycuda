# pycuda によるカーネルの書き方サンプル

## pycuda のインストール

```
pip3 install --user -U pycuda
```

### xlocale.h が見つからないエラーでインストールできない場合

インストール時に、以下のように xlocale.h が見つからないエラーが発生した場合は、xlocale.h のシンボリックリンクを作成することでインストールできるようになる。

```
numpy/core/src/multiarray/numpyos.c:18:10: fatal error: xlocale.h: No such file or directory
 #include <xlocale.h>
          ^~~~~~~~~~~
compilation terminated.
numpy/core/src/multiarray/numpyos.c:18:10: fatal error: xlocale.h: No such file or directory
 #include <xlocale.h>
          ^~~~~~~~~~~
compilation terminated.
error: Command "aarch64-linux-gnu-gcc -pthread -DNDEBUG -g -fwrapv -O2 -Wall -g -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2 -fPIC -DHAVE_NPY_CONFIG_H=1 -D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE=1 -D_LARGEFILE64_SOURCE=1 -Ibuild/src.linux-aarch64-3.6/numpy/core/src/private -Inumpy/core/include -Ibuild/src.linux-aarch64-3.6/numpy/core/include/numpy -Inumpy/core/src/private -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/usr/include/python3.6m -Ibuild/src.linux-aarch64-3.6/numpy/core/src/private -Ibuild/src.linux-aarch64-3.6/numpy/core/src/private -Ibuild/src.linux-aarch64-3.6/numpy/core/src/private -c numpy/core/src/multiarray/numpyos.c -o build/temp.linux-aarch64-3.6/numpy/core/src/multiarray/numpyos.o" failed with exit status 1
```

<br>シンボリックリンクを作成する。

```
sudo ln -s /usr/include/aarch64-linux-gnu/bits/types/__locale_t.h /usr/include/xlocale.h
```

## 実行方法

```
python3 hello_pycuda.py
```

## プログラムの解説

### カーネル(GPUコード)の定義

```python3
from pycuda.compiler import SourceModule

mod = SourceModule("""
__global__ void add_matrix_gpu(const float* __restrict__ dMat_A, float* __restrict__ dMat_B, float *dMat_G, const int mat_size_x, const int mat_size_y) {
    int mat_x = threadIdx.x + blockIdx.x * blockDim.x;
    int mat_y = threadIdx.y + blockIdx.y * blockDim.y;
    if (mat_x >= mat_size_x) {
        return;
    }
    if (mat_y >= mat_size_y) {
        return;
    }
    const int index = mat_y * mat_size_x + mat_x;
    dMat_G[index] = dMat_A[index] + dMat_B[index];
}
""")
add_matrix_gpu = mod.get_function("add_matrix_gpu")
```

### GPUコードの実行

```python3
block = (BLOCKSIZE, BLOCKSIZE, 1)
grid = ((MAT_SIZE_X + block[0] - 1) // block[0], (MAT_SIZE_Y + block[1] - 1) // block[1])

h_a = numpy.random.randn(MAT_SIZE_X, MAT_SIZE_Y).astype(numpy.float32)
h_b = numpy.random.randn(MAT_SIZE_X, MAT_SIZE_Y).astype(numpy.float32)
h_d = numpy.empty_like(h_a)

add_matrix_gpu(cuda.In(h_a), cuda.In(h_b), cuda.Out(h_d), numpy.int32(MAT_SIZE_X), numpy.int32(MAT_SIZE_Y), block = block, grid = grid)
```
