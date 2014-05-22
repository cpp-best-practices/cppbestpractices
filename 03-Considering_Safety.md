# Considering Safety


## Const as much as possible
`const` tells the compiler that a variable or method is immutable. This helps the compiler optimize the code and helps the developer know if a function side effects. Also, using `const &` prevents the compiler from copying data unnecessarily. [Here](http://kotaku.com/454293019) are some comments on const from John Carmack.

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
}


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
}
```

## Avoid raw memory access

Raw memory access, allocation and deallocation, are difficult to get correct in C++ without [risking memory errors and leaks](http://blog2.emptycrate.com/content/nobody-understands-c-part-6-are-you-still-using-pointers). C++11 provides tools to avoid these problems.

```cpp
// Bad Idea
MyClass *myobj = new MyClass;

// ...
delete myobj;


// Good Idea
std::shared_ptr<MyClass> myobj = make_shared<MyClass>();
// ... 
// myobj is automatically freed for you whenever it is no longer used.
```

## Use Exceptions

Exceptions cannot be ignored. Return values, such as using `boost::optional`, can be ignored and if not checked can cause crashes or memory errors. An exception, on the other hand, can be caught and handled. Potentially all the way up the highest level of the application with a log and automatic restart of the application.

Stroustrup, the original designer of C++, [makes this point](http://www.stroustrup.com/bs_faq2.html#exceptions-why) much better than I ever could.

