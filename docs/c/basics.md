# C - basics
{:.no_toc}

## Content
{:.no_toc}

* TOC
{:toc}

## Datatypes

### Elementary types
you have a few elementary types, among others:

- `int` an integer
- `float` single precision floating point number
- `double` double precision floating point number
- `char` a character

### Structure

There is also the possibility to define structures, a.k.a. custom datatypes

```c++
typedef struct{
    int a;
    double b;
} my_struct;

my_struct s;
s.a = 8;
s.b = M_PI;
```

### Enum

It is also possible to define a type that can only take a few given values, know as an `enum`.

```c++
typedef enum{
    A, B, C
} my_enum;


my_enum list;
if (list == A){
    // do something
} else if (list == B){
    // do something else
}
```


## Macros

Macros are directly replaced (verbatim) in the code.
Some are already defined such as `M_PI` which is the value of pi.

You can also define your own macros (you should be careful though, it can be very bug-prone):

```c++
#define SIZE 89

#define m_sign(a)                                                \
    ({                                                           \
        double m_sign_a_    = (a);                               \
        double m_sign_zero_ = 0;                                 \
        (m_sign_zero_ < m_sign_a_) - (m_sign_a_ < m_sign_zero_); \
    })
```

## Memory and pointers

The advantage of C is that the memory management must be done by the user. It may also lead to the very (in)famous `SEGFAULT` termination of your program.

### Memory allocation


### Pointers


## Functions

### Function definition

a function has a retu

### Function `main`

every code must contain the main function
```c++
int main(int argc, char**argv){

    return 0;
}
```

if the code returns `0`, it means success while another value means that an error occured.

- the `argc` and `argv` arguments are used to capture the command line. For example calling `./exe --option1 --flag2` will lead to `argc = 3` and `argv = {"./exe", "--option1", "--flag2"}`.



## Standard library

C comes with a standard library providing an implementation for all the commonly used functions

### Print and logs

- `printf`

### String management

In C the string are stored as a 

### 


-----------------------------------------
[home](../index.md) - [compilation](c.md)