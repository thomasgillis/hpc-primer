# C++ - language
{:.no_toc}

## Content
{:.no_toc}

* TOC
{:toc}

--------------------------------------------------------------------------------

# Functional design


## templates

## Substitution failure is not an error (SFINAE)

## do and don't

# C API

In order to call `C++` code from `C` you may need to provide an API.
Below is and example of how it could be done.

```c++
// file lib.hpp -> this is the library private declaration file (C++)
class MyClass{
    DoSomething();
};
```

```c++
// file lib.cpp -> this is the library private implementation file (C++)
MyClass::DoSomething(){
    // do something implementation
}
```

```c++
// file: capi.h -> this file can be read by both C and C++ compilers

// for every class accessible from the outside create a struct
typedef struct MyLib_MyClass MyLib_MyClass

// use `extern "C"` to generate C compatible function names
#ifdef __cplusplus
extern "C"{
#endif
MyLib_DoSomething(MyLib_MyClass*);
#ifdef __cplusplus
}
#endif
```

```c++
// file: capi.cpp -> the implementation of the API, C++ file

// the extern "C" is always here because it's a cpp file
extern "C" {
#include "api.h"
#include "lib.hpp"

MyLib_DoSomething(MyLib_MyClass* arg){
    // simply cast the pointer and then handle it as you would handle your normal objects!
    auto ptr = reinterpret_cast<MyClass*>(arg);
    ptr->DoSomething()
}

}
```

The main advantage of this method is that the cast works on any `C++` class.
It can be namespace or template (you must specify all the template arguments though)

```c++
// cast with namespace
auto ptr = reinterpret_cast<MyNameSpace::MyClass*>(arg);

// cast with template (specify all of them!)
auto ptr = reinterpret_cast<MyClass<double,4>*>(arg);
```

-----------------------------------------
[home](../index.md)