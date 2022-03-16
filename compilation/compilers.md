## Compilers

Compiled languages rely on the compiler to transform source codes into an executable binary.
This process involves two stages:

- **compilation** transform the source code (usually) `.c`/`.cpp` files into object files `.o`
- **linking** links the different `.o` together by matching the function calls, adding the libraries, etc.

### The compiler choice

Two very common compilers exist: `gcc` and `clang`.

`gcc` is a GNU compiler featuring the GNU-GPL license. It used to be very common a few years ago but is now loosing some share in favor of `clang`.

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


