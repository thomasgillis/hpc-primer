# C - language
{:.no_toc}

## Content
{:.no_toc}

1. TOC
{:toc}

--------------------------------------------------------------------------------

## Datatype and variables

### Elementary types
you have a few elementary types, among others:

- `int` an integer
- `float` single precision floating point number
- `double` double precision floating point number
- `char` a character


### Arrays

We can declare an array (more to be detailed below) using the `[size]` after the array's name.
The array can then be accessed using `[i]` where `i` goes from `0` to `size-1`.

A deeper explanation of the arrays comes with the pointers section.

```c++
double b[10]; // we have now 10 doubles

b[0] = 10;
b[9] = 0;
```

### Variable's scope

The scope of a variable can be interpreted as its lifetime.
In C every variable that is declared within `{}` will be unaccessible outside.

```c++
// b is unknown here
{
    int b = 9;
}
// b is unknown here
```

### Custom type definition with `typedef`

To shorten the code or defines type alias you can use the `typedef` directive:

```c++
typedef int my_int;
typedef double real;

my_int a = 9;
real b = 5.7;
```

This is particularly convenient when dealing with `enum` and `struct` as described below.

### Structure with `struct`

There is also the possibility to define structures, a.k.a. custom datatype

```c++
// this is the structure definition
struct MyLongStructureName{
    int a;
    double b;
};

// we can now use the structure
struct MyLongStructureName s;
s.a = 8;
s.b = M_PI;
```

To avoid very tedious and long structure names, we can use `typedef` as follows

```c++
// as the structure is 
struct MyFirstLongStructureName{
    int a;
    double b;
};
typedef struct MyFirstLongStructureName FirstStruct;

FirstStruct s;

// or equivalently
typedef struct MySecondLongStructureName{
    int a;
    double b;
} SecondStruct;

SecondStruct s;
```

In some of the oldest versions of C, you had to specify both `MySecondLongStructureName` and `SecondStruct`.
However, in the most recent versions (and in C++) you can simply use an un-named structure directly in the `typedef`, which makes it even more convenient.

```c++
typedef struct {
    int a;
    double b;
} SecondStruct;
```


### List of possible values with `enum`

It is also possible to define a type that can only take a few given values, know as an `enum`.

```c++
typedef enum{
    A, B, C
} MyEnum;


MyEnum list = B;

if (list == A){
    // do something
} else if (list == B){
    // do something else
}
```

The values given to `A`, `B`, and `C` are chosen by the compiler and usually are given in the order of declaration: `0`, `1`, ...
However, we can also enforce some of the values if some computations must be done on them:

```c++
typedef enum{
    A = 9,
    B = 3,
    C = 2
} MyEnum;

MyEnum list = B;

if((B%2) == 0){
    // do something
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

### Pointers

A pointer is the name given to an address in memory __pointing__ to a variable.
The type of a pointer is defined based to the type of the variable its pointing to:
`int *` is a pointer to an `int`, `double *` is a pointer to a `double`, etc.

You can get the memory address of a variable using `&`, while you can access the memory associated to the pointer using `*`.

```c++
// define a memory location of an integer and assign 9
int a = 9;

// store the address of that location
int* ptr_a = &a;

// set the memory location to 8 through the pointer
(*ptr_a) = 8;

// now a is 8
```

### Pointer arithmetic

The pointers are both powerful and dangerous, especially when doing "pointer arithmetic".
Useful to understand the arithmetic the function `sizeof()` returns the size (in bytes `1 byte = 8 bits`) of the variable in memory.

```c++
sizeof(int); // returns the size of an int in memory, usually 4 bytes
sizeof(double); // returns the size of a double in memory, usually 8 bytes
sizeof(void); // returns the size of a void in memory, 1 bytes
```

The biggest trick to understand is that the memory space of a pointer is the same independently of the type that it points to!

```c++
printf("sizeof(double*) = %ld\n",sizeof(double*)); // returns 8
printf("sizeof(int*) = %ld\n",sizeof(int*)); // returns 8
printf("sizeof(void*) = %ld\n",sizeof(void*)); // returns 8
```

Therefore, a pointer can always be cast around in a `void*`, and the `int` or `double` information is used as the "unit" associated to the pointer.

Actually this is best illustrated when comparing the result of a `+ i` operation on the pointer.
If the pointer is declared as `T * ptr`, then the `+ i` operation returns `ptr + i * sizeof(T)`:

```c++
// get the pointer to a
int a = 9.0;
int * ptr_a = &a;

// we can cast the pointer to a void *
void * ptrv_a = ptr_a;

printf("address after a = %ld\n",ptr_a); // returns the memory address (expressed in a decimal number!)
printf("next address after a = %ld\n",ptr_a+1); // returns the address + 4 
printf("next address after a = %ld\n",ptrv_a+1); // returns the address + 1
```

Another example would be to use an array of integers.

```c++
int a[4] = {0,1,2,3};

// we take the address of the first element of the array (see later for a better way of doing this)
int *ptr_a =(int*)(&a[0]);
double *ptrd_a =(double*)(&a[0]);

printf("value in ptr_a + 1 = %d",*(ptr_a+1)); // prints 1 
printf("value in ptrd_a + 1 = %d",*(int*)(ptrd_a+1)); // prints 2 as sizeof(double) = 2 * sizeof(int)

// NB: the cast back to an int* is mandatory here so that only 4 bytes will be read in memory by `printf` instead of 8
```

To simplify the syntax, the C has defined a more convenient way than `*(ptr_a+1)` which is `ptr_a[1]`.
Both notation are completely equivalent!

With that notation we can revisit the array explained earlier.
When declaring an array like `double b[10]` what is returned is actually a pointer of type (double *).
Then, when accessing the fourth value, `b[4]` what is actually performed is `*(b+4)`.

This now opens the door to the memory allocation and the memory management by the programmer.



### Memory allocation

There are two main part of your memory that you can use: the **heap** and the **stack**.
The exact differences are beyond the scope of this document but it is useful to remember that

- the **stack** contains temporary values (and small arrays)
- the **heap** is used to contain longer lasting values and bigger arrays

When the size of the variable is know at compile time, is it allocated on the stack

```c++
int a ;
double b[400];
```

here a and b are in the stack.
The limitation is that the memory is automatically free'd at the end of the variable's scope.

The heap can be used by the programmer to store longer lasting information.
As there is no free lunch, the programmer must now explicitly request and free the memory needed.

```c++
// request the allocation of 10 * 8 bytes = 80 bytes for the array
double * b = malloc(10 * sizeof(double));

// release the memory as it is not needed anymore
free(b);
```


## Functions

A function has both a name, an argument list (+ types!), and a return type

```c++
// this function takes a double as input and returns a double
double function_1(double b){
    return b*2.0;
}
// this function takes a pointer to int as input but has no output
void function_1(int * b);
    b[0] = 9.0;
```

### Function declaration vs definition

In C (and C++) a function has both a declaration and a definition.

- **the declaration** tells the compilers (and the rest of the code) about the function names, input arguments and return type
- **the definition** actually provides the implementation of the function

To avoid massive compilation time it is common practice to declare the functions in a separate file (usually `.h`) and define them in another file (usually `.c`).
This allows other files to include the function declaration while not being aware of the definition.

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





## Variadic functions and macros

## Memory alignment

Memory alignment is a powerful tool to reach an immediate performance gain without changing your code.

Alignment will ensure your memory starts on a on address multiple of `A`, where A can be chosen to fit best on your architecture.

A simple way to check alignment is to used the `void*` conversion

```c

```

-----------------------------------------
[home](../index.md)