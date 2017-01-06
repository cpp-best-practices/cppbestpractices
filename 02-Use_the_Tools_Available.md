# Use The Tools Available

An automated framework for executing these tools should be established very early in the development process. It should not take more than 2-3 commands to checkout the source code, build, and execute the tests. Once the tests are done executing, you should have an almost complete picture of the state and quality of the code.

## Source Control

Source control is an absolute necessity for any software development project. If you are not using one yet, start using one.

 * [GitHub](https://github.com/) - allows for unlimited public repositories, must pay for a private repository.
 * [Bitbucket](https://bitbucket.org/) - allows for unlimited private repositories with up to 5 collaborators, for free.
 * [SourceForge](http://sourceforge.net/) - open source hosting only.
 * [GitLab](https://gitlab.com/) - allows for unlimited public and private repositories, unlimited CI Runners included, for free.
 * [Visual Studio Online](https://visualstudio.com) (http://www.visualstudio.com/what-is-visual-studio-online-vs) - allows for unlimited public repositories, must pay for private repository. Repositories can be git or TFVC. Additionally: Issue tracking, project planning (multiple Agile templates, such as SCRUM), integrated hosted builds, integration of all this into Microsoft Visual Studio. Windows only.

## Build Tool

Use an industry standard widely accepted build tool. This prevents you from reinventing the wheel whenever you discover / link to a new library / package your product / etc. Examples include:

 * [CMake](http://www.cmake.org/)
   * Consider: https://github.com/sakra/cotire/ for build performance
   * Consider: https://github.com/toeb/cmakepp for enhanced usability
 * [Conan](https://www.conan.io/) - a crossplatform dependency manager for C++
 * [C++ Archive Network (CPPAN)](https://cppan.org/) - a crossplatform dependency manager for C++
 * [Waf](https://waf.io/)
 * [FASTBuild](http://www.fastbuild.org/)
 * [Ninja](https://ninja-build.org/) - can greatly improve the incremental build time of your larger projects. Can be used as a target for CMake.
 * [Bazel](http://bazel.io/) - Note: MacOS and Linux only.
 * [gyp](https://chromium.googlesource.com/external/gyp/) - Google's build tool for chromium.
 * [maiken](https://github.com/Dekken/maiken) - Crossplatform build tool with Maven-esque configuration style.
 * [Qt Build Suite](http://doc.qt.io/qbs/) - Crossplatform build tool From Qt.
 * [meson](http://mesonbuild.com/index.html) - Open source build system meant to be both extremely fast, and, even more importantly, as user friendly as possible.
 * [premake](https://premake.github.io/) 

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
   * free for public repositories on GitHub
 * [Hudson CI](http://hudson-ci.org/) / [Jenkins CI](https://jenkins-ci.org/)
   * Java Application Server is required
   * supports Windows, OS X, and Linux
   * extendable with a lot of plugins
 * [TeamCity](https://www.jetbrains.com/teamcity) 
   * has a free option for open source projects
 * [Decent CI](https://github.com/lefticus/decent_ci)
   * simple ad-hoc continuous integration that posts results to GitHub
   * supports Windows, OS X, and Linux
   * used by [ChaiScript](http://chaiscript.com/ChaiScript-BuildResults/full_dashboard.html)
 * [Visual Studio Online](https://visualstudio.com) (http://www.visualstudio.com/what-is-visual-studio-online-vs)
   * Tightly integrated with the source repositories from Visual Studio Online
   * Uses MSBuild (Visual Studio's build engine), which is available on Windows, OS X and Linux
   * Provides hosted build agents and also allows for user-provided build agents
   * Can be controlled and monitored from within Microsoft Visual Studio
   * On-Premise installation via Microsoft Team Foundation Server

If you have an open source, publicly-hosted project on GitHub:

 * go enable Travis Ci and AppVeyor integration right now. We'll wait for you to come back. For a simple example of how to enable it for your C++ CMake-based application, see here: https://github.com/ChaiScript/ChaiScript/blob/master/.travis.yml
 * enable one of the coverage tools listed below (Codecov or Coveralls)
 * enable [Coverity Scan](https://scan.coverity.com)
  
These tools are all free and relatively easy to set up. Once they are set up you are getting continuous building, testing, analysis and reporting of your project. For free.


## Compilers

Use every available and reasonable set of warning options. Some warning options only work with optimizations enabled, or work better the higher the chosen level of optimization is, for example [`-Wnull-dereference`](https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wnull-dereference-367) with GCC.

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
 * `-Wconversion` warn on type conversions that may lose data
 * `-Wsign-conversion` warn on sign conversions
 * `-Wmisleading-indentation` warn if identation implies blocks where blocks do not exist

Consider using `-Weverything` and disabling the few warnings you need to on Clang


`-Weffc++` warning mode can be too noisy, but if it works for your project, use it also.

### MSVC

`/W4 /W44640` - use these and consider the following

 * `/W4` All reasonable warnings
 * `/w14242` 'identfier': conversion from 'type1' to 'type1', possible loss of data
 * `/w14254` 'operator': conversion from 'type1:field_bits' to 'type2:field_bits', possible loss of data
 * `/w14263` 'function': member function does not override any base class virtual member function
 * `/w14265` 'classname': class has virtual functions, but destructor is not virtual instances of this class may not be destructed correctly
 * `/w14287` 'operator': unsigned/negative constant mismatch
 * `/we4289` nonstandard extension used: 'variable': loop control variable declared in the for-loop is used outside the for-loop scope
 * `/w14296` 'operator': expression is always 'boolean_value'
 * `/w14311` 'variable': pointer truncation from 'type1' to 'type2'
 * `/w14545` expression before comma evaluates to a function which is missing an argument list
 * `/w14546` function call before comma missing argument list
 * `/w14547` 'operator': operator before comma has no effect; expected operator with side-effect
 * `/w14549` 'operator': operator before comma has no effect; did you intend 'operator'?
 * `/w14555` expression has no effect; expected expression with side-effect
 * `/w14619` pragma warning: there is no warning number 'number'
 * `/w14640` Enable warning on thread un-safe static member initialization
 * `/w14826` Conversion from 'type1' to 'type_2' is sign-extended. This may cause unexpected runtime behavior.
 * `/w14905` wide string literal cast to 'LPSTR'
 * `/w14906` string literal cast to 'LPWSTR'
 * `/w14928` illegal copy-initialization; more than one user-defined conversion has been implicitly applied

Not recommended

 * `/Wall` - Also warns on files included from the standard library, so it's not very useful and creates too many extra warnings.



### General

Start with very strict warning settings from the beginning. Trying to raise the warning level after the project is underway can be painful.

Consider using the *treat warnings as errors* setting. `/Wx` with MSVC, `-Werror` with GCC / Clang

## LLVM-based tools

 * [include-what-you-use](https://github.com/include-what-you-use), [example results](https://github.com/ChaiScript/ChaiScript/commit/c0bf6ee99dac14a19530179874f6c95255fde173)
 * [clang-modernize](http://clang.llvm.org/extra/clang-modernize.html), [example results](https://github.com/ChaiScript/ChaiScript/commit/6eab8ddfe154a4ebbe956a5165b390ee700fae1b)
 * [clang-check](http://clang.llvm.org/docs/ClangCheck.html)
 * [clang-tidy](http://clang.llvm.org/extra/clang-tidy.html)

## Static Analyzers

The best bet is the static analyzer that you can run as part of your automated build system. Cppcheck and clang meet that requirement for free options.

### Coverity Scan 

[Coverity](https://scan.coverity.com/) has a free (for open source) static analysis toolkit that can work on every commit in integration with [Travis CI](http://travis-ci.org) and [AppVeyor](http://www.appveyor.com/).

### PVS-Studio

[PVS-Studio](http://www.viva64.com/en/pvs-studio/) is a tool for bug detection in the source code of programs, written in C, C++ and C#. It is free for personal academic projects, open source non-commercial projects and independent projects of individual developers. It works in Windows and Linux environment.

### Cppcheck
[Cppcheck](http://cppcheck.sourceforge.net/) is free and open source. It strives for 0 false positives and does a good job at it. Therefore all warnings should be enabled: `--enable=all`

### Clang's Static Analyzer

Clang's analyzer's default options are good for the respective platform. It can be used directly [from CMake](http://garykramlich.blogspot.com/2011/10/using-scan-build-from-clang-with-cmake.html). They can also be called via clang-check and clang-tidy from the [LLVM-based Tools](#llvm-based-tools).

### MSVC's Static Analyzer

Can be enabled with the `/analyze` [command line option](http://msdn.microsoft.com/en-us/library/ms173498.aspx). For now we will stick with the default options.

### Flint / Flint++

[Flint](https://github.com/facebook/flint) and [Flint++](https://github.com/L2Program/FlintPlusPlus) are linters that analyze C++ code against Facebook's coding standards.

### ReSharper C++ / CLion

Both of these tools from [JetBrains](https://www.jetbrains.com/cpp/) offer some level of static analysis and automated fixes for common things that can be done better. They have options available for free licenses for open source project leaders.

### Cevelop 

The Eclipse based [Cevelop](https://www.cevelop.com/) IDE has various static analysis and refactoring / code fix tools available. For example, you can replace macros with C++ `constexprs`, refactor namespaces (extract/inline `using`, qualify name), and refactor your code to C++11's uniform initialization syntax. Cevelop is free to use.

### Qt Creator

Qt Creator can plug into the clang static analyzer.

## Runtime Checkers

### Code Coverage Analysis

A coverage analysis tool shall be run when tests are executed to make sure the entire application is being tested. Unfortunately, coverage analysis requires that compiler optimizations be disabled. This can result in significantly longer test execution times.

 * [Codecov](https://codecov.io/)
   * integrates with Travis CI and AppVeyor
   * free for open source projects
 * [Coveralls](https://coveralls.io/)
   * integrates with Travis CI and AppVeyor
   * free for open source projects
 * [LCOV](http://ltp.sourceforge.net/coverage/lcov.php)
   * very configurable 
 * [Gcovr](http://gcovr.com/)
 * [kcov](http://simonkagstrom.github.io/kcov/index.html)
   * integrates with codecov and coveralls
   * performs code coverage reporting without needing special compiler flags, just by instrumenting debug symbols.


### Valgrind

[Valgrind](http://www.valgrind.org/) is a runtime code analyzer that can detect memory leaks, race conditions, and other associated problems. It is supported on various Unix platforms.

### Dr Memory

Similar to Valgrind. http://www.drmemory.org

### GCC / Clang Sanitizers

These tools provide many of the same features as Valgrind, but built into the compiler. They are easy to use and provide a report of what went wrong.

 * AddressSanitizer
 * MemorySanitizer
 * ThreadSanitizer
 * UndefinedBehaviorSanitizer

### Fuzzy Analyzers

If your project accepts user defined input, considering running a fuzzy input tester. 

Both of these tools use coverage reporting to find new code execution paths and try to breed novel inputs for your code. They can find crashes, hangs, and inputs you didn't know were considered valid.

 * [american fuzzy lop](http://lcamtuf.coredump.cx/afl/)
 * [LibFuzzer](http://llvm.org/docs/LibFuzzer.html)
 * [KLEE](http://klee.github.io/) - Can be used to fuzz individual functions

## Ignoring Warnings

If it is determined by team consensus that the compiler or analyzer is warning on something that is either incorrect or unavoidable, the team will disable the specific error to as localized part of the code as possible.

## Testing

CMake, mentioned above, has a built in framework for executing tests. Make sure whatever build system you use has a way to execute tests built in.

To further aid in executing tests, consider a library such as [Google Test](https://github.com/google/googletest), [Catch](https://github.com/philsquared/Catch), [CppUTest](https://github.com/cpputest/cpputest) or [Boost.Test](http://www.boost.org/doc/libs/release/libs/test/) to help you organize the tests.

### Unit Tests

Unit tests are for small chunks of code, individual functions which can be tested standalone.

### Integration Tests

There should be a test enabled for every feature or bug fix that is committed. See also [Code Coverage Analysis](#code-coverage-analysis). These are tests that are higher level than unit tests. They should still be limited in scope to individual features.

### Negative Testing

Don't forget to make sure that your error handling is being tested and works properly as well. This will become obvious if you aim for 100% code coverage.

## Debugging

### uftrace

[uftrace](https://github.com/namhyung/uftrace) can be used to generating function call graphs of a program execution

### rr

[rr](http://rr-project.org/) is a free (open source) reverse debugger that supports C++.

## Other Tools

### Metrix++

[Metrix++](http://metrixplusplus.sourceforge.net/) can identify and report on the most complex sections of your code. Reducing complex code helps you and the compiler understand it better and optimize it better.

### ABI Compliance Checker

[ABI Compliance Checker](http://ispras.linuxbase.org/index.php/ABI_compliance_checker) (ACC) can analyze two library versions and generates a detailed compatibility report regarding API and C++ ABI changes. This can help a library developer spot unintentional breaking changes to ensure backward compatibility.

### CNCC 

[Customizable Naming Convention Checker](https://github.com/mapbox/cncc) can report on identifiers in your code that do not follow certain naming conventions.

### ClangFormat

[ClangFormat](http://clang.llvm.org/docs/ClangFormat.html) can check and correct code formatting to match organizational conventions automatically.

### SourceMeter 

[SourceMeter](https://www.sourcemeter.com/) offers a free version which provides many different metrics for your code and can also call into cppcheck.
