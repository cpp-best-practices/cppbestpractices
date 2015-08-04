# Considering Threadability

## Avoid Global Data

Global data leads to unintended side effects between functions and can make code difficult or impossible to parallelize. Even if the code is not intended today for parallelization, there is no reason to make it impossible for the future.

### Statics

Besides being global data, statics are not always constructed and deconstructed as you would expect. This is particularly true in cross-platform environments. See for example, [this g++ bug](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=66830) regarding the order of destruction of shared static data loaded from dynamic modules.

### Shared Pointers

`std::shared_ptr` is "as good as a global" (http://stackoverflow.com/a/18803611/29975) because it allows multiple pieces of code to interact with the same data.

### Singletons

A singleton is often implemented with a static and/or `shared_ptr`.

## Avoid Heap Operations

Much slower in threaded environments. In many or maybe even most cases, copying data is faster. Plus with move operations and such and things.

## Mutex and mutable go together (M&M rule, C++11)
For member variables it is good practice to use mutex and mutable together. This applies in both ways:
* A mutable member variable is presumed to be a shared variable so it should be synchronized with a mutex (or made atomic)
* If a member variable is itself a mutex, it should be mutable. This is required to use it inside a const member function.

For more information see the following article from Herb Sutter: http://herbsutter.com/2013/05/24/gotw-6a-const-correctness-part-1-3/

See also [related safety discussion](04-Considering_Safety.md#consider-return-by-value-for-mutable-data-const--for-immutable) about `const &` return values
