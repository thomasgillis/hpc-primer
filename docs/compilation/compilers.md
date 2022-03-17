## Compilers
{:.no_toc}

## Content
{:.no_toc}

* TOC
{:toc}

## The two steps of compiling

Compiled languages rely on the compiler to transform source codes into an executable binary.
This process involves two stages:

1. **compilation**: transform the source code (usually) `.c`/`.cpp` files into object files `.o`
2. **linking**: links the different `.o` together by matching the function calls, adding the libraries, etc.

## The compiler

Two very common compilers exist: `gcc` and `clang`.

`gcc` is a GNU compiler featuring the GNU-GPL license. It used to be very common a few years ago but it has recently lost ground in favor of `clang`.

`clang` is the LLVM-based c/c++ compiler. It transforms the source code into LLVM sub-language, which is then in turn transformed into object files.
The compiler is based on a permissive license, which makes is more attractive for companies than `gcc`. Also, the use of the LLVM-backend is shared by other languages like Julia.
Due to its permissive license LLVM and `clang` are also used by software companies (intel, nvidia) as a basis to their own compilers.

It is common in the compilation process to define environment variables containing the compiler's name. Usual names are `CC=clang` and `CXX=clang`.
For the GNU compilers it becomes `CC=gcc` and `CXX=g++` respectively.

To compile the code, simply enter
```bash
CXX [options] -o code.cpp
```

Many options are available for each compilers, see [here for GNU](https://gcc.gnu.org/onlinedocs/gcc/Invoking-GCC.html#Invoking-GCC) and [here for clang](https://clang.llvm.org/docs/UsersManual.html).

## The linker

The linker is used to put together the object files into an executable or a library. Commonly called using `ld` every compiler suite features one.
To make it easier you can link using your compiler:

```bash
CXX obj1.o obj2.o -o exe
```

You can also use the linker to create shared libraries using the `-shared` option. To create static libraries you should use `ar` instead.

### Use external libraries

To use external libraries, after having installed them you can simply request the linker to link to it:
```bash
CXX -lfancy obj1.o obj2.o -o exe
```
The linker will look for a file named `libfancy.so` in your `LIBRARY_PATH`.

When linking to external libraries, the link can be _dynamic_ or _static_.
The former means that at the start of the exe, i.e. when typing `./exe`, it will look for the different libraries (in your `LIBRARY_PATH`).
This approach is opposed to the _static_ linking where the library is included into the exe hence increasing its size.

The dynamic linking should be used whenever possible to ease its use, but it might also mean that sometimes the library are not found.
Before starting the program you can check which library is found or missing using the tool `ldd`: `ldd exe`.
Sometimes an improved version of this tool is useful, among others [libtree](https://github.com/haampie/libtree).

However, it might be convenient to encode the path to the library that you use inside the exe as an in-between solution
For example that path might be known at compile time only, or you may want to make sure that a given version of the library is used instead of the default one found in your `LIBRARY_PATH`.
To enforce the exe to look into `path/to/lib` when loading the library `libfancy.so` you can use

```bash
CXX -Lpath/to/lib -lfancy -Wl,-rpath,path/to/lib
```


## Advantages of `clang`

The compiler `clang` has some advantages compared to `gcc`, especially in the wide range of tools provided: `clang-format`, `clang-tidy`, etc.

Here is a non-exhaustive list of some features we have found useful over time:

- **sanitize**: `sanitize=address`: this option makes the debugging easy in case of segfaults, especially when used in parallel (MPI). Add the option `-fsanitize=address` to both the compiler and the linker. Similar to the previous option `sanitize=undefined` helps to detect undefined variables. Finally to improve the trace output that you get from `clang`, you can use `-O1 -fno-omit-frame-pointer`. More information are given in the [clang documentation](https://clang.llvm.org/docs/AddressSanitizer.html).

- **optimization** you can easily generate a readable optimization report (in html) from the compilation process with `-fsave-optimization-record`. Then use the provided python script to generate the html output: `python3 /usr/lib/llvm-xx/share/opt-viewer/opt-viewer.py --output-dir opt_reports build/*.opt.yaml`. Note that the exact path of the `opt-viewer.py` script might change on your system.


- **compilation database** `clang-format` and `clang-tidy` are part of the tools provided as part of the LLVM suite. In general those tools rely on the _compilation database_. The latter can be generated by the `clang` compiler as a two step process. First generate the `json` file for every source file using the options `-MJ source.o.json ` during compilation. Once done, you can gather the `json` into a single database using `sed -e '1s/^/[\n/' -e '$$s/,$$/\n]/' $^ > compile_commands.json`.


-----------------------------------------
[home](../index.md) - [compilation](compilation.md)