# pycuda によるカーネルの書き方サンプル

## pycuda のインストール

```
pip3 install --user -U pycuda
```

### xlocale.h が見つからないエラーが発生する場合

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

シンボリックリンクを作成する。

```
sudo ln -s /usr/include/aarch64-linux-gnu/bits/types/__locale_t.h /usr/include/xlocale.h
```

## pycuda の利用方法


