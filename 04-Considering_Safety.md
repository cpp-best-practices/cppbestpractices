# Considering Safety


## Const as Much as Possible
`const` tells the compiler that a variable or method is immutable. This helps the compiler optimize the code and helps the developer know if a function has a side effect. Also, using `const &` prevents the compiler from copying data unnecessarily. [Here](http://kotaku.com/454293019) are some comments on `const` from John Carmack.

```cpp
// Bad Idea
class MyClass
{
public:
  MyClass(std::string t_value)
    : m_value(t_value)
  {
  }

  std::string get_value()
  {
    return m_value;
  }

private:
  std::string m_value;
};


// Good Idea
class MyClass
{
public:
  MyClass(const std::string &t_value)
    : m_value(t_value)
  {
  }

  std::string get_value() const
  {
    return m_value;
  }

private:
  std::string m_value;
};
```

### Consider Return By Value for Mutable Data, `const &` for Immutable

You don't want to have to pay a cost for copying the data when you don't need to, but you also don't want to have to safely return data in a threading scenario.

See also this discussion for more information: https://github.com/lefticus/cppbestpractices/issues/21

## Avoid Raw Memory Access

Raw memory access, allocation and deallocation, are difficult to get correct in C++ without [risking memory errors and leaks](http://blog2.emptycrate.com/content/nobody-understands-c-part-6-are-you-still-using-pointers). C++11 provides tools to avoid these problems.

```cpp
// Bad Idea
MyClass *myobj = new MyClass;

// ...
delete myobj;


// Good Idea
auto myobj = std::make_unique<MyClass>(constructor_param1, constructor_param2); // C++14
auto myobj = std::unique_ptr<MyClass>(new MyClass(constructor_param1, constructor_param2)); // C++11
auto mybuffer = std::make_unique<char[]>(length); // C++14
auto mybuffer = std::unique_ptr<char[]>(new char[length]); // C++11

// or for reference counted objects
auto myobj = std::make_shared<MyClass>(); 

// ...
// myobj is automatically freed for you whenever it is no longer used.
```

## Use `std::array` or `std::vector` Instead of C-style Arrays

Both of these guarantee contiguous memory layout of objects and can (and should) completely replace your usage of C-style arrays for many of the reasons listed for not using bare pointers.

Also, [avoid](http://stackoverflow.com/questions/3266443/can-you-use-a-shared-ptr-for-raii-of-c-style-arrays) using `std::shared_ptr` to hold an array. 

## Use Exceptions

Exceptions cannot be ignored. Return values, such as using `boost::optional`, can be ignored and if not checked can cause crashes or memory errors. An exception, on the other hand, can be caught and handled. Potentially all the way up the highest level of the application with a log and automatic restart of the application.

Stroustrup, the original designer of C++, [makes this point](http://www.stroustrup.com/bs_faq2.html#exceptions-why) much better than I ever could.

## Use C++-style cast instead of C-style cast
Use the C++-style cast (static\_cast<>, dynamic\_cast<> ...) instead of the C-style cast. The C++-style cast allows more compiler checks and is considerable safer.

```cpp
// Bad Idea
double x = getX();
int i = (int) x;

// Good Idea
int i = static_cast<int>(x);
```
Additionally the C++ cast style is more visible and has the possibility to search for.

## Additional Resources

[How to Prevent The Next Heartbleed](http://www.dwheeler.com/essays/heartbleed.html) by David Wheeler is a good analysis of the current state of code safety and how to ensure safe code.
