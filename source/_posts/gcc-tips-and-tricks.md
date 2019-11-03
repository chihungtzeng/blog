---
title: gcc tips and tricks
date: 2019-11-03 11:17:32
tags:
- gcc
- g++
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
