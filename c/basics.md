# C - basics

{:toc}

## main function

every code must contain the main function
```c
int main(int argc, char**argv){

    return 0;
}
```

if the code returns `0`, it means success while another value means that an error occured.

- the `argc` and `argv` arguments are used to capture the command line. For example calling `./exe --option1 --flag2` will lead to `argc = 3` and `argv = {"./exe", "--option1", "--flag2"}`.


## elementary types
you have a few elementary types, among others:

- `int` an integer
- `float` single precision floating point number
- `double` double precision floating point number
- `char` a character

### macros

Macros are directly replaced (verbatim) in the code.
Some are already defined such as `M_PI` which is the value of pi.

You can also define your own macros (you should be careful though, it can be very bug-prone):

```c
#define SIZE 89

#define m_sign(a)                                                \
    ({                                                           \
        double m_sign_a_    = (a);                               \
        double m_sign_zero_ = 0;                                 \
        (m_sign_zero_ < m_sign_a_) - (m_sign_a_ < m_sign_zero_); \
    })
```

### structures - user defined types

There is also the possibility to define custom datatypes:

```c
typedef struct{
    int a;
    double b;
} my_struct;
```

Will lead to structure:

```c
my_struct s;
s.a = 8;
s.b = M_PI;
```

### function definition

a function has a retu
