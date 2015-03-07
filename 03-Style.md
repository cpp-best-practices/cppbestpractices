# Style

Consistency of style is more important. Second most importance is following a style that the average C++ programmer is used to reading. 
C++ allows for arbitrary length identifier names, so there's no reason to be terse when naming variables. Use descriptive names, and be consistent in the style

 * `CamelCase`
 * `snake_case`
 
are common examples. snake_case has the advantage that it can also work with spell checkers, if desired.

## Common C++ Naming Conventions

 * Types start with capitals: `MyClass`
 * functions and variables start with lower case: `myMethod`
 * constants are all capital: `const int PI=3.14159265358979323;`

*Note that the C++ standard does not follow any of these guidelines. Everything in the standard is lowercase only.*

## Distinguish Private Object Data

Name private data with a `m_` prefix to distinguish it from public data.

## Distinguish Function Parameters

Name function parameters with an `t_` prefix.


## Don't name anything starting with an `_`

If you do, you risk broaching on names reserved for implementation use

http://stackoverflow.com/questions/228783/what-are-the-rules-about-using-an-underscore-in-a-c-identifier


## Well formed example

```cpp
class MyClass
{
public:
  MyClass(int t_data)
    : m_data(t_data)
  {
  }
  
  int getData() const
  {
    return m_data;
  }
  
private:
  int m_data;
};
```



## Enable out of source directory builds

### Make sure generated files go into build folder

Not the source folder


## Use `nullptr`

C++11 introduces `nullptr` which is a special type denoting a null pointer value. This should be used instead of `0` or `NULL` to indicate a null pointer.

## Comments

Comment blocks should use `//`, not `/* */`. Using `//` makes it much easier to comment out a block of code while debugging.

```cpp
// this function does something
int myFunc()
{
}
```

To comment out this function block during debugging we might do:

```cpp
/*
// this function does something
int myFunc()
{
}
*/
```

which would be impossible if the function comment header used `/* */`

## Never Use `using namespace` In a Header File

This causes the name space you are `using` to be pulled into the namespace of the header file.
It litters the own namespace and it may lead to name collisions in the future. 
Writing `using namespace` in an implementation file is fine though.


## Include Guards

Header files must contain an distinctly named include guard to avoid problems with including the same header multiple times or conflicting with other headers from other projects

```cpp
#ifndef MYPROJECT_MYCLASS_HPP
#define MYPROEJCT_MYCLASS_HPP

namespace MyProject {
class MyClass {
};
}

#endif
```


## {} are required for blocks. 
Leaving them off can lead to semantic errors in the code.

```cpp
// Bad Idea
// this compiles and does what you want, but can lead to confusing
// errors if close attention is not paid.
for (int i = 0; i < 15; ++i)
  std::cout << i << std::endl;

// Bad Idea
// the cout is not part of the loop in this case even though it appears to be
int sum = 0;
for (int i = 0; i < 15; ++i)
  ++sum;
  std::cout << i << std::endl;
  
  
// Good Idea
// It's clear which statements are part of the loop (or if block, or whatever)
int sum = 0;
for (int i = 0; i < 15; ++i) {
  ++sum;
  std::cout << i << std::endl;
}
```

## Keep lines a reasonable length

```cpp
// Bad Idea
// hard to follow
if (x && y && myFunctionThatReturnsBool() && caseNumber3 && (15 > 12 || 2 < 3)) { 
}

// Good Idea
// Logical grouping, easier to read
if (x && y && myFunctionThatReturnsBool() 
    && caseNumber3 
    && (15 > 12 || 2 < 3)) { 
}
```


## Use "" For Including Local Files
... `<>` is [reserved for system includes](http://blog2.emptycrate.com/content/when-use-include-verses-include).

```cpp
// Bad Idea. Requires extra -I directives to the compiler
// and goes against standards
#include <string>
#include <includes/MyHeader.hpp>

// Worse Idea
// requires potentially even more specific -I directives and 
// makes code more difficult to package and distribute
#include <string>
#include <MyHeader.hpp>


// Good Idea
// requires no extra params and notifies the user that the file
// is a local file
#include <string>
#include "MyHeader.hpp"
```

## Initialize Member Variables
...with the member initializer list

```cpp
// Bad Idea
class MyClass
{
public:
  MyClass(int t_value)
  {
    m_value = t_value;
  }

private:
  int m_value;
};


// Good Idea
// C++'s memeber initializer list is unique to the language and leads to
// cleaner code and potential performance gains that other languages cannot 
// match
class MyClass
{
public:
  MyClass(int t_value)
    : m_value(t_value)
  {
  }

private:
  int m_value;
};
```


## Always Use Namespaces

There is almost never a reason to declare an identifier in the global namespaces. Instead, functions and classes should exist in an appropriately named namespaces or in a class inside of a namespace. Identifiers which are placed in the global namespace risk conflicting with identifiers from other (mostly C, which doesn't have namespaces) libraries.

## Avoid Compiler Macros

Compiler definitions and macros are replaced by the pre-processor before the compiler is ever run. This can make debugging very difficult because the debugger doesn't know where the source came from.

```cpp
// Good Idea
namespace my_project {
  class Constants {
  public:
    static const double PI = 3.14159;
  }
}

// Bad Idea
#define PI 3.14159;
```


## Use the correct integer type for stdlib features

The standard library generally returns `size_t` for anything related to size. What exactly `size_t` is is implementation defined.

Make sure you stick with the correct integer types and be consistent with the C++ stdlib. It might not warn on the platform you are currently using, but it probably will when you change platforms.

## Use .hpp and .cpp for your file extensions

Ultimately this is a matter of preference, but .hpp and .cpp are widely recognized by various editors and tools. So the choice is pragmatic. Specifically, VisualStudio only automatically recognizes .cpp and .cxx for C++ files, plus vim doesn't necessarily recognize .cc as a C++ file. 

One particularly large project (OpenStudio) uses .hpp and .cpp for user generated files and .hxx and .cxx for tool generated files. Both are well recongized and having the disctinction is helpful

## Never Mix Tabs and Spaces

Some editors like to indent with a mixture of tabs and spaces by default. This makes the code unreadable to anyone not using the exact same tab indentation settings.

## Never Put Code with Side Effects Inside an assert()

```cpp
assert(registerSomeThing()); // make sure that registerSomeThing() returns true
```

The above code succeeds when making a debug build, but gets removed by the compiler when making a release build, giving you different behavior between debug and release builds.

## Don't be afraid of templates

They can help you stick to DRY principles.

## Use operator overloads judiciously
