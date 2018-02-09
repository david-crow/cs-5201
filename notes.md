## CS 5201: Object-Oriented Numerical Modeling

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
        Point() {}
        Point(const float x, const float y);
        float distance(const Point p) const;
        float distance(const Line l) const;
        float get_x() const;
        float get_y() const;
};

// --- point.cpp
#include "point.h"
#include "line.h"
#include <math.h>

// Point::Point() {}

Point::Point(const float x, const float y)
{
    m_x = x;
    m_y = y;
}

float Point::distance(const Point p) const
{
    return sqrt((m_x - p.m_x) * (m_x - p.m_x) + (m_y - p.m_y) * (m_y - p.m_y));
}

float Point::distance(const Line l) const
{
    return l.distance(*this);
}

float get_x() const {return m_x;}

void set_x(const float x) {m_x = x; return;}

// --- line.h
class Line
{
    public:
        Line(const Point lp = 0, const Point rp = 0);
        Line(const float direction, const float x, const float y);
        Point intersection(const Line l) const;w
        float distance(const Point p) const;
        static float parallel_tolerance;
    
    private:
        float m_a, m_b, m_c;
};

// --- line.cpp
#include "line.h"
#include "point.h"
#include <stdlib.h>
#include <math.h>

float Line::parallel_tolerance = 0.0001;

float Line::distance(const Point p) const
{
    return abs(m_a * p.get_x() + m_b * p.get_y() + m_c) / sqrt(m_a * m_a + m_b * m_b);
}
```

***
#### 29 January: An Array Class
***

**The Array Class**

Requirements for our array class
1. Runtime sizing - using the constructor
2. Use of the `[ , ]` operator for indexing into the array
3. Automatic memory management
4. Array copying facility
5. Being able to assign a single value to every element of the array
6. Resizing of my array

```C++
// array.h
#ifndef ARRAY_H
#define ARRAY_H

<template class T>
class Array // array of T
{
public:
    Array();
    Array(const int size);
    Array(const Array<T>& source);
    ~Array();

    T& operator[](const int i);
    Array<T>& operator=(const Array<T>& source);
    Array<T>& operator=(const T s);

    int get_size() const;
    void set_size(const int size);

private:
    int m_size;
    T * ptr_to_data;

    void array_copy(const Array<T>& source);
};

#include "array.hpp"
#endif

// array.hpp
<template class T>
Array<T>::Array()
{
    m_size = 0;
    ptr_to_data = NULL;
}

<template class T>
Array<T>::Array(const int size)
{
    m_size = size;
    ptr_to_data = new T[size];
}

<template class T>
Array<T>::Array(const Array<T>& source)
{
    m_size = source.m_size;
    ptr_to_data = new T[m_size];
    array_copy(source);
}

<template class T>
void Array<T>::array_copy(const Array<T>& source)
{
    T * p = ptr_to_data + m_size;
    T * q = source.ptr_to_data + source.m_size;

    while (p > ptr_to_data)
        *--p = *--q;

    /* equivalently,
    for (int i = 0; i < m_size; i++)
        ptr_to_data[i] = source.ptr_to_data[i];
    */

    return;
}

<template class T>
Array<T>::~Array() {delete [] ptr_to_data;}

<template class T>
T& Array<T>::operator[](const int i)
{
    return ptr_to_data[i];
}

<template class T>
Array<T>& Array<T>::operator=(const Array<T>& source)
{
    if (ptr_to_data != source.ptr_to_data)
    {
        set_size(source.m_size);
        array_copy(source);
    }

    return *this;
}

<template class T>
Array<T>& Array<T>::operator=(const T s)
{
    for (int i = 0; i < m_size; i++)
        ptr_to_data[i] = s;

    return *this;
}

<template class T>
void Array<T>::set_size(const int size)
{
    if (m_size != size)
    {
        delete [] ptr_to_data;
        m_size = size;
        ptr_to_data = new T[size];
    }

    return;
}
```

***
#### 31 January: More on Templates
***

**Templating Functions**

```C++
template <typename T>
void swap(T& t1, T& t2)
{
    T temp = t1;
    t1 = t2;
    t2 = temp;
    return;
}

template <typename T>
int find(const T arr[], const int size, const T& target)
{
    bool found = false;
    int i = 0;

    while (!found && i < size>)
    {
        if (arr[i] == target)
            found = true;
        else
            i++;
    }

    return (found ? i : -1);
}
```

Most importantly, your pre-conditions need to recognize the operators used in the code (and whether or not they're available for the templated type).

**Extern Templates**

```C++
// header.h
template <class T>
void BIGFUNC()
{
    // ...
}

// source1.cpp
#include "header.h"
void func1()
{
    BIGFUNC<int>();
    // ...
}

// source2.cpp
#include "header.h"
void func2()
{
    BIGFUNC<int>();
    // ...
}
```

The result of this is that the compiler creates two copies of `BIGFUNC` with the template instantiated with int in the .o files. It has to discard one. To prevent this,

```C++
// source2.cpp
extern template BIGFUNC<int>();
void func2()
{
    // ...
}
```

And this prevents the generation of multiple copies of the function.

**`auto`**

`auto` allows your code to deduce a type when allocating memory. It's a signal to the compiler to infer a type based on an initial value.

```C++
float x = 1.2;
auto y = 5.6; // double
auto z = x; // double
```

But this isn't really useful and *not* what `auto` is good for. If there is not initial value, it won't work. `auto` is useful for using with templates and iterators.

example 1:

```C++
vector<int> vec;
auto itr = vec.iterator(); // instead of vector<int>::iterator itr = vec.iterator()
```

example 2:

```C++
template<class BuiltType, class Builder>
void buildSomething(Builder& builder)
{
    BuiltType obj = builder.makeObj();
    // ...
    return;
}
```

Not only do we have two template parameters, but the type of the built object can't be inferred by the parameter.

```C++
MyObjBuilder builder;
buildSomething<AnObj> builder;
```

But this can be simplified using `auto`. Let C++ determine the type of the built object.

```C++
template<class Builder>
void makeSomething(BUilder& builder)
{
    auto val = builder.makeObj();
    // ...
    return;
}
```

So, you only need *one* template parameter, and the type is going to be inferred by C++.

```C++
MyObjBuilder builder;
makeSomething(builder);
```

**`decltype`**

Let's suppose in the example above that you want to *return* the object built by the function. Will `auto` help? Yes and no.

There is a new return type syntax.

```C++
int add(int x, int y);
auto add(int x, int y) -> int; // the new way!
```

So, this example is far too simple. Try this:

```C++
class Student
{
private:
    age m_age;
public:
    enum age{OLD, YOUNG};
    age getAge();
};
```

The implementation of `getAge()` is the problem.

```C++
student::age student::getAge()
{
    return m_age;
}
```

With `auto`, we can do the following:

```C++
auto student::getAge()
{
    return m_age;
}
```

How does this help with the foregoing example? We need something new. While `auto` allows the declaration of a particular type, `decltype` allows you to *extract* a type. 

```C++
int x = 3;
decltype(x) y = x;
```

So, let's apply this to the example.

```C++
template<class Builder>
auto makeSomething(Builder& builder) -> decltype(builder.makeObj())
{
    auto val = builder.makeObj();
    // ...
    return val;
}
```

***
#### 2 February: Exception Handling
***

What happens when an object is thrown? Well, two things are thrown:

1. an object
2. control

```C++
class RangeErr
{
public:
    RangeErr(int i);
    int BadSubscript();
private:
    int subscr;
};

class SizeErr
{
public:
    SizeErr(int i);
    int BadSubscript();
private:
    int subscr;
};
```

We would want to modify three functions in the `Array` class.

```C++
template <class T>
Array<T>::Array(int n)
{
    if (n < 0) throw SizeErr(n);
    m_size = n;
    ptr_to_data = new T[n];
}

template <class T>
T& Array<T>::operator[](int i)
{
    if (i < 0 || i >= m_size) throw RangeErr;
    return ptr_to_data[i];
}

template <class T>
void Array<T>::setSize(int n)
{
    if (n != m_size)
        if (n < 0) throw SizeErr(n);
    // ...
}

// later
try
{
    int n;
    cin >> n;
    Array<float> array(n);
    // ...
    int j;
    cin >> j;
    float f = array[j];
}

catch (RangeErr e)
{
    cerr << "Subscript out of range: " << e.BadSubscript();
}

catch (SizeErr e)
{
    cout << "Size of declared array is negative: " << e.BadSubscript();
}
```

***
#### 5 February: Function Overloading, Templates, and Member Functions
***

```C++
float index_of_min(const Array<float>& a)
{
    // ...
    return index_of_min_element;
}

float& min_element(Array<float>& a)
{
    // ...
    return a[index_of_min(a)];
}
```

This return by reference, though legal and preferred in some cases, can lead to heartbreak. This is because that reference creates a backdoor into the class. Consider returning a non-referenced value (like a `const`).

**Function Overloading and Templates**

You overload a function when you want to have multiple functions with the same name but different parameter lists. This is different from templating where you want the exact same functionality but for different types.

**Overloading Functions and How C++ Resolves Function Calls**

There are levels of consideration that C++ will go through to know how to resolve a function call.

1. Consider an exact match with only the possibility of trivial conversions
    - Trivial: `T` to/from `T&`
    - Trivial: `T` to/from `T*`
    - `void f(T&)` and `void f(T)` can't coexist.
    - Note that `void f(T)` and `void f(const T)` can coexist.
2. Match with promotion
    - From `char`, `enum`, or `short` to `int` or `long`
        - Example: `void bob(float)` and `void bob(int)` can coexist
3. Standard conversions
    - `int`-types to `float`-types
4. User-defined conversions
    - Be sure that your code only defines one way to convert one type to another.
        ```C++
        void bob(int, float);
        void bob(int, double);
        // bob(1, 2); // C++ throws an error for ambiguous function call
        ```

**Templates**

Suppose we have the following:

```C++
template <class T>
void bob(T a, T& b);

// members of the class include:
void bob(int a, const int& b);
void bob(char a, const char& b);

// but don't include
// void bob(int a, const float& b);

int a;
float b;

bob(1, a); // matches the template with T = int
bob(1.0, b); // matches the template with T = float
// bob(1, b); // no match

void bob(float a, const float& b);
bob(1, b); // match with promotion

```

The steps C++ follows to resolve function calls:

1. Match without the possibility of any conversion
2. Template match with the possibility of trivial conversion
3. Function delcaration using the ordinary matching process

```C++
template <class T>
int compare(const T& t1, const T& t2)
{
    if (t1 < t2) return -1;
    if (t1 > t2) return 1;
    return 0;
}

template <> // specialization
int compare<const char*>(const char* const& s1, const char* const& s2)
{
    return strcmp(s1, s2);
}
```

**Member Functions**

One difference between member and non-member functions is that member functions can be `const` functions. A `const` function is a member function that can *not* alter the calling object. If you want to overload `operator[]`, make two versions: a `const` version and a non-`const` version.

**Special People**

Constructors
- No return type
- No return statement
- Named name of class
- C++ will provide a default constructor, but it's suppressed when any custom constructor is created.

***
#### 7 February: Constructors and Operators
***

We can define constructors in a .cpp, inline, or with an initialization list.

```C++
class point
{
public:
    point(float x, float y) : m_x(x), m_y(y) {}

private:
    float m_x;
    float m_y;
};

// --- point.cpp

/* point::point(float x, float y)
{
    m_x = x;
    m_y = y;
} */
```

You initialize if:
1. you have a no default constructor
2. you have a member variable that is a constant
3. you have a member variable that is a constant reference

You don't have to initialize if:
1. members are C++ primitives
2. you have a user defined default constructor
3. you have no user defined other constructor

**Copy Constructors**

- `A::A(const A& rhs);`
- Copy constructors are called:
    - explictly
    - pass-by-value parameters in functions
    - return-by-value from functions
    - when an exception is thrown
- C++ provides an automatic copy constructor, but it will copy member-by-member even if they are pointers. This leads to shallow copies.
- Remember: if your class contains a pointer (referential aggregation), then you should write your own:
    - copy constructor
    - copy assignment
    - destructor

**Operators (AKA Functions for Special Symbols)**

- You can't change the functionality of an operator for primitives.
- You can't change the arity of an operator.
- You can't change the order of precedences.
- You can't overload `::`, `:`, `.*`, `sizeof`, or `?:`.

**General Rules**

- Make your operators make sense
- Define pairs when appropriate

```C++
// complex numbers
complex& complex::operator+=(const complex& rhs)
{
    m_real += rhs.m_real;
    m_imaginary += rhs.m_imaginary;

    return *this;
}

complex operator+(const operator& rhs, const complex& rhs)
{
    complex result(lhs);
    return result += rhs;
}

complex w, z;
w += z;
w += z += w;
```

`operator[]`, `operator()`, and `operator=` have to be implemented as member functions.

***
#### 9 February: Function Wrappers, Friend Functions, Static Functions, and Conversions
***

`operator()` - use as a function or as a function class. Effectively, you wrap up a function in a class.

```C++
class bell_curve
{
private:
    float m_h;
    float m_w;

public:
    bell_curve(const float h = 1, const float w = 1): m_h(h), m_w(w) {}
    float operator()(float x) { return m_h / (m_w + x * x); }
};

// later...
bell_curve y(1, 1);
// ...
cout << y(2) << endl; // 0.2
```

**Friend Functions**

A function is a friend of a class means that it is a non-member and that it has been given direct access rights to the private section of that class.
    - Don't trust your friends.
    - Avoid friends.

Note: the only place that `friend` should ever appear is inside a class.

The functions that (almost) everyone makes a non-member `friend` are the streaming operators.

**Static Functions**

```C++
class complex
{
public:
    complex(floar re, float im): m_re(re), m_im(im) {}
    static complex file_read(istream& in = cin);
};

complex complex::file_read(istream& in = cin)
{
    float r, i;
    in >> r >> i;
    return complex(r, i);
}

complex z = complex::file_read(input); // where input is a stream
// z.file_read(...) does not work because it's not a member function 

struct stooge
{
    static void moe();
    static void larry();
    static void curly();
};

stooge::curly();
```

**Conversions**

If your class has a constructor that takes a single variable of some type and builds an object (of course), then it can be thought of as a *constructor for automatic conversion*.

```C++
complex(float r = 1, float i = 0): m_r(r), m_i(i) {}

// non-member function
complex operator+(const complex& lhs, const complex& rhs);

// later...
z = u + r;
z = u + 4; // 4 gets sent to the constructor and becomes a complex because we gave the constructor default values
// this also wouldn't work if operator+ were a member function
```

There are functions for automatic conversions. These are called *conversion functions*.

```C++
class complex
{
private:
    float m_r, m_i;

public:
    // ...
    operator bool() { return m_r || m_i; }
};

complex z;

if (z)
    // do stuff
```
