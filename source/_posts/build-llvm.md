---
layout:     post
title: Building and Running LLVM-Clang Static Analyzer
date: 2020-06-02 20:17:19
subtitle: ""
author:     "Nithin"
header-img: "llvm.png"
catalog: true
tags:
    - LLVM
    - Clang
    - Static-Analysis
---
## Building LLVM From source
I am documenting how I am building LLVM Clang in my Mac Air 2015.
- macOS Mojave 10.14.6
- 1.6 GHz Intel Core i5
- 8 GB Memory
### Get the source code 
`git clone https://github.com/llvm/llvm-project.git`
### Build the code (I am using ninja to build)
```
mkdir $ROOT/llvm-project/build
cd $ROOT/llvm-project/build
cmake -G Ninja \
    -DDEFAULT_SYSROOT="$(xcrun --show-sdk-path)" \
    -DLLVM_ENABLE_PROJECTS="clang;libcxx;libcxxabi" \
    -DCMAKE_BUILD_TYPE=Release ../llvm
ninja clang
ninja cxx
```
Apparently we need to build libc++: by running `ninja cxx` other wise we get some header errors.
### Running LLVM-Clang Static Analyzer
To run a static analyzer on `test.cpp` file, we can use `scan-build` utility with below command.
```
$ROOT/llvm-project/clang/tools/scan-build/bin/scan-build -k -V \
    --use-analyzer $ROOT/llvm-project/build/bin/clang -o . clang -c ./test.cpp
```
**Options**
- `-V` option it will open the report in the browser
- `-k` keep on going option
- `c`  Only run preprocess, compile, and assemble steps

![alt text](/2020/06/02/build-llvm/report.png "Report")

Or we can use it via `clang` to run a specific check. Here for example `NullDereference`
```
$ROOT/llvm-project/build/bin/clang++ -cc1 -analyze -analyzer-checker=core.NullDereference test.cpp 
```
### Summary
Full build took around 2.5 hours in my machine.
Building after a small change took around 1 minute.

---
- [How to build LLVM from source](https://quuxplusone.github.io/blog/2019/11/09/llvm-from-scratch/)
