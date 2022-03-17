# Make
{:.no_toc}

The `make` program (several implementation possible, usually the [GNU](https://www.gnu.org/software/make/manual/make.html) is installed) is a lightweight and portable tool to automate compilation tasks.

## Content
{:.no_toc}

* TOC
{:toc}

## Rules 101

### Vocabulary

`make` rules are built around time-stamps and file existence.
A rule is triggered if the file targeted does not exists or its timestamp is too old.

Even though targets are not always files (see later), we first assume this is the case

```make
target: prerequisites
    recipe-1
    recipe-2
    recipe-3
```

- **target**: is the name of the file that will be constructed during the execution of the rule
- **prerequisites**: other targets that need to be called beforehand
- **recipes**: the way to build the target. Recipes lines start with a `tab` and are executed (in parallel) as independent process.



### Variable

`make` supports the definition of variables as well as the interaction with environment variables. Unless enforced, the variables work in a _verbatim_ way, meaning that they are not evaluated at creation but at use (the same way a macro would work).

```make
# you can shorten long expressions
VAR1 = /path/to/your/file

# this will replace everywhere VAR2 with ${HOME} and evaluate it at use
VAR2 = ${HOME}

# you can force the evaluation of the variable at creation
VAR3 := ${HOME}

# sets a default value to the variable if not defined
VAR4 ?= ${HOME}
```

### `.PHONY` targets

`make` provides a few special targets (see bellow for more details) that help to control the behavior of dependencies.
Among them the `.PHONY` target
```make
.PHONY: target
```
instructs to `make` that the target is not associated to a file. The consequence is that everytime the target is called (through `make target` or as prerequisites) the rule is executed.


### Best practice

It is quite common (not mandatory) to have a few phony targets defined in your makefile:

- `make clean` should cleanup the current build
- `make reallyclean` should really cleanup the current build, including every file that might stay during the `clean` rule
- `make info` displays some information about your program



## Advanced features

### multi(-lines) recipes

There is two ways to handle multi(-line) recipes:

1. add lines: this will lead to the (maybe parallel and unordered) execution of the recipes. You should also pay attention that every recipe will be executed starting from the location where `make` has been called.

2. use the `\` and/or `&&` symbols to break lines and introduce dependencies between recipes

Some examples:
```make
# this will work as the recipes are executed both from the same 
my_dir:
    cd /path/to/my_dir
    mkdir -p my_dir

# This will work
my_dir:
    cd /path/to/my_dir && mkdir -p my_dir

# equivalently
my_dir:
    cd /path/to/my_dir && \
    mkdir -p my_dir
```

### removing time-stamps

You can remove the time-stamp constrain on the prerequisites and only check for existence using `|`:
```make
target: file_1 file_2 | file_3
    touch target
```

Here an up-to-date timestamp for `file_1` and `file_2` is requested while only the existence of `file3` is checked.
See [here](https://www.gnu.org/software/make/manual/html_node/Prerequisite-Types.html) for more information.


### Automatic variables

It is very convenient to generalize the recipes using the [automatic variables](https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html) defined automatically by make.

Some are particularly famous:

- `$@` contains the target name,
- `$<` the name of the first prerequisite only,
- `$^` the name of all the prerequisites,
- `$?` the name of all the prerequisites that are newer than the target,
- `$*` the name of the matched pattern.

### Match patterns

To automate even further the rules, you can use pattern matching and automatic variables (see bellow).
To compile all your `.o` files you can for example use the following rule:

```make
%.o: %.c
    CXX 
```




### Special targets

[Special targets](https://www.gnu.org/software/make/manual/html_node/Special-Targets.html) can be used to further fine-tune the behavior of `make`


### Functions

`make` also comes with convenient functions:

- `wildcard`
- `foreach`


-----------------------------------------
[home](../index.md) - [compilation](compilation.md)


