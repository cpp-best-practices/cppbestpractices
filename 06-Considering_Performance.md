# Considering Performance

## Build Time

## see also
include-what-you-use


### Forward Declare when Possible

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

This is a proactive approach to simplify compilation time and rebuilding dependencies.

### Firewall Frequently Changing Header Files

### Don't Unnecessarily Include Headers

### Reduce the load on the preprocessor

This is a general form of "Firewall Frequently Changing Header Files" and "Don't Unnecessarily Include Headers." Tools like BOOST_PP can be very helpful, but they also put a huge burden on the preprocessor

### Consider using precompiled headers

### Consider Using Tools

These are not meant to supercede good design

CCACHE, facebook's thing (warp)


## Runtime

### Limit Variable Scope

Variables should be declared as late as possible, and ideally, only when it's possible to initialize the object. Reduced variable scope results in less memory being used, more efficient code in general, and helps the compiler optimize the code further.

```c++
// Good idea
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

### Prefer double to float

### Prefer ++i to i++
... when it is semantically correct. Pre-increment is [faster](http://blog2.emptycrate.com/content/why-i-faster-i-c) then post-increment because it does not require a copy of the object to be made.

```cpp
// Bad Idea
for (int i = 0; i < 15; i++)
{
  std::cout << i << std::endl;
}


// Good Idea
for (int i = 0; i < 15; ++i)
{
  std::cout << i << std::endl;
}

```
