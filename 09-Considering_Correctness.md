# Considering Correctness

## Avoid Typeless Interfaces


Bad Idea:

```cpp
std::string find_file(const std::string &base, const std::string &pattern);
```

Better Idea:

```cpp
std::filesystem::path find_file(const std::filesystem::path &base, const std::regex &pattern);
```

The above is better but still suffers from having implicit conversions from `std::string` to `std::filesystem::path` and back.

Consider using a typesafe library like

 * https://foonathan.net/type_safe/
 * https://github.com/rollbear/strong_type
 * https://github.com/joboccara/NamedType

Note that stronger typing can also allow for more compiler optimizations.

* [Sorting in C vs C++](Sorting in C vs C++.pdf)


