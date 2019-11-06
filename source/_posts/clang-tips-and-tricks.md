---
title: clang tips and tricks
date: 2019-11-04 18:53:47
tags:
- clang
categories:
- tooling
---

### sanitizers

clang and gcc have similar sanitizer flags. It is widely documented so I do not write it here :D

### static analyzer

Clang has built-in static analyzer. Other similar tools are `flawfinder`, `cppcheck`, `egypt`, `pscan`, and `splint`.

```
$ cat foo.c 
#include <stdlib.h>

int main()
{
  int *a = (int *) malloc(sizeof(int));
  return 0;
}

$ clang --analyze foo.c
foo.c:5:8: warning: Value stored to 'a' during its initialization is never read
  int *a = (int *) malloc(sizeof(int));
       ^   ~~~~~~~~~~~~~~~~~~~~~~~~~~~
foo.c:6:3: warning: Potential leak of memory pointed to by 'a'
  return 0;
  ^~~~~~~~
2 warnings generated.
```


### Show what targets are supported

```
$ llc --version
LLVM (http://llvm.org/):
  LLVM version 8.0.1
  Optimized build.
  Default target: x86_64-pc-linux-gnu
  Host CPU: skylake

  Registered Targets:
    amdgcn  - AMD GCN GPUs
    bpf     - BPF (host endian)
    bpfeb   - BPF (big endian)
    bpfel   - BPF (little endian)
    nvptx   - NVIDIA PTX 32-bit
    nvptx64 - NVIDIA PTX 64-bit
    r600    - AMD GPUs HD2XXX-HD6XXX
    x86     - 32-bit X86: Pentium-Pro and above
    x86-64  - 64-bit X86: EM64T and AMD64
```

### Show the details about optimizer

```
$ llvm-as < /dev/null | opt -O2 -disable-output -debug-pass=Arguments
Pass Arguments:  -tti -targetlibinfo -tbaa -scoped-noalias -assumption-cache-tracker -verify -simplifycfg -domtree -sroa -early-cse -lower-expect
Pass Arguments:  -targetlibinfo -tti -tbaa -scoped-noalias -assumption-cache-tracker -forceattrs -inferattrs -ipsccp -globalopt -domtree -mem2reg -deadargelim -basicaa -aa -domtree -instcombine -simplifycfg -basiccg -globals-aa -prune-eh -inline -functionattrs -domtree -sroa -early-cse -lazy-value-info -jump-threading -correlated-propagation -simplifycfg -basicaa -aa -domtree -instcombine -tailcallelim -simplifycfg -reassociate -domtree -loops -loop-simplify -lcssa -loop-rotate -basicaa -aa -licm -loop-unswitch -simplifycfg -basicaa -aa -domtree -instcombine -loops -scalar-evolution -loop-simplify -lcssa -indvars -aa -loop-idiom -loop-deletion -loop-unroll -basicaa -aa -mldst-motion -aa -memdep -gvn -basicaa -aa -memdep -memcpyopt -sccp -domtree -demanded-bits -bdce -basicaa -aa -instcombine -lazy-value-info -jump-threading -correlated-propagation -domtree -basicaa -aa -memdep -dse -loops -loop-simplify -lcssa -aa -licm -adce -simplifycfg -basicaa -aa -domtree -instcombine -barrier -basiccg -rpo-functionattrs -elim-avail-extern -basiccg -globals-aa -float2int -domtree -loops -loop-simplify -lcssa -loop-rotate -branch-prob -block-freq -scalar-evolution -basicaa -aa -loop-accesses -demanded-bits -loop-vectorize -instcombine -scalar-evolution -aa -slp-vectorizer -simplifycfg -basicaa -aa -domtree -instcombine -loops -loop-simplify -lcssa -scalar-evolution -loop-unroll -basicaa -aa -instcombine -loop-simplify -lcssa -aa -licm -scalar-evolution -alignment-from-assumptions -strip-dead-prototypes -globaldce -constmerge -verify
Pass Arguments:  -domtree
```

