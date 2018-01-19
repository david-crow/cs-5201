## CS 5201 - Object-Oriented Numerical Modeling I

****
#### 17 January: Introduction
- Instructor: [Clayton Price](https://sites.google.com/a/mst.edu/price/courses/cs-5201)
- Grader: [Nathan Jarus](http://web.mst.edu/~nmjxv3/cs5201/)
- Use the calendar tool on Nathan's website to schedule appointments
- Use Doxygen tool to format comments
- Use C++17 for assignments

#### 19 January: Make and Makefiles
make is a unix command. It's part of a utility designed to lead to easier building of programs. The goal is to simplify the process. Many times, the creation of an executable is a very quick, simple, and easy compile command. With just a few files, that suffices. Other times, though, projects have more than just a few files. Also, you may change just one file, so there's no need to rebuild all the files - only those that have changed or that are dependencies of those that have changed. Make can do this. It uses time stamps and it knows the dependencies.

**The basics**
- makefile or Makefile: a file you create that is executed by the command 'make'
- make: the command
- target: the executable that is to be built
- dependencies: files used to build the target
- motivation: expedite the build to save time
- a makefile is also known as a description file.

**Basic example makefile**
```make
# this is the makefile for the stooge project
stooge : moe.o larry.o curly.o shemp.h
    g++ moe.o larry.o curly.o -o stooge
moe.o : moe.cpp larry.cpp curly.cpp shemp.h
    g++ -c moe.cpp larry.cpp curly.cpp -o moe.o
curly.o : curly.cpp shemp.h
    g++ p-c curly.cpp -o curly.o
larry.o : larry.cpp shemp.h
    g++ -c larry.cpp -o larry.o
```

**Another example makefile**
```make
# makefile for stooge
cxx = /mst/bin/g++
FLAGS = -W -Wall -s -pedantic-errors
OBJECTS = moe.o \
    larry.o \
    curly.o \
.SUFFIXES : .cpp
.cpp .o : $<
    ${cxx} -c ${FLAGS} $< -o $@
default : all
all : stooge
stooge : ${OBJECTS}
    ${cxx} ${FLAGS} ${OBJECTS} -o $@
clean :
    -@rm -f core > /dev/null 2>&1
    -@rm -f stooge > /dev/null 2>&1
```

**Basic rules and details**
1. There must be a tab at the lead or beginning of a command line. You can
use the unix tool `$cat -v -t -e makefile` to put a `^I` anywhere there is a tab character and `$` at the end of every line.
2. `\` at the end of a line continues that line on the next line of the file. This assumes there
is no space between the `\` and the newline character.
3. Don't use tabs anywhere other than the beginning of a command line.
4. make ignores blank lines; `#` begins a comment
5. Targets without dependences are allowed. Make treats all non-existent targets as out-of-date targets.
    ```
    # example
    clean :
        --
        --
    ```
6. Command lines are echoed to the standard out.
7. `-@` will suppress the echo.