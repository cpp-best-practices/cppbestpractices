# Considering Maintainability


## Avoid Compiler Macros

Compiler definitions and macros are replaced by the preprocessor before the compiler is ever run. This can make debugging very difficult because the debugger doesn't know where the source came from.

```cpp
// Bad Idea
#define PI 3.14159;

// Good Idea
namespace my_project {
  class Constants {
  public:
    // if the above macro would be expanded, then the following line would be:
    //   static const double 3.14159 = 3.14159;
    // which leads to a compile-time error. Sometimes such errors are hard to understand.
    static constexpr double PI = 3.14159;
  };
}
```

## Consider Avoiding Boolean Parameters

They do not provide any additional meaning while reading the code. You can either create a separate function that has a more meaningful name, or pass an enumeration that makes the meaning more clear.

See http://mortoray.com/2015/06/15/get-rid-of-those-boolean-function-parameters/ for more information.

## Avoid Raw Loops

Know and understand the existing C++ standard algorithms and put them to use.

 * See [cppreference](https://en.cppreference.com/w/cpp/algorithm)
 * Watch [C++ Seasoning](https://www.youtube.com/watch?v=qH6sSOr-yk8)
 
Consider a call to `[]` as a potential code smell, indicating that an algorithm was not used where it could have been.


## Never Use `assert` With Side Effects

```cpp
// Bad Idea
assert(set_value(something));

// Better Idea
[[maybe_unused]] const auto success = set_value(something);
assert(success);
```

The `assert()` will be removed in release builds which will prevent the `set_value` call from ever happening.

So while the second version is uglier, the first version is simply not correct.


## Properly Utilize 'override' and 'final'

These keywords make it clear to other developers how virtual functions are being utilized, can catch potential errors if the signature of a virtual function changes, and can possibly [hint to the compiler](http://stackoverflow.com/questions/7538820/how-does-the-compiler-benefit-from-cs-new-final-keyword) of optimizations that can be performed.
