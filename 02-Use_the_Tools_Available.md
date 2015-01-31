# Use The Tools Available

## Source Control

Source control is an absolute necessity for any software development project. If you are not using one yet, start using one.

 * [github](http://github.com) - allows for unlimited public repositories, must pay for a private repository
 * [bitbucket](http://bitbucket.org) - allows for unlimited private repositories with up to 5 collaborators, for free.
 * [sourceforge](http://sf.net) - open source hosting only
 * Subversion, Bitkeeper, many many others... The above are the most popular free services.

## Build Tool

Use an industry standard widely accepted build tool. This prevents you from reinventing the wheel whenever you discover / link to a new library / package your product / etc. Examples include:

 * [cmake](http://cmake.org)
 * [waf](http://waf.googlecode.com)
 * ninja - can greatly improve the incremental build time of your larger projects. Can be used as a target for cmake
 * google's build tool

Remember, it's not just a build tool, it's also a programming language. Try to maintain good clean build scripts and follow the recommended practices for the tool you are using

## Continuous Integration

Once you have picked your build tool, set up a continuous integration environment.

Continuous Integration (CI) tools automatically build the source code as changes are pushed to the repository. These can be hosted privately or with a CI host.

 * [Travis CI](http://travis-ci.org)
   * works well with C++
   * designed for use with github
   * free for public repositories on github
 * Hudson CI
 * [Decent CI](https://github.com/lefticus/decent_ci)
  * simple ad-hoc continuous integration that posts results to github
  * supports Windows, MacOS and Linux
  * used by [ChaiScript](http://chaiscript.com/ChaiScript-BuildResults/full_dashboard.html)

If you have an OpenSource, publicly hosted project on github, go enable travis-ci integration right now. We'll wait for you to come back. For a simple example of how to enable it for your C++ CMake based application, see here: https://github.com/ChaiScript/ChaiScript/blob/master/.travis.yml


## Compilers

Use every available and reasonable set of warning options

You should use as many compilers as you can for your platform(s). Each compiler implements the standard slightly differently and supporting multiple will help ensure the most portable, most reliable code.

### GCC / Clang

`-Wall -Wextra -Wshadow -Wnon-virtual-dtor -pedantic`

 * `-Wall -Wextra` reasonable and standard
 * `-Wshadow` warn the user if a variable declaration shadows one from a parent context
 * `-Wnon-virtual-dtor` warn the user if a class with virtual functions has a non-virtual destructor. This helps catch hard to track down memory errors
 * `-Wold-style-cast` warn for c-style casts
 * `-Wcast-align` warn for potential performance problem casts 
 * `-Wunused` warn on anything being unused
 * `-Woverloaded-virtual` warn if you overload (not override) a virtual function
 * `-pedantic`

`-Weffc++` warning mode can be too noisy, but if it works for your project, use it also.

### MSVC

`/W4 /W44640`

 * `/W4` - All reasonable warnings
 * `/w44640` - Enable warning on thread un-safe static member initialization

Not recommended

 * `/Wall` - Also warns on files included from the standard library, so it's not very useful and creates too many extra warnings. 



### General

Start with very strict warnings settings from the beginning. Trying to raise the warning level after the project is underway can be painful.

Consider using the "treat warnings as errors" setting. `/Wx` with MSVC, `-Werror` with GCC / Clang

## llvm based tools


include-what-you-use   https://github.com/ChaiScript/ChaiScript/commit/c0bf6ee99dac14a19530179874f6c95255fde173

clang-modernize https://github.com/ChaiScript/ChaiScript/commit/6eab8ddfe154a4ebbe956a5165b390ee700fae1b

clang-check
clang-tidy

## Static Analyzers

### cppcheck
Cppcheck is free and opensource. It strives for 0 false positives and does a good job at it. Therefore all warnings should be enabled: `-enable=all`

### Clang's Static Analyzer

Clang's analyzer's default options are good for the respective platform. It can be used directly [from cmake](http://garykramlich.blogspot.com/2011/10/using-scan-build-from-clang-with-cmake.html). They can also be called via clang-check and clang-tidy from the LLVM Based Tools.


### MSVC's Static Analyzer

Can be enabled with the `/analyze` [command line option](http://msdn.microsoft.com/en-us/library/ms173498.aspx). For now we will stick with the default options.

commercial options



## Runtime Checkers

### Code Coverage Analysis

A coverage analysis tool shall be run when tests are executed to make sure the entire application is being tested. Unfortunately, coverage analysis requires that compiler optimizations be disabled. This can result in significantly longer test execution times.

The most likely candidate for a coverage visualization is the [lcov](http://ltp.sourceforge.net/coverage/lcov.php) project. A secondary option is [coveralls](https://coveralls.io/), which is free for open source projects.

<link to chaiscript example of using it>

### GCC/Clang Sanitizers

 * address
 * thread
 * undefined


## Ignoring Warnings

If it is determined by team consensus that the compiler or analyzer is warning on something that is either incorrect or unavoidable, the team will disable the specific error to as localized part of the code as possible.

## Testing

### Unit Tests

Unit tests are for small chunks of code, individual functions which can be tested standalone.

### Integration Tests

There should be a test enabled for every feature or bug fix that is committed. See also "Code Coverage Analysis." These are tests that are higher level than unit tests. They should still be limited in scope to individual features.

### Negative Testing

Don't forget to make sure that your error handling is being tested and works properly as well. This will become obvious if you aim for 100% code coverage.

