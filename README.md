## CS 5201: Object-Oriented Numerical Modeling (2018)

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
2. Use of the `[]` operator for indexing into the array
3. Automatic memory management
4. Array copying facility
5. Being able to assign a single value to every element of the array
6. Resizing of the array

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
    ptr_to_data = nullptr;
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

But this isn't really useful and *not* what `auto` is good for. If there is no initial value, it won't work. `auto` is useful for using with templates and iterators.

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
void makeSomething(Builder& builder)
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
3. Function declaration using the ordinary matching process

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
    point(float x, float y): m_x(x), m_y(y) {}

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
1. you have no default constructor
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

***
#### 12 February: Object Lifetime
***

**The Object Lifecycle**

1. Allocation - memory is allocated by the operating system as triggered by your code
2. Pre-initialization - overhead of making the memory a C++ object, naming it, connecting it to your program
3. Initialization - constructors are run
4. Use - its use in your program
5. Cleanup - reverse of initialization
6. Post-cleanup and deallocation - memory is restored to the free store

There are three types of objects in C++:

1. Automatic - allocated when the code requests that allocation and destroyed when out of scope
2. Static variables - allocated if code runs and they persist until the end of the program
3. Dynamic - allocated and destroyed depending on how the program runs

```C++
class Trace
{
public:
    Trace(const char* name); // create and print messages
    ~Trace();

private:
    enum {max = 100}; // max number of objects to create
    static char existing_objects[max + 1]; // L - live; D - dead
    static int total_created;
    int instance;
    char var_name[21];
};

int Trace::total_created = 0;
char Trace::existing_objects[max + 1] = "";

Trace::Trace(const char* name):instance(total_created++)
{
    strncpy(var_name, name, 20);
    var_name[20] = '\0';

    if (total_created <= max)
    {
        existing_objects[total_created - 1] = 'L';
        existing_objects[total_created] = '\0';
        cout << existing_objects << " trace created for " << var_name << endl;
    }
}

Trace::~Trace()
{
    if (instance < max)
    {
        existing_objects[instance] = 'D';
        cout << existing_objects << " trace destroyed for " << var_name << endl;
    }
}
```

**Static Variables**

Static variables can give state to a function. Static variables persist to the end of the program, which destroys them in the reverse order in which they were created.

Local static variables are created first.

***
#### 14 February: Dynamic Memory and Interpolating Polynomials
***

- Objects are allocated by a call to `new` and deallocated by a call to `delete`.
- Objects are created as the program needs them.
- So, there needs be no relationship between dynamic memory and variables - known at compile time.
- `new` will invoke the appropriate constructor
- New arrays of objects require a default constructor
- Upside of dynamic memory: flexibility
- Downside of dynamic memory: memory management

Construction: `int * p = new int; // new takes a type as an argument`
Or: `int * p = new int[size]; // dynamic array`

Destruction: always match a call to `new` with an appropriate call to `delete`; match them one-to-one

Bad deletions lead to *big* problems.
1. Dangling pointers (shallow copies): copying a pointer instead of what it points to - when you `delete` both, you have a *double delete*
    - Fix: write your own copy constructor and assignment operator

    ```C++
    class bad
    {
    public:
        bad() { p = new int(0); }
        ~bad(){ delete p; }
        int * p;
    };

    void f()
    {
        bad a;
        bad b = a;
        // ...
    }

    // later
    class good
    {
    public:
        good() { p = new int(0); }
        ~good() { delete p; }
        good(const good& g): p(new int(*s.p)) {}
        good& operator=(const good& g)
        {
            if (g.p != p)
            {
                delete p;
                p = new int(*g.p);
            }

            return *this;
        }

        int * p;
    }
    ```

2. Dangling pointers: set a pointer to an automatic object; when the object goes out of scope, the pointer points to nothing.
    - Fix: don't point to automatic objects.

3. Deleting a function parameter: suppose you pass a pointer to a function and `delete` it there. In the calling function, the pointer is useless.
    - Fix: don't do this - don't `delete` function parameters

4. Garbage: not deleting an object; the memory is lost
    - Fix: put pointers into class-type-sorta-thingies called smart pointers

**Interpolating Polynomials**

Fit a curve to the existing data to be able to explain it in some way. Additionally, this allows you to identify values at points not reflected in the data itself.

If you trust the data (i.e. you want your curve to exactly hit each point), use an interpolating polynomial. Polynomials are nice because you can easily calculate values.

There are two forms of creating an interpolating polynomial:
- Lagrange form
- Newton form

**Newton Form**

`P_N(x) = a0 + a1(x-x0) + a2(x-x1)(x-x0) + ... + a_N(x - xN)*...*(x-x0)` where `ak = f[x0, x1, x2, ..., xk], k = 0, 1, ..., N`

```
f[]:
    f[xk] = f(xk) = yk
    f[xk-1, xk] = (f[xk] - f[xk-1]) / (xk - xk-1)
    f[xk-2, xk-1, xk] = (f[xk-1, xk] - f[xk-2, xk-1]) / (xk - xk-2)
    f[xk-j, xk-j+1, ..., xk] = (f[xk-j+1, xk] - f[xk-j, xk-1]) / (xk - xk-j)
```

The table:

| xk | yk = f[xk] | f[ , ] | f[ , , ] | f [ , , , ] |
| - | - | - | - | - |
| x0 | y0 | - | - | - |
| x1 | y1 | f[x0, x1] | - | - |
| x2 | y2 | f[x1, x2] | f[x0, x1, x2] | - |
| x3 | y3 | f[x2, x3] | f[x1, x2, x3] | f[x0, x1, x2, x3] |

***
#### 16 February: Move
***

One of the most important goals in programming is to make it fast. There are many forces in our world working against that effort. One *big* one is the generation of objects - in particular, temporary objects that just end up discarded anyway.

```C++
class Array;

Array squares(const Array& a)
{
    int n = a.get_size();
    Array square_array(n);
    for (int i = 0; i < n; i++)
        square_array[i] = a[i] * a[i];
    return square_array;
}

int main()
{
    // ...
    Array stuff(10000);
    stuff = squares(stuff);
}
```

What's the problem? Copies! There are too many copies of arrays. Can we avoid all of the copying? Yes! The temporary objects just get discarded. In the past (pre-C++11), we had no way to identify temporary objects. We do now.

**rvalues vs lvalues**

An lvalue (formerly left-hand value) is an expression with a locatable memory... a "locator value." It has some *permanent* piece of memory - a name.

```C++
short x; // x is an lvalue
x = 10;
```

```C++
int x;
int& get_x() { return x; }
get_x() = 10;
```

So, an lvalue need not be a variable.

An rvalue is *not* an lvalue. An expression that is a temporary object.

```C++
int x;
int get_x() { return x; }
get_x(); // this is a temporary object
x = get_x(); // assigning from a temporary object
```

Can we identify temporary objects? In pre-C++11, no, we couldn't. An *rvalue reference* is a reference that will only bind to a temporary object.

```C++
// before C++11
const int& z = get_x(); // okay
// int& z = get_x(); // not okay
```

You can't have a mutable reference to a temporary object.

C++11 introduces the rvalue reference that will bind to an rvalue and *not* an lvalue.

```C++
// with C++11 and beyond
const int&& z = get_x(); // okay
int&& z = get_x(); // also okay
```

So, an rvalue reference can detect a temporary object.
Consider the following two functions:

```C++
void printref(const string& s) { cout << s; } // function 1
void printref(string&& s) { cout << s; } // function 2
```

Function 1 will accept any - mutable or not - lvalue or rvalue. However, if Function 2 is present, then Function 1 will accept all *but* mutable rvalue references.

**Move Constructors**

Like a copy constructor, a move constructor builds a new object. Rather than create new memory, though, it will *move* the object. It can because it knows the argument is a temporary object - an rvalue reference. If the argument is a primitive, move is just a copy. However, if the object to copy is/has a pointer, it will just steal the pointer and then nullify the pointer to the old object. This works because the old object is about to go away.

```C++
// for array
class Array
{
public:
    // ...
    Array(Array&& other): ptr_to_data(other.ptr_to_data), size(other.size)
    {
        other.ptr_to_data = nullptr;
        other.size = 0;
    }
};
```

We set the `other`'s pointer to `null` because it's a temporary object about to be released from The Old ArrayFolks Home.

Now, suppose that the class you are writing the move constructor for contains an object. How will it be handled?

Suppose we have the following:

```C++
class data
{
    int size;
    string name;

public:
    data(const int s, const string& n): size(s), name(n) {}
    data(const data& other): size(other.size), name(other.name) {}
    data(data&& other): size(other.size), name(other.name) {}
};

class Array
{
    float* ptr_to_data;
    data size_n_name;

public:
    // ...
}
```

Will this work? Does it work for `Array`'s move constructor to call `data`'s move constructor? Nope.

We must `#include <utility>` for `std::move`.

```C++
Array(Array&& other): ptr_to_data(other.ptr_to_data), data(std::move(other.data))
{
    other.ptr_to_data = nullptr;
}
```

We have to do the same thing for `data`'s move constructor. The reason: the `other` in `Array`'s move constructor is an rvalue reference. However, an rvalue reference is not an rvalue; it's an lvalue. This means we have to change it. The `std::move` casts that lvalue to an rvalue reference.

***
#### 19 February: The Copy/Swap Maneuver
***

...for the overload of the assignment operator.

In our `Array` class, we had an overload of the assignment operator that was as follows:

```C++
Array& Array::operator=(const Array& s)
{
    if (ptr_to_data != s.ptr_to_data)
    {
        set_size(s.m_size);
        for (int i = 0; i < m_size; i++)
            ptr_to_data[i] = s.ptr_to_data[i];
    }

    return *this;
}
```

Can we do better? Problems with that code:

1. To make allowance for self-assignment (how often does this really happen?)
2. Time spent walking down the array (10 elements vs 10 billion elements)

Answer:

```C++
Answer& Array::operator=(Array s)
{
    swap(*this, s);
    return *this;
}
```

Most important thing is that you understand the swap used here. You need to write your own swap function.

```C++
class Array
{
public:
    // ...
    friend void swap(Array& a1, Array& a2)
    {
        std::swap(a1.m_size, a2.m_size);
        std::swap(a1.ptr_to_data, a2.ptr_to_data);
    }
}
```

We can't use the `std::swap` to swap the arrays because it uses the copy constructor and the assignment operator, that which we are trying to overload. So, in our new version of `operator=`, we use pass-by-value, letting the compiler make the copy for us (using our copy constructor). If it fails, then `*this` is not affected. Additionally, when the copy is made, we just steal the pointer, as we did with a move constructor. The passed object goes out of scope and we have the goods.

***
#### 21 February: Smart Pointers
***

We all know that pointers are necessary to programming in C++, but the built-in pointers are too weak and too strong: they allow the programmer too much power with too little oversight.

**Aggregation**

...is the combining of different objects into another object, and when your class *contains* a pointer, you have *referential* aggregation. The problem is that the life of the program - and what it points to - don't necessarily coincide. So, when objects get copied (especially by built-in copy constructors), then the possibility of a shallow copy exists. Leaks can be caused in many ways, including ways you hadn't thought of... yet. Additionally, they can happen when you think you have all of your bases covered - a user-defined copy constructor and assignment operator. Programmer-defined objects that wrap up pointers are called *smart pointers*, and they're designed to do what they're supposed to do and clean up after themselves. We write smart pointers to preserve a one-to-one relationship between the pointer and what it points to.

```C++
template <class T>
class CBPtr // copied built-in pointer
{
protected:
    T* the_p;

public:
    CBPtr(): the_p(nullptr) {} // default pointer
    CBPtr(T* just_newed): the_p(just_newed) {} // from a new pointer
    ~CBPtr() { delete the_p; }
    CBPtr(const CBPtr<T>& aCP): the_p(aCP.is_null() ? nullptr : new T(*aCP.the_p)) {}

    CBPtr<T>& operator=(T* p)
    {
        delete the_p;
        the_p = p;
        return *this;
    }

    CBPtr<T>& operator=(const CBPtr<T>& rhs)
    {
        if (the_p != rhs.the_p)
        {
            delete the_p;
            the_p = rhs.is_null() ? nullptr : new T(*rhs.the_p);
        }

        return *this;
    }

    T& operator*() const { return *the_p; }
    bool is_null() const { return the_p == 0; }

    T* release_control()
    {
        T* save_p = the_p;
        the_p = nullptr;
        return save_p;
    }

    friend bool operator==(const CBPtr<T>& lhs, const CBPtr<T>& rhs)
    {
        return lhs.the_p == rhs.the_p;
    }

    friend bool operator!=(const CBPtr<T>& lhs, const CBPtr<T>& rhs)
    {
        return !(lhs == rhs);
    }
};
```

**Benefits**

1. One-to-one relationship between pointer and heap object
2. Automatic copy is made when C++ auto generated copy constructor is called since the copy constructor for `CBPtr` is called
3. `delete` the pointer `delete`s the heap object → no garbage
4. Has an `is_null()` function
5. Has comparison operators

```C++
template <class T>
class COPtr : public CBPtr<T> // copy object pointer
{
public:
    COPtr(const COPtr<T>& p): CBPtr<T>(p) {}
    COPtr(): CBPtr<T>() {}
    COPtr(T* just_newed): CBPtr<T>(just_newed) {}
    // a destructor

    COPtr<T>& operator=(T* rhs)
    {
        CBPtr<T>::operator=(rhs);
        return *this;
    }

    COPtr<T>& operator=(const COPtr<T>& rhs)
    {
        CBPtr<T>::operator=(rhs);
        return *this;
    }

    T* operator->() const { return the_p; }
};
```

**Note**

The `CBPtr` class is for built-in types. It doesn't - and should not - have an overload of `operator->`. This operator should return either a built-in pointer to a class object or an object of a pointer-like class with an overload of `operator->`. Built-in pointers to built-in objects cannot be returned.

```C++
class Leaker
{
public:
    Leaker(const int size): p(new float(0)), array(size) {}
    Leaker(const Leaker& rhs): p(new float(*rhs.p)) {}
    ~Leaker() { delete p; }

    Leaker& operator=(const Leaker& rhs)
    {
        p = new float(*rhs.p);
        array = rhs.array;
        return *this;
    }

private:
    float* p;
    Array<int> array;
};

void f()
{
    Leaker l(-1);
    // ...
}
```

When `f()` is called, the constructor for `Leaker` is called. The `float` is allocated and we try to construct `array` with a negative size. An exception is thrown. When an exception is thrown, all *fully-constructed* objects are destroyed -- we have a leak!

```C++
class nonLeaker
{
public:
    nonLeaker(const int size): p(new float(0)), array(size) {}

private:
    CBPtr<float> p;
    Array<int> array;
};
```

***
#### 23 February: A Review of Vector Analysis
****

**Definition:** A vector space is a set of vectors and scalars along with the operations `+` and `·` that adhere to the following:

1. A vector space `V` is closed under vector addition `+`
    1. `α + β = β + α`, for all `α`, `β` in `V`
    2. `(α + β) + γ = α + (β + γ)`, for all `α`, `β`, `γ` in `V`
    3. For all `0` in `V` (zero vector), `α + 0 = α`, for all `α` in `V`
    4. For all `α` in `V`, there exists some `β` in `V` such that `α + β = 0`
        - the negative of `α` is `β`
2. `V` is closed under scalar multiplication (if `α` in `V` and `c` is a scalar)
    1. `c · (α + β) = c · α + c · β`, for all `α`, `β` in `V`, and `c · α` in `V`
    2. `(c + d) · α = c · α + d · α`
    3. `(c * d) · α = c · (d · α)`
    4. `1 · α = α`

**Example**

1. Let `V` be the set of all pairs of real numbers (`R^2`) and let the scalars be real numbers (`R`), and let `+` be defined as the sum of two vectors whose components are the usual sum of the operands and scalar multiplication is the usual multiplication. This is a vector space.
2. `V` is the set of all continuous functions on `[0, 1]`. This is a vector space.
3. Let `V` be the set of all matrices `A_{mn}` of real entries... this is a vector space.
4. Let `V = Z^+`, scalars are reals. This is not a vector space.

**Subspaces**

A subset of vectors `v` in `V` is a subspace if that subset is closed under `+` and `·`.

**Definition:** A linear combination is any sum of constant (scalar) multiples of vectors in the vector space.

**Defintion:** Let `S = {α_1, ..., α_n}` be a subset of `V`. If any `v` in `V` is some linear combination of the vectors in `S`, then `S` is said to span `V`. `S` is a *spanning set*.

**Definition:** Let set `S = {α_1, ..., α_n}` be a subset of `V`. `S` is said to be linearly dependent if there exists `a_i`, `1 <= i <= n`, not all `0`, such that `a_1 * α_1 + a_2 * α_2 + ... + a_n * α_n = 0`.

If, given `a_1 * α_1 + a_2 * α_2 + ... + a_n * α_n = 0`, the solution is `a_1 = a_2 = ... = a_n = 0`, then `S` is said to be linearly independent.

**Definition:** If a set `S = {α_1, ..., α_n}` spans the vector space `V` and is linearly independent, it is called a basis.

In 3D space, `i-hat, j-hat, k-hat` (the unit vectors) form a basis. For example, `v = <2, 7, -3> = 2i + 7j -3k`.

**Definition:** The dimension of a vector space `V` (`dim V`) is the number of vectors in a linearly independent spanning set of `V`.

***
#### 26 January: Inner Product Spaces
***

**Definition**

An inner product on a vector space `V` is a real-valued function over `V` with the following rules:

1. `(α, α) > 0`, for all `α` in `V`
2. `(α, β) = (β, α)`, for all `α, β` in `V`
3. `(α + β, γ) = (α, γ) + (β, γ)`
4. `(cα, β) = c(α, β)`, where `c` is a scalar

**Theorem:** Cauchy-Schwarz Inequality

For any `α, β` in `V`, `(α, β)^2 <= |α|^2 * |β|^2`, where `|α| = √[(α, α)]`.

**Proof**

If `α = 0`, then `(α, β) = 0` and the inequality holds. Suppose `α != 0`, and let `r = 0` be in `R`, and we consider `0 <= (rα + β, rα + β) = r^2 (α, α) + 2r(α, β) + (β, β) = ar^2 + 2br + c`, where `a = (α, α)`, `b = (α, β)`, and `c = (β, β)`.

Well, `ar^2 + 2br + c` is a quadratic in `R` that is non-negative. This implies that `4b^2 - 4ac <= 0`, so `b^2 - ac <= 0`, so `b^2 <= ac`, so `(α, β)^2 <= |α|^2 * |β|^2`.

**Theorem:** The Triangle Inequality

For any `α` and `β` in `V`, `|α + β| <= |α| + |β|`.

**Proof**

`|α + β|^2 = (α + β, α + β) = (α, α) + (β, α) + (α, β) + (β, β) = |α|^2 + 2(α, β) + |β|^2 <= |α|^2 + 2|α| * |β| + |β|^2 = (|α| + |β|)^2`.

Thus, `|α + β| <= |α| + |β|`.

**Definition**

Let `V` be an inner product space. Then we say that `α, β` in `V` are orthogonal if `(α, β) = 0`.

**Example**

In `R^4` (`n <= 3`), the *dot product* is an inner product. The cross product is *not* an inner product.

Recall: we all agree that a basis is best when it is *orthonormal* (the basis vectors are of length `1` and all are mutually orthogonal).

Question: if we have an arbitrary basis for `V`, can we create from it an orthonormal (even orthogonal) basis? The technique is called the *Gram-Schmidt* method.

**Gram-Schmidt Method**

Suppose `T = {α_1, α_2, ..., α_n}` is a basis for a vector space. We wish to generate a set of vectors `{β_1, β_2, ..., β_n}` that is an orthogonal basis from this basis, `T`.

Let `β_1 = α_1`. Now, find `β_2` so that `β_1` is orthogonal to `β_2` and the set `{β_1, β_2}` spans the same subspace as {`α_1, α_2}` or `{β_1, α_2}`. We have `β_2 = a_1 * α_1 + a_2 * α_2 = a_1 * β_1 + a_2 * α_2`. We want `(β_1, β_2) = 0 = (β_1, a_1 * β_1 + a_2 * α_2) = a_1 * (β_1, β_1) + a_2 * (α_2, β_1)`. Let `a_2 = 1`. Solve for `a_1`.

`a_1 = - a_2 * (α_2, β_1) / (β_1, β_1)`

Thus, `β_2 = α_2 - a_2 * (α_2, β_1) / (β_1, β_1) * β_1`.

Next, we want to find `β_3` so that `{β_1, β_2, β_3}` spans the same subspace as `{α_1, α_2, α_3}` or `{β_1, β_2, α_3}` and they're all orthogonal to each other.

Let `β_3 = b_1 * β_1 + b_2 * β_2 + b_3 * β_3`.

We want `(β_1, β_3) = 0 = b_1 * (β_3, β_1) + b_2 * (β_1, β_2) + b_3 * (β_1, α_3)`

`(β_2, β_3) = 0 = b_1 * (β_2, β_1) + b_2 * (β_2, β_2) + b_3 * (β_2, α_3)`

Let `b_3 = 1`.

`b_1 = - (β_1, α_3) / (β_1, β_2)`

`b_2 = - (β_2, α_3) / (β_2, β_2)`

So, `β_3 = α_3 - (β_1, α_3) / (β_1, β_1) - β_2 * (β_2, α_3) / (β_2, β_2)`.

Thus, `b_k = α_k - ∑_{n=1, k-1} β_n * (β_n, α_k) / (β_n, β_n)`, where `k = 1, ..., n`.

An orthonormal basis is obtained by dividing all vectors by their own magnitudes.

If `s = {α_1, ..., α_n}` is an orthonormal basis for the vector space and `v` in `V` is any vector in the vector space, then there are some constants such that `v = a_1 * α_1 + a_2 * α_2 + ... + a_n * α_n`. Finding the multipliers (the coefficients) is a cake walk.

`(α_1, v) = (α_1, a_1 * α_1) + (α_1, a_2 * α_2) + ...`, so `a_1 = (α_1, v) / 1`.

***
#### 2 March: Vector and Matrix Norms
***

A norm is a way to measure, but we demand certain properties:

**Definition:** Suppose `V` is a vector space. Then, `N: V → R` is a norm if:

1. `N(v) >= 0` for all `v` in `V`; `N(v) = 0` if and only if `v = 0`
2. `N(α * v) = |α| * N(v)`, for all `v` in `V` and for all `α` in `R`
3. `N(v + w) <= N(v) + N(w)`, for all `v` and `w` in `V`

**p-norms**

`||v||_p = [∑_{n} |x_i|^p]^(1/p)`, with `v = <x_1, x_2, ..., x_n>`

`p = 1`: `||v||_1 = ∑_{i = 1, n} |x_i|`
`p = 2`: `||v||_2 = sqrt(∑_{i = 1, n} |x_i|^2)`
`p = ∞`: `||v||_∞ = max_i |x_i|`

Given `v = <4, 0, -2, 1>`,
- `1`-norm = `7`
- `2`-norm = `sqrt(21)`
- `∞`-norm = `4`

**Theorem:** Equivalence of Norms

Let `N` and `M` be norms on `R^n`. Then, there exist constants `c_1 > 0` and `c_2 > 0` such that `c_1 * M(x) <= N(x) <= c_2 * M(x)`, for all `x` in `V`, where `V = R^n`.

**Lemma:** Continuity of Norms

Let `M(x)` be a norm on `R^n`. Then, `M(x)` is a continuous function of the `x_i` for all `x` in `R^n`, where `(x = <x_1, x_2, ..., x_n>)`

**Proof:** Show that, if `x_i ≈ y_i`, then `N(x) ≈ N(y)`.

We have `(x - y) = ∑_{i = 1, n} (x_i - y_i) * e_i`. So, `N(x - y) = ||x - y|| <= ∑_{i = 1, n} |x_i - y_i| * N(e_i) <= ∑_{i = 1, n} max_i |x_i - y_i| * N(e_i) = ||x - y||_∞ * ∑_{i = 1, n} N(e_i)`

Now, using the Reverse Triangle Inequality, `|N(x) - N(y)| <= N(x - y) <= ||x - y||_∞ * c`, where `c = ∑_{i} N(e_i)`. Equivalently, `|N(x) - N(y) <= c * ||x - y||_∞`

**Proof:** Equivalence of Norms

It is sufficient to show for an arbitrary norm `N` and a specific norm, `||...||_∞`. Thus show that, for each of `c_1, c_2 > 0`, `c_1 * ||x||_∞ <= N(x) <= c_2 * ||x||_∞`.

From the lemma, if we set `y = 0`, we have `|N(x) - N(y)| <= c_2 * ||x - y||_∞`, so `N(x) = |N(x)| <= c_2 * ||x||_∞`, and `N(x) <= c_2 * ||x||_∞`.

So, the upper inequality holds. The lower inequality holds because all norms are bounded away from `0`.

`c_1 * ||x||_∞ <= N(x) <= c_2 * ||x||_∞`.

Because of this, we know there are two more constants, `c_3` and `c_4` which show `c_3 * ||x||_∞ <= M(x) <= c_4 * ||x||_∞`

Why is this important? What does this theorem give us?

When you judge whether a set sequence of vectors diverges or converges to some value, it doesn't matter which norm you use.

***
#### 5 March: Matrices
***

A matrix (an augmented matrix) can be used to represent a system of linear equations, and our matrix operations can easily be used to solve (or not) the system.

**Theorem:** Notebook
**Matrix Norms:** Notebook
**Example:** Notebook
**Frobenius (Operator) Norms:** Notebook

***
#### 7 March: Your Matrix Class, The Exam
***

**Matrix Implementation Ideas**

1. `Array<Array<T>> element;`
    - Quick and dirty
    - Easy
    - Lot of constructor calls
2. `Array<T>* element;`
    `element = new Array<T>[rows];`
    - Pointer to array of points
3. `T * element;`
    `element = new T[rows * cols];`
    - Pointer arithmetic
    - One single array
4. `T** element;`
    `element = new T*[rows];`
    `for (i = 0; i < rows; i++) element[i] = new T[cols];`

Whichever you choose, think in terms of future assignments. Think in terms of derivations (what the child classes will look like, what they'll inherit from the base).

Consider different matrices (upper triangular, lower triangular, sparse, banded).

**The Exam**

1. Makefiles
2. UML
3. C++ (chapters 4-7)
4. A bunch of linear algebra crap
    - Norms
    - Inner product space
    - Equivalence of Norms Theorem
    - Cauchy-Schwarz and Triangle-Inequality (proofs, too!)
5. CS1570 format
    - What's wrong with this code?
    - Write some code to do such-and-such.
    - What's the principle at play?
    - Why do we do things this way?
    - Why is this a good idea?
6. Also, look at Chapter 8. Don't read 1-3.
7. Look at the sidebar links on his website.

***
#### 12 March: Tools to Fix Your Code
***

**gdb-GNU Debugger**

When compiling, use the `-g` flag. When running gdb, use the following commands:

| command | action |
| - | - |
| `break` | set a breakpoint |
| `step` | runs the next line of code |
| `print` | print a variable |
| `next` | runs the next line in the current function |
| `backtrace` | view the current backtrace |
| `continue` | resume where we left off |
| `set` | change a variable's value |
| `finish` | run until the function returns |

**Valgrind**

memcheck can find four different kinds of problems:
1. Uninitialized values
2. Invalid reads/writes
3. Mismatched/double deletes
4. Memory leaks

**Cachegrind**

You can use this to see what happens when the code runs. For example, view charts about processor utilization, order of calls, the number of calls, etc.

***
#### 14 March: Lambdas in C++
***

- Anonymous functions
- Closures
- Shorthand for functor classes
- Good for writing "glue code"

**Syntax**

```C++
auto func = []() -> int { /* body */ };
```

Here, `auto` is required, `[]` is the capture clause, `()` is for the parameters, `-> int` is the return type (this is optional), and `{}` houses the body.

**Examples**

```C++
auto hi = []() { cout << "hello"; };
hi();
```

```C++
auto add5 = [](int x) { return 5 + x; };
add5(2); // 7
```

```C++
int amount;
cout << "Enter the amount: ";
cin >> amount; // let's say we get 2

auto add_some = [=](int x) {
    return x + amount;
};

add_some(3); // 5
```

```C++
template <class Func>
void call_with_3(Func f) {
    f(3);
}
```

**Capture Clauses**

- `=` → capture by value
    - makes a `const` copy
- `&` → capture by reference
    - lets you modify things in the calling function
- `this` → captures the calling object

**More Examples**

```C++
int amount = 7;
int calls = 0;

auto add_and_count = [amount, &calls](int x) -> int
{
    calls++;
    return x + amount;
}

call_with_3(add_and_count); // 10
cout << calls << endl; // 1
```

```C++
auto Lambda = [=](Vector<float> v) { return v * scalar; };

// alternatively
class MS
{
private:
    float the_scalar;

public:
    MS(float scalar): the_scalar(scalar) {}
    Vector<float> operator()(Vector<float> v) { return v * the_scalar; }
};
```

**Passing Call-ables to Functions**

What is a callable?
- Function pointers
- Functor classes
    - → `operator()`

```C++ i;

call_with_3(int (*f)(int)) {
    f(3);
}
```

```C++
template <class Func>
call_with_3(Func f) {
    f(3);
}
```

***
#### 19 March: Commonality of Behavior
***

The greatest utility of a language like C++ is its object-oriented design. One of the principal reasons for a course like this one is to express the usefulness of its capabilities. Object-oriented programming not only gives us the ability to closely model reality, but it also allows us to produce code that is adaptable and maintainable.

So far, we've learned about encapsulation. We encapsulate data and functionality into classes. We do this so as to be able to better control *how* objects are used and modified. So, now we take the concept of abstraction one level higher. By using inheritance and virtual functions, we will be able to model the idea "is usable as." Here, then, we introduce base classes.

| GPIBController |
| - |
| +insert(char *, int) |
| +send(int, float) |
| +send(int, char *) |
| +receive(int): float |

| Acme130 |
| - |
| -my_control: GPIBController |
| -address: int |
| +Acme130( ) |
| +set( ) |
| +min( ) |
| +max( ) |

| VoltyMetrics |
| - |
| -controller: GPIBController |
| -address: int |
| +VoltyMetrics( ) |
| head( ): float |

| Calibration(Acme130& ...) |
| - |

In the real world, objects have *state* and *behavior*. State describes - at any instant in time - what the object *is*. Behavior describes how it affects other objects and how it is affected by others - how it responds to stimuli. C++ objects have the same descriptors. They have member variables (the object's state) and member functions (the object's behavior).

```C++
class GPIBController
{
public:
    void insert(char* device_name, int address);
    void send(int address, char* command);
    void send(int address, float v_value);
    float receive(int address);
}; // notice there are no state variables - only behavior

class Acme130
{
public:
    Acme130(GPIBController& control, int address);
    void set(float volts);
    float min() const;
    float max() const;

private:
    GPIBController my_controller;
    int gpib_address;
};

Acme130::Acme130(GPIBController& control, int address)
{
    my_controller = control;
    gpib_address = address;
    my_controller.insert("Acme130", gpib_address);
}
```

**Encapsulation**

The goal of encapsulation is to hide information. What information? We hide data... in obvious ways/reasons. We also hide implementations of behavior. We hide *how* functionality is coded; we also do this by not exposing data structures and by using general terms - using iterators, for example. In addition, we can add layers of abstraction using inheritance. For example, we may want a voltage supply to hook into the `GPIBController`. Do we care what kind it is? Probably not. As long as the object has the functionality we require, we don't care whether it's an `Acme130` or a `VoltOn59` (the `VoltOn59` is pretty much exactly the same as the `Acme130`).

We can *generalize* the Acme and VoltOn voltage supplies to a type of object that we will call `VoltageSupply`. Separating the code that specifies implementation of objects from code that uses the objects and so allows code that uses object to access *only behavior* (the functions) and not state representation (data structures) gives us code that is adaptable and versatile. So, implementation can change while interface remains unchanged.

***
#### 21 March: Interface Bases
***

To our system, we add two components: first, a meter for testing voltages, and second, a function called `calibrate()`, which is used to calibrate voltage supply.

```C++
#include <math.h> // fabs()

class VoltyMetrics
{
public:
    VoltyMetrics(GPIBController& control, int address);
    float read() { return my_controller.receive(address); };

private:
    GPIBController my_controller;
    int my_address;
};

// non-member
float calibrate(Acme130& supply, VoltyMetrics& meter, float test_voltage)
{
    supply.set(test_voltage);
    return fabs(test_voltage - meter.read()); // doesn't really matter
}
```

So, we put this system together and it all works fine. However, what happens when our Acme130 blows up and is no longer usable? Well, in the real world, we walk down the hall and borrow a VoltOn59 from Larry. So, it works, it is plug-adaptable... except, in our model, it just doesn't.

The code requires an Acme130. So, what? Change the code. The problem is that we can go on changing code forever, and that's just impractical. Instead, we're going to think *globally*. We're not going to code for specifics; we're going to code for *categories*.

We now make our first base class.

```C++
class VoltageSupply
{
public:
    virtual void set(float volts) = 0;
    virtual float min() const = 0;
    virtual float max() const = 0;
    virtual ~VoltageSupply() {};
};
```

The functions that end in `= 0` are *pure virtual functions*. No instances of any class containing pure virtual functions can be created. We cannot create a `VoltageSupply`. This class is what we call an *interface class* (or *interface base*). Usually, interface bases contain pure virtual functions - but they don't have to - and no state (member variables). The term *interface base* is a design term, not a C++ term. The idea is to put common behavior in an interface. We now have to change `calibrate()`.

```C++
float calibrate(VoltageSupply& supply, VoltyMetrics& meter, float test_voltage)
{
    // ...
}
```

We also need to change `Acme130` and `VoltOn59`.

```C++
class Acme130: public VoltageSupply
{
public:
    Acme130(GPIBController& control, int address);
    virtual void set(float volts);
    virtual void min() const;
    virtual void max() const;

private:
    GPIBController my_controller;
    int my_address;
};
```

Now, the `Acme130` is usable as a `VoltageSupply`. Likewise, the `VoltOn59` (it's identical to `Acme130`) is usable as a `VoltageSupply`.

**Note:** The designation of `public` derivation means that instances of this derived class can be used through references and pointers to the base class (like in the parameter list for the `calibrate()` function).

The `virtual` designations in a derived class are *not* required, but they are convention (to make it clear that the functions are derived from a pure virtual class).

All aspects of the function signature in the derived class(es) should be the same as in the base.

Interface base classes don't have constructors. They don't have state variables.

An *interface base* has no data. An *abstract base* has *pure virtual* functions. You should use abstract bases as your interface in function parameters. This is because you are forcing the compiler to flag any undefined functions in a derived class that aren't defined.

Classes can be derived from more than one base. Let's consider writing a second interface base class. We'll create a base class for the category of any GPIB-type device or instrument (i.e. not just voltage supplies).

```C++
class GPIBInstrument
{
public:
    virtual void send(char*) = 0;
    virtual void send(float) = 0;
    virtual float receive() = 0;
    virtual ~GPIBInstrument() {};
};
```

This is an interface for a generalized GPIB instrument. The `Acme130` and the `VoltOn59` are both of this type.

```C++
class Acme130: public VoltageSupply, public GPIBInstrument
{
public:
    Acme130(GPIBController& control, int address);

    // VoltageSupply interface
    virtual void set(float volts);
    virtual void min() const;
    virtual void max() const;

    // GPIBInstrument interface
    virtual void send(char*);
    virtual void send(float);
    virtual float receive();

private:
    GPIBController my_controller
    int my_address;
};
```

**Note:** The second derivation is independent of the first.

***
##### 23 March: More Interface Bases
***

So, we have seen that client code, such as the calibration function, that can be passed an object of a derived type via a reference or pointer to a base class (parameter). We can also make an interface base type a member of a class. If we do this, however, it has to be as a reference (or pointer) to that type.

Let's show this by generalizing the `GPIBController` to make it an interface - representing any of many types of GPIB controllers.

```C++
class GPIBController
{
public:
    virtual void insert(...) = 0;
    virtual void send(...) = 0;
    virtual void send(...) = 0;
    virtual float receive() = 0;
    virtual ~GPIBController();
};
```

We'll now modify the original `GPIBController`. Let's call it `GPIB`.

```C++
class GPIB: public GPIBController
{
public:
    virtual void insert(...);
    virtual void send(...);
    // et cetera
};
```

Thus, it's the same as before except that it derives from an interface base class. Now, we must generalize the `Acme130` (and `VoltOn59`).

```C++
class Acme130: public VoltageSupply, public GPIBInstrument
{
public:
    Acme130(GPIBController& controller, ...);
    // ...

private:
    GPIBController& my_controller;
    // ...
};
```

The constructor initializes the reference data member so that the controller is of any kind of `GPIBController`, and we really don't need to know what kind (the details) as long as it can be derived from the interface and have its functionality.

As another example, let's create an IV (current-voltage) tester class.

```C++
class IVTester
{
public:
    IVTester(VoltageSupply& vs, VoltyMetrics& vm): supply(vs), meter(vm) {};
    float current(...);

private:
    VoltageSupply& supply;
    VoltyMetrics& meter;
};
```

We can now create an `IVTester` object with any combination of `VoltageSupply` and `VoltyMetrics` that we want.

Now, is it possible to have an array of these different instruments? Yes - we create an array of pointers to the interface base type. As long as the objects that the pointers point to are derived from that base type, we can *connect* them.

```C++
class GPIBController_GIS: public GPIBController
{
public:
    // ...

private:
    const SimulatorFactor& sim_fact;
    Array<GPIBInstrumentSimulation*> simulators;
};
```

***
#### 2 April: Commonality of Implementation
***

In chapter 9, we focused on commonality of behavior; that lead to virtual functions and interface bases. An interface had no state variables.

Now our goal is to deal with repeated code. We will now use extension by public inheritance to avoid the replication of code.

Recall from chapter 4 that we extended functionality of the `Array` class to become the `CheckedArray`. Now, we'll do the same thing - but in a different way.

```C++
class CheckedArray: public Array
{
public:
    CheckedArray();
    CheckedArray(int n);

    float& operator[](int i);
    CheckedArray& operator=(float n);

    class SubscriptErr
    {
        // ...
    };
};

class Array
{
public:
    Array();
    Array(int n);
    Array(const Array& a);
    ~Array();

    float& operator[](int i);
    Array& operator=(const Array& s);
    Array& operator=(float n);

    void set_size(int n);
    int num_elements();

private:
    int num_elements;
    float* ptr_to_data;
    void copy(...);
};
```

Notice that we have *no* virtual functions and the base has data (member variables). This is *not* an interface base. Notice also that it provides implementation. This base class is called an *implementation base*. Any instance of a derived class contains an instance of the base called the *base subobject*.

Each member of the base is inherited by the derived class except:
- Constructors
- Destructors
- Assignment operators

Any function defined in the derived class that has the same name is said to hide that which is in the base. Thus, the `CheckedArray` class has its own `operator[]` and its own `operator=` - along with constructors.

Let's write `operator[]`:

```C++
float& CheckedArray::operator[](int i)
{
    if (i < 0 || i > num_elements()) throw SubscriptError();
    return Array::operator[](i); // using the functionality of the base
}
```

Again, unless defined for the derived class, the constructors and destructor will automatically be generated by the compiler. A derived class must provide for construction of any subobject in addition to any new members.

```C++
CheckedArray::CheckedArray(int i): Array(n) {}
```

This (the `Array(n)`) is called a base initializer.

Of course, it is best to provide a *default* constructor for several reasons:
1. If you want an `Array` of this particular kind of object
2. The existence of any other constructor will suppress the automatic default constructor

Also, if C++ generates its own copy constructors, it will call the base class version. This is also true for the copy-assignment operator. Remember that the `operator=` functions are not inherited.

```C++
CheckedArray& CheckedArray::operator=(float n)
{
    Array::operator=(n);
    return *this;
}
```

**Clients**

Derived class objects can be passed to pointers and references to the base class type. If so, though, the base class functionality applies.

```C++
float max_element(Array& a)
{
    // ...
    // operator[] used at some point
    return max;
}
```

Which `operator[]` is used? Is it checked or is it unchecked? It's the base class version (unchecked) that is used.

***
#### 4 April: What About Interfaces and Extension by Inheritance?
***

The Acme Corporation introduces their new voltage supply, the Acme140, which behaves a lot like the Acme130, but...

```C++
class Acme140: public Acme130
{
public:
    enum Jumper {J1, J2};
    Acme140(GPIBController& controller, int address, Jumper j);
    virtual float max() const;

private:
    float max_volts;
};
```

So, the `Acme140` inherits everything from the `Acme130`, but it also allows for a jumper value to determine its max voltage output. Thus, the `Acme140` is usable as an `Acme130`, as a `VoltageSupply`, and as a `GPIBInstrument`. Note, however, that the `Acme130` is an implementation base and *not* an interface base. So, we have to have a constructor for the `Acme140`.

```C++
Acme140::Acme140(GPIBController& controller, int address, Jumper j):
    Acme130(controller, address), max_volts(j == J1 ? 10 : 50) {};
```

So, here it calls the `Acme130`'s base initializer and then initializes its own member variable.

We must also provide a new `max()` function for the `Acme140` so that `Acme140` objects can indeed behave like `Acme140` objects.

```C++
float Acme140::max() const
{
    return max_volts;
}
```

Derived-class-defined virtual functions *override* the base class definitions.

```C++
// ...
Acme140 supply(gpib, 16, Acme140::J2);
cout << supply.max() << endl; // 50

Acme130& as_130 = supply;
cout << as_130.max() << endl; // 50

VoltageSupply& as_vs = supply;
cout << as_vs.max() << endl; // 50
```

Why? Because the function (`max()`) is virtual. We get object-oriented behavior.

```C++
void Acme130::set(float v)
{
    if (v > max() || v < min()) throw hissy_fit();
    send(v);
    return;
}
```

Since the `Acme140` derives from the `Acme130` and `max()` is virtual, then the correct `max()` is called - that for the `Acme140` rather than for the `Acme130`. So,

```C++
supply.set(20); // works
as_130.set(20); // works

Acme140 s(gpib, 3, Acme140::J1);
s.set(20); // error (because J1 is out of bounds)
```

**Problems That Result From Public Inheritance**

1. Public inheritance exposes implementation - an implementation decision. Recall: you should hide implementation (decisions). If client code exploits that decision, then change will hurt. If you decided to change how you implement, then clients will also have to change.
2. We have forced the use of virtual functions, even though that is what gives us the true object-oriented behavior. If the virtual funciton `max()` was not virtual, then the `max()` invoked would be determined by the reference or pointer it was used through. So, if `max()` was not virtual, then calling it for an `Acme140` as an `Acme130` would result in an incorrect value. Virtual function overhead is expensive, but extension by inheritance requires us to use them. Thus, avoid new definitions of functions in derived classes if they are non-virtual. Public inheritance suggests that we declare base class functions virtual unless there's a *good* reason not to:
    1. When making a concrete class (for a specific purpose - unlikely to be extended), then you save on the virtual function overhead.
    2. Sometimes you simply want consistency from parent to child. Consider:
        ```C++
        void print_max(Acme130 a)
        {
            cout << "Max voltage is " << a.max() << endl;
            return;
        }
        ```
        ...and `supply1` and `supply2` of the type `Acme140` with `supply1` having a max of 50 and `supply2` having a max of 10.
        ```C++
        print_max(supply1); // 10
        print_max(supply2); // 10
        ```
        This is because the parameter to the function is *not* a reference, and, because it's not, a copy is made... of the base subobject. That parameter is treated like an `Acme130` and not like an `Acme140`. The `Acme130` is both the implementation and interface base for the `Acme140`, so the compiler can't catch the mistake. The lesson is: use references to base classes to have the compiler help you stay on the straight and narrow path to C++ salvation.

***
#### 6 April: Member Function Forwarding
***

```C++
class Acme130: public VoltageSupply, public GPIBInstrument
{
public:
    Acme130(GPIBController& control, int address);
    // VoltageSupply interface
    // ...
    // GPIBInterface
    virtual void send(char*);
    virtual void send(float);
    virtual float receive();

private:
    GPIBController my_controller;
    int my_address;
};
```

Well, the `VoltOn59` looks exactly like this and also derives from `GPIBInstrument`. Likewise, the `VoltyMetrics` derives from `GPIBInstrument`.

All three of these classes derive from `GPIBInstrument` and use the same functions and data. We can avoid all of this replication by placing it all in another class and employing *function forwarding*.

```C++
class GPIBInstrumentData
{
public:
    GPIBInstrumentData(GPIBController& control, int address, const char* name);
    // instrument interface
    void send(char*);
    void send (float;
    float receive();

private:
    GPIBController& my_controller;
    int my_address;
};
```

**Note:** It is structurally the same as the `Acme130`, the `VoltOn59`, and the `VoltyMetrics`, but it is not like the interface bases.

The function implementations are the same as was in the `Acme130`, the `VoltOn59`, and the `VoltyMetrics`, but we now have just one version.

**Example**

```C++
void GPIBInstrumentData::send(float f)
{
    my_controller.send(my_address, f);
    return;
}
```

The constructor is going to initialize the data and it inserts the device onto the controller. However, since it isn't for a particular device, it has to have a third parameter; that's the name of the device.

```C++
GPIBInstrumentData::GPIBInstrumentData(GPIBController& control, int address), const char* name):
    my_controller(control), my_address(address)
{
    my_controller.insert(name, address);
}

// now, we need to fix the classes
class Acme130: public VoltageSupply, public GPIBInstrument
{
private:
    GPIBInstrumentData gpib_rep;

public:
    Acme130(GPIBController& control, int address): gpib_rep(controller, address, "Acme130") {};
    // VoltageSupply interface
    // instrument interface
    virtual void send(char* c) { gpib_rep.send(c); }
    virtual void send(float f) { gpib_rep.send(f); }
    virtual float receive() { return gpib_rep.receive(); }
};
```

**Note:** My notebook contains a diagram (Figure 10) of the implementation.

**Problems**

1. Still have replicated code
2. What if you want to derive something?

***
#### 9 April: Implementation Using Inheritance
***

We now use inheritance/derivation to accomplish what we set out to do with function forwarding. First, change the `GPIBInstrumentData` to be derived from the `GPIBInstrument` so that it implements our interface.

```C++
class GPIBInstrumentData: public virtual GPIBInstrument
{
public:
    GPIBInstrumentData(GPIBController& control, int address, const char* name);
    // GPIB interface
    virtual void send(char*);
    virtual void send(float);
    virtual float receive();

private:
    GPIBController& my_controller;
    int my_address;
};
```

So, the `GPIBInstrumentData` is usable as a `GPIBInstrument`. The `GPIBInstrument` is a *virtual base*. The implementations of the functions remain the same. Now, we rewrite the `Acme130`.

```C++
class Acme130:
    public virtual VoltageSupply,
    public virtual GPIBInstrument,
    private GPIBInstrumentData
{
public:
    Acme130(...);
    // VoltageSupply interface
    virtual void set(...);
    virtual float min();
    virtual float max();
};
```

**Notes**

- The virtual derivations from base classes
- The private derivation from the ipmlemenation base
- Any instance of an `Acme130` contains a base sub-object of the base
- Because of the private derivation, it is *not* usable as a `GPIBInstrumentData` object
- The private derivation *hides* the implementation of this class

Notice, the `Acme130` does *not* implement the instrument interface functions. Those are privately inherited from the `GPIBInstrumentData` interface. Public access of the member functions is blocked. Clients have to access the functionality throught that interface. They have to access them through the *same* interface base as the `GPIBInstrumentData`. This is the reason for the virtual derivation. Function calls are routed through the implemenation base.

**Example**

```C++
Acme130 s(gpib, 4);
s.send(10); // we can't do this. the private derivation prohibits it

GPIBInstrument* g = new Acme130(gpib, 4);
g.send(10); // this works fine

GPIBInstrument& t = *g;
t.send(10); // this also works
```

The whole idea is that you use the functionality of the particular devices through the interface. You can also *override* the privacy inherited from that implementation base by declaring something public.

```C++
class Acme130:
    public virtual VoltageSupply,
    public virtual GPIBInstrument,
    private GPIBInstrumentData
{
public:
    GPIBInstrumentData::send;
    // ...
};
```

Then, all of the code in the example above will work. However, this goes against the whole idea of inheriting privately.

***
#### 11 April: Scope, Dominance, and Accessibility
***

Inheritance works because the compiler resolves names. A name is an identifier with a meaning. A name has three attributes that we will look at now: scope, dominance, and accessibility. Resolving these will determine how the name works.

We declare a name. Its *scope* is determined by how it is nested in classes, functions, and blocks of code. Whether it *dominates* or is dominated by other names with the same identifier will be determined by its relationship of its class with other class in a directed acyclic graph (DAG). Additionally, of course, *accessibility* is determined by the designation as public, private, or protected.

Identifiers must resolve to the present scope.

```C++
class Acme230:
    public virtual VoltageSupply,
    public virtual GPIBInstrument,
    private Acme130
{
public:
    Acme230(...);
    virtual float max();
    virtual void set(float v);
    enum Range {low, high};
    virtual void set(Range r);

private:
    Range setting;
};
```

**Note:** My notebook contains a diagram (Figure 11) of the implementation.

**Scope**

- Private derivation from an implementation base - `Acme130` - and public virtual derivation from its `VoltageSupply` and instrument bases
- Introduces new names: high and low
- Introduces a new set functionality (for range value)
- Redeclare the other (old) `set()` and `max()` functions

```C++
float Acme230::max()
{
    if (setting == high)
        return 100;

    return 10;
}
```

Here, the names high and low must be scoped because, outside the `Acme230` class, they have no meaning.

```C++
Acme230 s(gpib, 1);
s.set(Acme230::high);
```

Derived classes' scope will include the scopes of all base classes. So, all names in the `VoltageSupply`, `GPIBInstrument`, and `Acme130` bases are in the `Acme230`'s scope. Names are inherited. It operates much like block scope.

A name declared in an inner block *hides* the name (with the same identifier) in an outer block. Let's look at `max()`. The `Acme230` inherits `max()` from the `Acme130`, but redeclaring it in the `Acme230` *hides* the `Acme130` meaning. `min()` is also inherited, but it is not hidden.

Now, if the name of a virtual function is redeclared in a derived class and matches in the number and types of parameters, then the base class name is *hidden* and *overridden*. If the number and types of the parameters doesn't match, then *only* hiding takes place, and you're likely to get a compiler warning... generally not a good idea. Since we want both types of `set()` function, we have to ensure that we do have both types.

**Note:** Name hiding occurs before and independently of function overloading. Thus, we declare the following:

```C++
void Acme230::set(float v)
{
    Acme130::set(v);
}
```

**Dominance**

Here's the problem: suppose that our class has two bases and inherits the same name from both. Which one applies? For multiple bases, this is a matter of *dominance*. For a single base, this is a matter of *hiding*.

A name declared in a derived class will *dominate* the same name in any base class. As an example, `min()` is declared in the `Acme130`. Now, consider the `Acme230` which derives from both the `Acme130` and the `VoltageSupply`. So, which `min()` applies? It's the `Acme130`'s `min()`. Why? Well, the name that dominates in any DAG will be the one that dominates in that sub-DAG. If a name dominates in a two-class DAG, it will dominate in any DAG that contains it.

***
#### 16 April: Partial Differential Equations
***

In most scientific investigations (that are worth doing), you model reality using partial differential equations (PDEs). Why? In most studies, you have many variables controlling the system. To model with only one variable is really unrealistic. A PDE can be characterized as the following: an equation relating one or more dependent variables to two or more independent variables and their derivatives with respect to those independent variables.

Some famous partial differential equations include Poisson's Equation, the Heat Equation, and the Wave Equation. In the first one, if `f ≡ 0`, then we call it Laplace's Equation.

What is a solution to a PDE? We're looking for any function of the required number of independent variables that satisifies the PDE. Solving PDEs is really, really, really difficult.

**Boundary Value / Initial Value Problems**

We will solve the Direchlet Problem for Poisson's Equation. This means we know the solution to the PDE on the boundary of the domain. Here's our problem: solve `uxx + uyy = g(x, y)` for `(x, y) ∈ Ω` with `u(x, y) = f(x, y)` on boundary of `Ω`. To make this problem tractable, let's assume the domain, `Ω`, is the unit square `0 ≤ x ≤ 1`, `0 ≤ y ≤ 1`. Let's take `N > 0` to be the number of partitions of both dimensions with `h = 1 / N`.

If we assume that the function we seek is `e^4(Ω)`, and that we'll then use the `O(h^2) centered difference formula for the approximation to the second derivative of the function, we can use the following derivative for our solution:

`G"(x) = [G(x + h) - 2G(x) + G(x - h)] / h^2 - (h^2 / 12) * G""(η)`, where `η` is necessarily unknown. If we use this approximation to the second derivative in our PDE in both directions, we get [continued in notebook]...

***
#### 18 April: Curiously Recursive Template Pattern (Barton-Nackmon Trick)
***

**Applications**

- Static polymorphism
    - No Vtable
    - Objects use less RAM
    - Function calls can be much faster
    - More complex
    - Results in more code/a bigger executable
    - Must know types at compile time
- Mixins
    - Provide common implementation of features

**Implementation**

```C++
// interface
template <class Derived>
class Base
{
public:
    int foo()
    {
        return asDerived.foo();
    }

    Derived& asDerived()
    {
        return static_cast<Derived&>(*this);
    }
};

class Seven: public Base<Seven> // curiously recursive
{
public:
    int foo()
    {
        return 7;
    }
};

Seven s;
std::cout << s.foo() << std::endl; // 7

Base<Seven> b;
std::cout << b.foo() << std::endl; // 7

template <class Derived>
void print_foo(Base<Derived> b) // not virtual, so we can pass by value
{
    cout << b.foo() << endl;
}

// this works
Base<Seven> s;
Base<Eleven> e;
print_foo(s);
print_foo(e);

// this also works
Seven s;
Eleven e;
print_foo(s);
print_foo(e);
```

Let's edit the two classes.

```C++
template <class Derived>
class Base
{
public:
    int foo()
    {
        return asDerived.foo_impl();
    }

    Derived& asDerived()
    {
        return static_cast<Derived&>(*this);
    }

    int foo_impl()
    {
        return -1;
    }
};

class Seven: public Base<Seven> // curiously recursive
{
public:
    int foo_impl()
    {
        return 7;
    }
};
```

What if we want to call the function that returns `-1`? Add this class:

```C++
class neg_one: public Base<neg_one> {};
```

**Mixins**

```C++
void asdf(Shape& s)
{
    // Shape* bob = new Shape(s); // can't do this because Shape is abstract
    Shape* bob = s.clone();
}
```

***
#### 23 April: Callbacks
***

**Method 1**

```C++
double integrate(double a, double b, int num_points, double (*f)(double))
{
    double delta = (b - a) / (num_points - 1);
    double sum = 0;

    for (int i = 0; i < num_points; i++)
        sum += f(a + i * delta);

    return sum * (b - a) / num_points;
}

// the call
double something = integrate(3, 4, 100, cos);
```

**Method 2**

```C++
class Integrator
{
public:
    double integrate(double a, double b, int num_points)
    {
        double delta = (b - a) / (num_points - 1);
        double sum = 0;

        for (int i = 0; i < num_points; i++)
            sum += function_to_integrate(a + i * delta);

        return sum * (b - a) / num_points;
    }

    virtual double function_to_integrate(double x) = 0;
};

class IntegrateMe: public Integrator
{
public:
    virtual double function_to_integrate(double x)
    {
        return cos(0);
    }
};

// the call
IntegrateMe f;
double something = f.integrate(0, 3.14159, 4500);
```

**Method 3**

```C++
class Function1
{
public:
    double operator()(double x)
    {
        return 1.0 / (1 + x * x);
    }
};

template <class T_function>
double integrate(double a, double b, int num_points, T_function f)
{
    double delta = (b - a) / (num_points - 1);
    double sum = 0;

    for (int i = 0; i < num_points; i++)
        sum += f(a + i * delta);

    return sum * (b - a) / num_points;
}

// the call
double something = integrate(1, 2, 100, Function1());
```

**Method 4**

```C++
double function1(double x)
{
    return 1.0 / (1 + x * x);
}

template <double T_function(double)>
double integrate(double a, double b, int num_points)
{
    double delta = (b - a) / (num_points - 1);
    double sum = 0;

    for (int i = 0; i < num_points; i++)
        sum += T_function(a + i * delta);

    return sum * (b - a) / num_points;
}

// the call
double something = integrate<function1>(1, 2, 100);
```

***
#### 27 April: Traits Classes
***

With traits classes, you can create a mapping of typenames that can be very useful. Here are some examples:

**Example 1**

Suppose that you are writing pieces of a code library that is template-able on various numerical types (e.g. `float`, `double`, `long double`). Each type has a set of constants associated with it (e.g. maximum exponent value, an epsilon, a max mantissa) all defined in some header somewhere. The confusion occurs when, after templating on a specific type, you need `FLT_MAX_EXP` or `DBL_MAX_EXP` or whatever, the compiler needs a way to determine which you want.

Here's the traits solution to the problem:

```C++
template <class numT>
struct float_traits {}

struct float_traits<float>
{
    typdef float float_type;
    enum { max_exponent = FLT_MAX_EXP };
    static inline float_type epsilon() { return FLT_ESPILON; }
};

struct float_traits<double>
{
    typedef double float_type;
    enum { max_exponent = DBL_MAX_EXP };
    static inline float_type epsilon() { return DBL_EPSILON; }
};

// et cetera
```

So, when referring to a `max_exponent` without knowing the type, we have no worries. For example:

```C++
template <class numT>
class Matrix
{
public:
    typedef numT num_type;
    typedef float_traits<num_type> traits_type;
    inline num_type epsilon() { return traits_type::epsilon(); }
};
```

**Example 2**

The `average()` function:

```C++
template <class T>
T average(const T* data, const int n)
{
    T sum = 0;

    for (int i = 0; i < n; i++)
        sum += data[i];

    return sum / n;
}
```

This works just fine if you want the return type to be the same as the template type. What if you don't want that, though? Here's the traits solution to the problem:

```C++
template <class T>
struct float_trait { typedef T T_float; };

template <>
struct float_trait<char> { typedef double T_float; };

template <>
struct float_trait<int> { typedef double T_float; };

// et cetera
```

So, what you map *from* is the template parameter, and what we map *to* is what you put inside the struct. Then, `float_trait<T>::T_float` will evaluate to the appropriate `float` type for that `T_type`.

The `average()` function thus becomes the following:

```C++
template <class T>
typename float_trait<T>::T_float average(const T* data, const int n)
{
    typename float_trait<T>::T_float sum = 0;

    for (int i = 0; i < n; i++)
        sum += data[i];

    return sum / n;
}
```

**Example 3**

Type promotion:

```C++
template <class T1, class T2>
Array<???> operator+(Array<T1>& l, Array<T2>& r);
```

What will be the template-type for the returned `Array`?

Here's the traits solution:

```C++
template <class T1, class T2>
struct promote_trait {};

#define DECLARE_PROMOTE(A, B, C) |
    template <> struct promote_trait<A, B> { typedef C T_promote; };

// macros
// DECLARE_PROMOTE(int, char, int)
// DECLARE_PROMOTE(int, float, float)
// et cetera

// usage
template <class T1, class T2>
Array<typename promote_trait<T1, T2>::T_promote> operator+(Array<T1>& l, Array<T2>& r);
```

There are lots of other ways to use traits classes. Google them.

***
#### 2 May: Exam 2
***

- ~10 problems
- Possibility of subjective answers
- Chapter 9, chapter 10, and the first three sections of 17
- Traits classes
- Callbacks: how the different methods work, why we use callbacks, and what the goal is
- Lambda functions
- Commonality of Behavior
- Commonality of Implementation
- Virtual constructors
