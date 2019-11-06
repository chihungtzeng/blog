---
title: gcc tips and tricks
date: 2019-11-03 11:17:32
tags:
- gcc
- g++
- clang
categories:
- tooling
---

Here are the tips and tricks that I collected along the way during my years as a software engineer. Many tricks work in g++, too.  Some of them might be out of date now.  I keep them short in order to give an overview about the toolchain so that people can use google their howto/tutorials.


### Show default compiler options

```
$ gcc -dM -E - < /dev/null
#define __SSP_STRONG__ 3
#define __DBL_MIN_EXP__ (-1021)
#define __FLT32X_MAX_EXP__ 1024
#define __UINT_LEAST16_MAX__ 0xffff
...
```

### Show default header inclusion paths

```
$ gcc -E -Wp,-v - < /dev/null
ignoring nonexistent directory "/usr/local/include"
ignoring nonexistent directory "/usr/lib/gcc/x86_64-pc-linux-gnu/8.3.0/../../../../x86_64-pc-linux-gnu/include"
#include "..." search starts here:
#include <...> search starts here:
 /usr/lib/gcc/x86_64-pc-linux-gnu/8.3.0/include
 /usr/lib/gcc/x86_64-pc-linux-gnu/8.3.0/include-fixed
 /usr/include
End of search list.
...
```

### work with pkg-config to use other libraries

```
$ g++ `pkg-config --cflags --libs opencv` foo.cpp
```

The above pkg-config command is expanded by shell.

```
$ pkg-config --cflags --libs opencv
-I/usr/include/opencv -lopencv_shape -lopencv_stitching -lopencv_superres -lopencv_videostab -lopencv_aruco -lopencv_bgsegm -lopencv_bioinspired -lopencv_ccalib -lopencv_dnn_modern -lopencv_dnn_objdetect -lopencv_dpm -lopencv_face -lopencv_photo -lopencv_freetype -lopencv_fuzzy -lopencv_hfs -lopencv_img_hash -lopencv_line_descriptor -lopencv_optflow -lopencv_reg -lopencv_rgbd -lopencv_saliency -lopencv_stereo -lopencv_structured_light -lopencv_phase_unwrapping -lopencv_surface_matching -lopencv_tracking -lopencv_datasets -lopencv_text -lopencv_dnn -lopencv_video -lopencv_plot -lopencv_ml -lopencv_ximgproc -lopencv_calib3d -lopencv_features2d -lopencv_highgui -lopencv_videoio -lopencv_flann -lopencv_xobjdetect -lopencv_imgcodecs -lopencv_objdetect -lopencv_xphoto -lopencv_imgproc -lopencv_core
```

### static stack usage analysis

Pass -fstack-usage to gcc.

Related:
- -fcallgraph-info
- -fdump-tree-optimized
- scripts/checkstack.pl in Linux kernel source.

### gcc function trace

```
$ gcc -finstrument-functions foo.c
```

The above command inserts ```__cyg_profile_func_enter()``` and ```__cyg_profile_func_exit()``` before and after a function call.
To disable it for certain functions, try:

```c
void __attribute__((__no_instrument_function__)) __cyg_profile_func_enter(void *this_func, void *caller)
```

### dump machine

Useful when you are working on cross-compiling.

```
$ g++ -dumpmachine
x86_64-pc-linux-gnu
```

### weak/strong reference

```
__attribute__((weak))
__attribute__((weakref)) void foo();
```

If foo() is not defined, its address is 0.


### Turn .a into .so

```
gcc -shared -o output.so libfoo.a
```

### Check unsafe code

#### Sanitizer

There are three major kinds of sanitizers: address, thread and undefined. This field is under heavy development and more sanitizers might come. I personally use it with -g options to show full stack trace.

```
gcc -fsanitize=address
gcc -fsanitize=thread
gcc -fsanitize=undefined

# Other variants
gcc -fsanitize=kernel-address
gcc -fsanitize=leak
```


#### stack protector

Replace unsafe calls like gets()

```
gcc -fstack-protector -D_FORTIFY_SOURCE
```

#### 32-bit addition overflow

```
gcc -ftrapv
```

#### Mudflap Pointer Debugging (gcc < 4.9)

```
gcc -fmudflap -lmudflap
```

Use MUDFLAP_OPTIONS to do run time checking.

### list header inclusion

```
$ cat foo.c
#include <stdio.h>

int main()
{
  return 0;
}

$ gcc -H foo.c
. /usr/include/stdio.h
.. /usr/include/bits/libc-header-start.h
... /usr/include/features.h
.... /usr/include/sys/cdefs.h
..... /usr/include/bits/wordsize.h
...
```

### Show compile time

```
$ gcc -ftime-report foo.c

Time variable                                   usr           sys          wall               GGC
 phase setup                        :   0.00 (  0%)   0.00 (  0%)   0.00 (  0%)    1243 kB ( 71%)
 phase parsing                      :   0.00 (  0%)   0.00 (  0%)   0.01 (100%)     436 kB ( 25%)
 preprocessing                      :   0.00 (  0%)   0.00 (  0%)   0.01 (100%)     127 kB (  7%)
 TOTAL                              :   0.00          0.00          0.01           1744 kB
```

### Show optimization items

```
$ gcc -O2 -Q --help=optimizers
The following options control optimizations:
  -O<number>                  		
  -Ofast                      		
  -Og                         		
  -Os                         		
  -faggressive-loop-optimizations 	[enabled]
  -falign-functions           		[disabled]
  -falign-functions=          		16
  -falign-jumps               		[disabled]
  -falign-jumps=              		16
...
```

For LLVM/clang:

see another post: clang-tips-and-tricks.

