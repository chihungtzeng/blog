---
title: binary hacks
date: 2019-11-05 19:05:00
tags:
categories:
- tooling
---

This is a memo of how I work with binary files in Linux system. For a more systematic study of this topic, I found the following two books quite useful. Their authors are Japenese, and I am not sure if there are Enghlish version.

![binary_hacks.jpg](binary_hacks.jpg)


### Show defined symbols in a shared library

```
$ nm -C -D --defined-only /usr/lib/libopencv_imgproc.so
000000000048b738 B __bss_start
0000000000163880 T cv2DRotationMatrix
0000000000048360 T cvAcc
...
```

### Show .debug_info section contents

See symbol values

```
$ readelf --debug-dump=info foo.o
Contents of the .debug_info section:

  Compilation Unit @ offset 0x0:
   Length:        0xcb (32-bit)
   Version:       4
   Abbrev Offset: 0x0
   Pointer Size:  8
...
```

### Show source code paths

Sometimes we get a spurious shared library, and we want to find how it was compiled. We can use `strings` to do so.

```
$ strings -f /usr/lib/libopencv_imgproc.so
```

It actually prints all the strings in a binary file. We need to inspect the ouput to catch the path.

### Dump the contents of a section

For example, we want to dump the .text section of libopencv_imgproc.so

```
$ objdump -h /usr/lib64/libopencv_imgproc.so | grep .text
 10 .text         001e0a84  000000000003aa80  000000000003aa80  0003aa80  2**4
$ dd if=/usr/lib64/libopencv_imgproc.so of=output.bin bs=1 count=$[0x001e0a84] skip=$[0x0003aa80]
```
The output file is output.bin.


### Check ABI compatibility

abi-comliance-checker

### Check module dependency

elf-dependency-walker

### Recompile related *.c when .h is modified

autodependencies with GNU make

### Check what compiler that generates a binary file

```
$ readelf -p .comment foo.so

String dump of section '.comment':
  [     0]  GCC: (Gentoo 8.2.0-r6 p1.7) 8.2.0
  [    22]  GCC: (Gentoo 8.3.0-r1 p1.1) 8.3.0

```

or

```
$ objdump -s --section .comment foo.o
```
