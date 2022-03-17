# Make
{:.no_toc}

The `make` program (several implementation possible, usually the [GNU](https://www.gnu.org/software/make/manual/make.html) is installed) is a lightweight and portable tool to automate compilation tasks.

## Content
{:.no_toc}

* TOC
{:toc}

## Rules

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

### How-to

#### multi-line recipes


## Variable

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

## Functions

`make` also comes with convenient functions:

- `wildcard`
- `foreach`


