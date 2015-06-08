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
    static const double PI = 3.14159;
  }
}
```



## Avoid Raw Loops

Know and understand the existing C++ standard algorithms and put them to use. See [C++ Seasoning](https://www.youtube.com/watch?v=qH6sSOr-yk8) for more details. 
