# Considering Performance

## Build Time



### Forward Declare When Possible

This:

```cpp
// some header file
class MyClass;

void doSomething(const MyClass &);
```

instead of:

```cpp
// some header file
#include "MyClass.hpp"

void doSomething(const MyClass &);
```


This applies to templates as well:

```cpp
template<typename T> class MyTemplatedType;
```

This is a proactive approach to reduce compilation time and rebuilding dependencies.

### Avoid Unnecessary Template Instantiations

Templates are not free to instantiate. Instantiating many templates, or templates with more code than necessary increases compiled code size and build time.

For more examples see [this article](http://blog2.emptycrate.com/content/template-code-bloat-revisited-smaller-makeshared).


### Analyze the Build

The tool [Templight](https://github.com/mikael-s-persson/templight) can be used to analyze the build time of your project. It takes some effort to get built, but once you do, it's a drop in replacement for clang++.

After you build using Templight, you will need to analyze the results. The [templight-tools](https://github.com/mikael-s-persson/templight-tools) project provides various methods. (Author's Note: I suggest using the callgrind converter and visualizing the results with kcachegrind).

### Firewall Frequently Changing Header Files



#### Don't Unnecessarily Include Headers

The compiler has to do something with each include directive it sees. Even if it stops as soon as it seems the `#ifndef` include guard, it still had to open the file and begin processing it.

[include-what-you-use](https://code.google.com/p/include-what-you-use) is a tool that can help you identify which headers you need.

#### Reduce the load on the preprocessor

This is a general form of "Firewall Frequently Changing Header Files" and "Don't Unnecessarily Include Headers." Tools like BOOST_PP can be very helpful, but they also put a huge burden on the preprocessor.

### Consider using precompiled headers

### Consider Using Tools

These are not meant to supersede good design

 * [ccache](https://ccache.samba.org/)
 * [warp](https://github.com/facebook/warp), Facebook's preprocessor

### Put tmp on Ramdisk

See [this](https://www.youtube.com/watch?v=t4M3yG1dWho) YouTube video for more details.

### Use the gold linker

If on Linux, consider using the gold linker for GCC.

## Runtime

### Analyze the Code!

There's no real way to know where your bottlenecks are without analyzing the code.

 * http://developer.amd.com/tools-and-sdks/opencl-zone/codexl/
 * http://www.codersnotes.com/sleepy

### Simplify the Code

The cleaner, simpler, and easier to read the code is, the better chance the compiler has at implementing it well.

### Use Initializer Lists

```cpp
// This
std::vector<ModelObject> mos{mo1, mo2};

// -or-
auto mos = std::vector<ModelObject>{mo1, mo2};
```

```cpp
// Don't do this
std::vector<ModelObject> mos;
mos.push_back(mo1);
mos.push_back(mo2);
```

Initializer lists are significantly more efficient; reducing object copies and resizing of containers.

### Reduce Temporary Objects

```cpp
// Instead of
auto mo1 = getSomeModelObject();
auto mo2 = getAnotherModelObject();

doSomething(mo1, mo2);
```

```cpp
// consider:

doSomething(getSomeModelObject(), getAnotherModelObject());
```

This sort of code prevents the compiler from performing a move operation...

### Enable move operations

Move operations are one of the most touted features of C++11. They allow the compiler to avoid extra copies by moving temporary objects instead of copying them in certain cases.

Certain coding choices we make (such as declaring our own destructor or assignment operator or copy constructor) prevents the compiler from generating a move constructor.

For most code, a simple

```cpp
ModelObject(ModelObject &&) = default;
```

would suffice. However, MSVC2013 doesn't seem to like this code yet.

### Kill `shared_ptr` Copies

`shared_ptr` objects are much more expensive to copy than you'd think they would be. This is because the reference count must be atomic and thread-safe. So this comment just re-enforces the note above: avoid temporaries and too many copies of objects. Just because we are using a pImpl it does not mean our copies are free.

### Reduce Copies and Reassignments as Much as Possible

For more simple cases, the ternary operator can be used:

```cpp
// Bad Idea
std::string somevalue;

if (caseA) {
  somevalue = "Value A";
} else {
  somevalue = "Value B";
}
```

```cpp
// Better Idea
const std::string somevalue = caseA?"Value A":"Value B";
```

More complex cases can be facilitated with an [immediately-invoked lambda](http://blog2.emptycrate.com/content/complex-object-initialization-optimization-iife-c11).

```cpp
// Bad Idea
std::string somevalue;

if (caseA) {
  somevalue = "Value A";
} else if(caseB) {
  somevalue = "Value B";
} else {
  somevalue = "Value C";
}
```

```cpp
// Better Idea
const std::string somevalue = [&](){
    if (caseA) {
      return "Value A";
    } else if (caseB) {
      return "Value B";
    } else {
      return "Value C";
    }
  }();
```


### Avoid Excess Exceptions

Exceptions which are thrown and captured internally during normal processing slow down the application execution. They also destroy the user experience from within a debugger, as debuggers monitor and report on each exception event. It is best to just avoid internal exception processing when possible.

### Get rid of “new”

We already know that we should not be using raw memory access, so we are using `unique_ptr` and `shared_ptr` instead, right?
Heap allocations are much more expensive than stack allocations, but sometimes we have to use them. To make matters worse, creating a `shared_ptr` actually requires 2 heap allocations.

However, the `make_shared` function reduces this down to just one.

```cpp
std::shared_ptr<ModelObject_Impl>(new ModelObject_Impl());

// should become
std::make_shared<ModelObject_Impl>(); // (it's also more readable and concise)
```

### Prefer `unique_ptr` to `shared_ptr`

If possible use `unique_ptr` instead of `shared_ptr`. The `unique_ptr` does not need to keep track of its copies because it is not copyable. Because of this it is more efficient than the `shared_ptr`. Equivalent to `shared_ptr` and `make_shared` you should use `make_unique` (C++14 or greater) to create the `unique_ptr`:

```cpp
std::make_unique<ModelObject_Impl>();
```

Current best practices suggest returning a `unique_ptr` from factory functions as well, then converting the  `unique_ptr` to a `shared_ptr` if necessary.

```cpp
std::unique_ptr<ModelObject_Impl> factory();

auto shared = std::shared_ptr<ModelObject_Impl>(factory());
```

### Get rid of std::endl

`std::endl` implies a flush operation. It's equivalent to `"\n" << std::flush`.


### Limit Variable Scope

Variables should be declared as late as possible, and ideally only when it's possible to initialize the object. Reduced variable scope results in less memory being used, more efficient code in general, and helps the compiler optimize the code further.

```cpp
// Good Idea
for (int i = 0; i < 15; ++i)
{
  MyObject obj(i);
  // do something with obj
}

// Bad Idea
MyObject obj; // meaningless object initialization
for (int i = 0; i < 15; ++i)
{
  obj = MyObject(i); // unnecessary assignment operation
  // do something with obj
}
// obj is still taking up memory for no reason
```

### Prefer `double` to `float`

Operations on `double`s are typically faster than `float`s. However, in vectorized operations, `float` might win out. Analyze the code and find out which is faster for your application!


### Prefer `++i` to `i++`
... when it is semantically correct. Pre-increment is [faster](http://blog2.emptycrate.com/content/why-i-faster-i-c) than post-increment because it does not require a copy of the object to be made.

```cpp
// Bad Idea
for (int i = 0; i < 15; i++)
{
  std::cout << i << '\n';
}

// Good Idea
for (int i = 0; i < 15; ++i)
{
  std::cout << i << '\n';
}
```

Even if many modern compilers will optimize these two loops to the same assembly code, it is still good practice to prefer `++i`. There is absolutely no reason not to and you can never be certain that your code will not pass a compiler that does not optimize this.
You should be also aware that the compiler will not be able optimize this only for integer types and not necessarily for all iterator or other user defined types.  
The bottom line is that it is always easier and recommended to use the pre-increment operator if it is semantically identical to the post-increment operator.

