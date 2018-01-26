## CS 5201: Object-Oriented Numerical Modeling I

***
#### 17 January: Introduction
****

- Instructor: [Clayton Price](https://sites.google.com/a/mst.edu/price/courses/cs-5201)
- Grader: [Nathan Jarus](http://web.mst.edu/~nmjxv3/cs5201/)
- Use the calendar tool on Nathan's website to schedule appointments
- Use Doxygen tool to format comments
- Use C++17 for assignments

***
#### 19 January: Make and Makefiles
****

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
    clean:
        --
        --
    ```
6. Command lines are echoed to the standard out.
7. `-@` will suppress the echo.

***
#### 22 January: Macros
***

I sort of think of macros like I do constants.

**Rules**
1. Use `=` to build a macro.
2. Don't use tabs or `:`. Note that `#` will start a comment and thus end the macro.
3. Conventially, use uppercase to name a macro.
4. To evaluate a macro, use either `${-}` or `$(-)`.
    - e.g. `${OBJECTS}` evaluates to `moe.o larry.o curly.o`
    - For a simple character macro, the `{}` or `()` are not required.
5. You can use macros in macro definitions.
6. `BOB = ` will assign the empty string to `BOB`.
7. Order of definitions does not matter. However, if your macro definitions recurse, then `make` will get *upset*.

**Internal (Predefined) Macros**
- `${cc}` - C compiler
- `$@` - current target
- `$?` - list of prerequisites newer than the current target
- `$<` - evaluates to the prerequisite that triggered that particular rule

Macros can be used on the command line. For example, `...$ make FLAGS=hello`.

**Macro Assignment Priority**
1. Default definitions of `make` (least important)
2. Current shell environment variables
    Note: `make -c` will force #2 to dominate all but command line    
3. Description file definitions
4. Command line definitions (most important)

**Macro String Substitutions**

ex: `SOURCES = moe.cpp larry.cpp` then `ls ${SOURCES: .cpp = .o}` will output `moe.o larry.o`

**Suffix Rules**

A suffix rule is a predefined, generalized makefile entry.

```make
.SUFFIXES: .cpp
.cpp.o: $<
    ${CXX} -c ${FLAGS} $< -o $@
```

How does make work? In unix, we have conventions. A `.c` extension signals a C-compatible file. Make knows these conventions; they form the basics of a set of standard rules on how to build files.

So, suffix rules are either built-in or user-defined rules on how to *do something*. Backward chaining is employed to build any prerequisite of the current target(s), and the rebuild is determined by user-defined and automatic, internal suffix rules.
- First looks for a specific user-defined rule
- Finding none, resort to internal rules
- Then, make looks for any file in the current directory that
    - has the same root name,
    - has a significant suffix,
    - and is required to build the target.

`.SUFFIXES: .cpp` tells make which suffixes are significant.

***
#### 24 January: Modeling
***

A model is a simplification of a reality.

**Why do we model?**
- to be able to visualize the entirety of the system
    - to consider the architecture
    - to see what the system is capable of
    - to save costs
    - to see where the real system is going
- to manage risk
- to document the system
    - "blueprints"
- to provide insight into unseen problems
- to provide different "views" of the system
- "divide and conquer" allows you to break up a *big* project into smaller problems to solve

**The goal of modeling Object-Oriented Programming (OOP)**
- reusability
- maintainability
- modifiability
- adaptability
- extendability

**UML: Unified Modeling Language (Basics)**

- Things, relationships (how things relate), and diagrams (groups of things)
- Things
    1. Structural (nouns in UML): the static parts of the model, both physical and/or conceptual (e.g. desk or matrix)
        - **a class - a set of objects with shared attributes and operations**
        - an interface
        - a collaboration
    2. Behavioral (verbs in UML): describe behavior over time
        - interaction
        - state machine
    3. Annotational (the explanatory part of UML)
        - a note (figure 1)
        - a constraint (figure 2)
- **Relationships**
    1. Dependencies
    2. Associations
    3. Generalizations
- **Diagrams:** any combination of structural things and relationships

**UML (Specifics)**
- Class diagram (figure 3)
- Relationships
    1. Dependency (figure 4)
        - shows that one thing affects another thing
    2. Association (figure 5)
    3. Generalization (figure 6)
- Template class (figure 7)
- Example 1 (figure 8)
- Example 2 (figure 9)
- Attributes: [vis] name [multiplicity] [:type]
    - Those in [ ] are optional
    - Vis: +, -, #
- Functions: [vis] name [parameter list] [:return type]
    - Parameter list: [dir] name [:const] [:type]
        - Dir: in (value), out (reference), inout (reference)

***
#### 26 January: Review of C++
***

**Classes**

```C++
// --- point.h
#include "line.h"

class Point
{
    private:
        float m_x;
        float m_y;

    public:
        point() {}
        point(const float x, const float y);
        float distance(const point p) const;
        float distance(const line l) const;
        float get_x() const;
        float get_y() const;
}

// --- point.cpp
#include "point.h"
#include "line.h"
#include <math.h>

// point::point() {}

point::point(const float x, const float y)
{
    m_x = x;
    m_y = y;
}

float point::distance(const point p) const
{
    return sqrt((m_x - p.m_x) * (m_x - p.m_x) + (m_y - p.m_y) * (m_y - p.m_y));
}

float point::distance(const line l) const
{
    return l.distance(*this);
}

float get_x() const {return m_x;}

void set_x(const float x) {m_x = x; return;}

// --- line.h
class line
{
    public:
        line(const point lp = 0, const point rp = 0);
        line(const float direction, const float x, const float y);
        point intersection(const line l) const;
        float distance(const point p) const;
        static float parallel_tolerance;
    
    private:
        float m_a, m_b, m_c;
}

// --- line.cpp
#include "line.h"
#include "point.h"
#include <stdlib.h>
#include <math.h>

float line::parallel_tolerance = 0.0001;

float line::distance(const point p) const
{
    return abs(m_a * p.get_x() + m_b * p.get_y() + m_c) / sqrt(m_a * m_a + m_b * m_b);
}
```