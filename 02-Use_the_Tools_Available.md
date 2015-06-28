# Use The Tools Available

An automated framework for executing these tools should be established very early in the development process. It should not take more than 2-3 commands to checkout the source code, build, and execute the tests. Once the tests are done executing, you should have an almost complete picture of the state and quality of the code.

## Source Control

Source control is an absolute necessity for any software development project. If you are not using one yet, start using one.

 * [GitHub](https://github.com/) - allows for unlimited public repositories, must pay for a private repository.
 * [Bitbucket](https://bitbucket.org/) - allows for unlimited private repositories with up to 5 collaborators, for free.
 * [SourceForge](http://sourceforge.net/) - open source hosting only.
 * [GitLab](https://gitlab.com/), Subversion, BitKeeper, many many others... The above are the most popular free services.

## Build Tool

Use an industry standard widely accepted build tool. This prevents you from reinventing the wheel whenever you discover / link to a new library / package your product / etc. Examples include:

 * [CMake](http://www.cmake.org/)
 * [Biicode](https://www.biicode.com/)
 * [Waf](https://waf.io/)
 * [FASTBuild](http://www.fastbuild.org/)
 * [Ninja](https://martine.github.io/ninja/) - can greatly improve the incremental build time of your larger projects. Can be used as a target for CMake.
 * [Bazel](http://bazel.io/) - Note: MacOS and Linux only.

Remember, it's not just a build tool, it's also a programming language. Try to maintain good clean build scripts and follow the recommended practices for the tool you are using.

## Continuous Integration

Once you have picked your build tool, set up a continuous integration environment.

Continuous Integration (CI) tools automatically build the source code as changes are pushed to the repository. These can be hosted privately or with a CI host.

 * [Travis CI](http://travis-ci.org)
   * works well with C++
   * designed for use with GitHub
   * free for public repositories on GitHub
 * [AppVeyor](http://www.appveyor.com/)
   * supports Windows, MSVC and MinGW
   * free for public repositoris on GitHub
 * [Hudson CI](http://hudson-ci.org/)
 * [TeamCity](https://www.jetbrains.com/teamcity) 
   * has a free option for open source projects
 * [Decent CI](https://github.com/lefticus/decent_ci)
   * simple ad-hoc continuous integration that posts results to GitHub
   * supports Windows, OS X, and Linux
   * used by [ChaiScript](http://chaiscript.com/ChaiScript-BuildResults/full_dashboard.html)

If you have an open source, publicly-hosted project on GitHub:

 * go enable travis-ci and AppVeyor integration right now. We'll wait for you to come back. For a simple example of how to enable it for your C++ CMake-based application, see here: https://github.com/ChaiScript/ChaiScript/blob/master/.travis.yml
 * enable one of the coverage tools listed below (Codecov or Coveralls)
 * enable [Coverity Scan](https://scan.coverity.com)
  
These tools are all free and relatively easy to set up. Once they are set up you are getting continuous building, testing, analysis and reporting of your project. For free.


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

Start with very strict warning settings from the beginning. Trying to raise the warning level after the project is underway can be painful.

Consider using the *treat warnings as errors* setting. `/Wx` with MSVC, `-Werror` with GCC / Clang

## LLVM-based tools

 * [include-what-you-use](https://code.google.com/p/include-what-you-use/), [example results](https://github.com/ChaiScript/ChaiScript/commit/c0bf6ee99dac14a19530179874f6c95255fde173)
 * [clang-modernize](http://clang.llvm.org/extra/clang-modernize.html), [example results](https://github.com/ChaiScript/ChaiScript/commit/6eab8ddfe154a4ebbe956a5165b390ee700fae1b)
 * [clang-check](http://clang.llvm.org/docs/ClangCheck.html)
 * [clang-tidy](http://clang.llvm.org/extra/clang-tidy.html)

## Static Analyzers

The best bet is the static analyzer that you can run as part of your automated build system. Cppcheck and clang meet that requirement for free options.

### Coverity Scan 

Coverity has a free (for open source) static analysis toolkit that can work on every commit in integration with [Travis CI](http://travis-ci.org) and [AppVeyor](http://www.appveyor.com/).

### Cppcheck
Cppcheck is free and open source. It strives for 0 false positives and does a good job at it. Therefore all warnings should be enabled: `-enable=all`

### Clang's Static Analyzer

Clang's analyzer's default options are good for the respective platform. It can be used directly [from CMake](http://garykramlich.blogspot.com/2011/10/using-scan-build-from-clang-with-cmake.html). They can also be called via clang-check and clang-tidy from the [LLVM-based Tools](#llvm-based-tools).

### MSVC's Static Analyzer

Can be enabled with the `/analyze` [command line option](http://msdn.microsoft.com/en-us/library/ms173498.aspx). For now we will stick with the default options.

### ReSharper C++ / CLion

Both of these tools from [JetBrains](https://www.jetbrains.com/cpp/) offer some level of static analysis and automated fixes for common things that can be done better. They have options available for free licenses for open source project leaders.

### Qt Creator

Qt Creator can plug into the clang static analyzer, but *only* on the *commercial* version of Qt Creator.

### Metrix++

http://metrixplusplus.sourceforge.net/

While not necessarily a static analyzer, Metrix++ can identify and report on the most complex sections of your code. Reducing complex code helps you and the compiler understand it better and optimize it better.

## Runtime Checkers

### Code Coverage Analysis

A coverage analysis tool shall be run when tests are executed to make sure the entire application is being tested. Unfortunately, coverage analysis requires that compiler optimizations be disabled. This can result in significantly longer test execution times.

 * [Codecov](https://codecov.io/)
   * integrates with Travis CI and Appveyor
   * free for open source projects
 * [Coveralls](https://coveralls.io/)
   * integrates with Travis CI and Appveyor
   * free for open source projects
 * [LCOV](http://ltp.sourceforge.net/coverage/lcov.php)
   * very configurable 
 * [Gcovr](http://gcovr.com/)


### Valgrind

Runtime code analyzer that can detect memory leaks, race conditions, and other associated problems. It is supported on various Unix platforms.

### GCC / Clang Sanitizers

These tools provide many of the same features as Valgrind, but built into the compiler. They are easy to use and provide a report of what went wrong.

 * AddressSanitizer
 * ThreadSanitizer
 * UndefinedBehaviorSanitizer

### Fuzzy Analyzers

If you project accepts user defined input, considering running a fuzzy input tester. 

Both of these tools use coverage reporting to find new code executation paths and try to breed novel inputs for your code. They can find crashes, hangs, and inputs you didn't know were considered valid.

 * [american fuzzy lop](http://lcamtuf.coredump.cx/afl/)
 * [LibFuzzer](http://llvm.org/docs/LibFuzzer.html)

## Ignoring Warnings

If it is determined by team consensus that the compiler or analyzer is warning on something that is either incorrect or unavoidable, the team will disable the specific error to as localized part of the code as possible.

## Testing

CMake, mentioned above, has a built in framework for executing tests. Make sure whatever build system you use has a way to execute tests built in.

To further aid in executing tests, consider a library such as [Google Test](https://code.google.com/p/googletest/) or [Catch](https://github.com/philsquared/Catch) or [Boost.Test](http://www.boost.org/doc/libs/1_55_0/libs/test/doc/html/index.html) to help you organize the tests.

### Unit Tests

Unit tests are for small chunks of code, individual functions which can be tested standalone.

### Integration Tests

There should be a test enabled for every feature or bug fix that is committed. See also [Code Coverage Analysis](#code-coverage-analysis). These are tests that are higher level than unit tests. They should still be limited in scope to individual features.

### Negative Testing

Don't forget to make sure that your error handling is being tested and works properly as well. This will become obvious if you aim for 100% code coverage.

