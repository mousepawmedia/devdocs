..  _buildc:

C/C++ Build Systems
###################################

We have developed a hybrid build system for all our C and C++ projects, which
simplifies the build process without sacrificing portability or control. It
consists of several pieces:

* **Clang** is our primary compiler, which we use because of its superior
  feature set and portability. However, we also support **GCC**, which is the
  standard on Linux.

* **CMake** is used for the heavy lifting. Our projects are configured to take
  full advantage of CMake's features, meaning that users familiar with the
  system can do custom builds.

* **Makefiles** are used to automate the most common build processes on UNIX
  systems. This is useful because, while CMake is powerful, using it is a
  fairly involved process. Since many users may not know CMake that well,
  Makefiles serve as a beginner-friendly wrapper, providing one-command builds.

..  warning:: We do *not* support the Microsoft Visual C++ [MSVC] compiler,
    and have **no plans** to officially support it in the future. LLVM Clang is
    officially supported by Microsoft to compile software on Windows.

To best understand this, we will start from the bottom of the stack and work
our way up the layers of abstraction.

..  _buildc_concepts:

Important Concepts
=====================================

..  _buildc_concepts_types:

Types of Builds
-------------------------------------

In C and C++, we can compile into one of three forms. The most obvious is
an **executable**, like an application or a command-line program, which runs
directly on a machine. The second is a **statically-linked library**, and the
third a **dynamically-linked library**, both of which allow us to provide
common code to other projects. We'll discuss the differences between
the two in the next section.

We also traditionally have two types of builds: Debug and Release.
The former, **Debug**, is intended for testing the end result and debugging
problems. It is typically larger and slower than the other type of build.
**Release** is intended for distribution and use by the end-user. It is
usually compiled to be faster and smaller than Debug, but it is ill-suited
to debugging.

..  warning:: As a rule, you should always do performance benchmarking on
    the *Release* build!

..  _buildc_concepts_libraries:

Understanding Libraries
-------------------------------------

Before we can begin to grasp the intricacies of building C and C++ projects,
we must first understand the two kinds of dependencies: **statically-linked**
and **dynamically-linked** libraries.

Most simply, a **library** is a simple way of sharing code between multiple
projects. Before the library's code can be used by the project, the two must
be **linked**.

A **statically-linked** library is one that is linked at **compile-time**,
effectively baking the library's code directly into the project's final
compiled code. The advantage is that the end-user does not need a copy of the
library on their system to run the compiled application. However, the
disadvantage is that the compiled application will be larger - it literally
*contains* all the parts it uses of the libraries that were statically
linked to it.

Additionally, once you've compiled the application, it is locked into the
version of the library to which it is statically linked. The only way to
link to a newer version of the library is to recompile the application. This
is both an advantage and a disadvantage. On the upside, the user will never
discover that the application has mysteriously stopped working because they
have the wrong version of the library. On the downside, the user must install
a new version of your application to get any bug fixes and improvements
made to the library it is linked to.

By contrast, a **dynamically-linked** library is linked at **runtime**.
This reduces the size of the program, because every user must have the library
installed on their system. This also means that multiple applications can share
one copy of the library. However, the downside is fairly infamous: if the
user does not have the right version of the library installed, the application
may fail to run.

Statically-linked libraries and dynamically-linked libraries compile to
different forms. To link to a statically-linked library, you must have both
the **headers** (the actual `*.h` and `*.hpp` files) and the compiled library
itself. On UNIX systems, statically-linked libraries are compiled to `*.a`
files; on Windows, they are `*.lib` files.

Dynamically-linked libraries compile to a single file. On UNIX, this is a
`*.so` file; on Windows, it's a `*.dll`; on Mac OSX, it's a `*.dylib`.

That last file extension may have just made your blood run cold as you remember
some "DLL error" on Windows. What literally happened was that a program was
dependent on a dynamically-linked library, and usually a specific version
besides, and couldn't find it.

This is where the trade-off must be considered. For every dependency, we
can either **increase the compiled program size** (static linking) or
**increase the likelihood of user-end errors** (dynamic linking). Some
problems with DLLs on Windows can be mitigated by shipping the right versions
of the libraries with the application, and placing them all in the install
directory. However, on Linux, the analogous `.so` files are usually shared
between programs, so ensuring a consistent version can be far more difficult.

..  _buildc_repo:

Structure of the Repository
=====================================

Every C/C++ project repository follows the same structure.

..  note:: Folders marked with (*) are untracked in the Git repository

..  _buildc_repo_libs:

Library Projects
-------------------------------------

Library projects have the following structure::

    Repository
    ├── docs ← Sphinx documentation.
    │   ├── build (*) ← The compiled documentation.
    │   ├── source ← The documentation source files.
    │   │   └── _themes ← The Sphinx theming files.
    │   └── Makefile ← The Makefile that automatically runs CMake.
    ├── library (*) ← Where 'make ready' puts the compiled library and its headers.
    ├── library-source ← The library source code.
    │   ├── build_temp (*) ← Temporary build stuff. Also where CMake is run from.
    │   ├── include
    │   │   └── library ← The library's header files (.hpp).
    │   ├── lib (*) ← The compiled library (copied from here to ../library)
    │   ├── obj (*) ← Temporary build stuff.
    │   ├── src ← The library's implementation files (.cpp).
    │   ├── CMakeLists.txt ← The CMake build instructions for the library.
    │   └── Makefile ← The Makefile that automatically runs CMake.
    ├── library-tester ← The library tester.
    │   ├── bin (*) ← The compiled tester.
    │   ├── build_temp (*) ← Temporary build stuff. Also where CMake is run from.
    │   ├── include ← The tester's header files (.hpp).
    │   ├── src ← The tester's implementation files (.cpp).
    │   ├── CMakeLists.txt ← The CMake build instructions for the tester.
    │   └── Makefile ← The Makefile that automatically runs CMake.
    ├── .arcconfig ← Configuration for Phabricator Arcanist.
    ├── .arclint ← Configuration for Arcanist linters.
    ├── .gitignore ← Untracks temporary build stuff and other cruft.
    ├── build.config.txt ← The template configuration file.
    ├── BUILDING.md ← User instructions for building.
    ├── CHANGELOG.md ← The list of versions and their changes.
    ├── default.config ← The default configuration file.
    ├── LICENSE.md ← The project's license.
    ├── Makefile ← The project's master Makefile.
    └── README.md ← The README file.

Let's break that down into more detail.

The source code for the library is placed in :file:`library-source`, with
the library name being substituted in (e.g. `pawlib-source`). Within
this directory, we place header files (`*.h` and `*.hpp`) inside of the
:file:`include/library` subdirectory. This ensures that all
:code:`#include` statements clearly indicate the source of the header file,
and to avoid name collisions. The implementation files (`*.c` and `*.cpp`)
are placed in the :file:`src` subdirectory.

Because we cannot execute libraries directly, each library project must have
a **tester** application. This belongs in the :file:`library-tester` folder,
with the library name being substituted in (e.g. `pawlib-tester`). This is
laid out in a similar manner to the :file:`library-source`, with two exceptions.
First, we do not need a subfolder in :file:`include`, as the executable's
header files won't be accessed outside of the tester's source code. Second,
:file:`main.cpp` goes directly in the :file:`library-tester` folder, for ease
of compiling.

You will notice that both :file:`library-source` and :file:`library-tester`
have their own :file:`Makefile` and :file:`CMakeLists.txt` files.
We'll be discussing these files later.

According to company policy, all projects *must* have Sphinx documentation.
This is placed in the :file:`docs` directory of the repository. Sphinx
automatically provides a :file:`Makefile` for compiling documentation.
The documentation source files go in the :file:`source` subfolder.

At the root level of the repository, we have our README files -
:file:`README.md`, :file:`LICENSE.md`, :file:`BUILDING.md`, and
:file:`CHANGELOG.md` - which are written in Markdown language for the best
compatibility with various repository management interfaces. We also have a
root-level :file:`Makefile`, and some configuration files
(:file:`default.config` and :file:`build.config.txt`). We'll discuss these last
three files later.

Finally, take note of the files :file:`.gitignore`, :file:`.arclint`, and
:file:`.arcconfig`. We'll discuss these, and several other hidden files,
in :ref:`rmdrepos`.

..  _buildc_repo_execs:

Executable Projects
-------------------------------------

Executable projects, such as applications, have a very similar structure
to that of library projects::

    Repository
    ├── docs ← Sphinx documentation.
    │   ├── build (*) ← The compiled documentation.
    │   ├── source ← The documentation source files.
    │   │   └── _themes ← The Sphinx theming files.
    │   └── Makefile ← The Makefile that automatically runs CMake.
    ├── project (*) ← Where 'make ready' puts the compiled project.
    ├── project-source ← The project source code.
    │   ├── bin (*) ← The compiled project (copied from here to ../project)
    │   ├── build_temp (*) ← Temporary build stuff. Also where CMake is run from.
    │   ├── include ← The project's header files (.hpp).
    │   ├── obj (*) ← Temporary build stuff.
    │   ├── src ← The project's implementation files (.cpp).
    │   ├── CMakeLists.txt ← The CMake build instructions for the project.
    │   └── Makefile ← The Makefile that automatically runs CMake.
    ├── .arcconfig ← Configuration for Phabricator Arcanist.
    ├── .arclint ← Configuration for Arcanist linters.
    ├── .gitignore ← Untracks temporary build stuff and other cruft.
    ├── build.config.txt ← The template configuration file.
    ├── BUILDING.md ← User instructions for building.
    ├── CHANGELOG.md ← The list of versions and their changes.
    ├── default.config ← The default configuration file.
    ├── LICENSE.md ← The project's license.
    ├── Makefile ← The project's master Makefile.
    └── README.md ← The README file.

Let's explore the differences.

The source code for the project belongs in :file:`project-source`. Header
files (`*.h` and `*.hpp`) go in the :file:`include` subfolder, but unlike
library source headers, they do not need to be placed in a second subfolder.
This is because the project headers won't be used outside of this code base.
If we encounter the following code...

..  code-block:: c++

    #include <string>
    #include "pawlib/stdutils.hpp"
    #include "magicclass.hpp"

...we can clearly see the difference between the three sources.
All `#include <whatever>` headers originate from the standard library,
while all `#include "library/whatever.hpp"` headers originate from linked
libraries. Since `#include "whatever.hpp"` is clearly neither, it must be
a local header belonging to the project.

..  _buildc_compiler:

The Compiler Toolchain
=====================================

When we **compile**, we are turning the source code into an executable
(or a compiled library, as the case may be). In fact, what we often call the
"compiler" is really the **compiler toolchain**, which consists of four pieces:

First, the **preprocessor** rearranges the code. Header files and macros are
copied into the source code. Basically, every command that starts with a `#` is
an instruction for the preprocessor. This generates a temporary copy of the
preprocessed source code.

Next, the **compiler** converts the preprocessed source code into assembly code,
generating `*.s` files. The exact nature of this assembly code varies depending
on platform and architecture.

This is where the header files are needed: they tell the compiler
*what to expect*. All the different pieces aren't actually clicked together
yet, but the compiler can know roughly what should appear where, and how it
should all fit. Errors relating to syntax originate at this step.

For C++, one important task the compiler undertakes is **name mangling**.
This creates a unique name for each item, thereby allowing the linker to
distinguish between :code:`int foo(int, int)` and
:code:`int foo(float, float)`.

The important thing to remember is that *this is still only code*, just in a
different language. It is actually still human-readable - if we had been working
in Assembly instead of C or C++, we'd actually be doing our coding work here.

The **assembler** now converts the assembly code into machine, or binary, code.
At this stage, we call this **object code**, stored in `*.o` or `*.obj` files.
However, all of those function calls and references to external dependencies
have been unresolved this entire time! The compiler toolchain works on the
expectation that all those *will* work when all is said and done.

..  note:: Technically, when compiling a statically-linked library, things stop
    here. The `*.a` or `*.lib` is just an archive of object code.

The **linker** now fills in those blanks. It also completes the linking for the
statically-linked libraries, and brings the appropriate code into the finished
result. Errors relating to undefined references usually originate at this step.

Besides this, the linker may also rearrange things to make the program run
better, and to make the operating system happy. The end result is the compiled
executable or dynamically-linked library.

..  note:: We can force the compiler to stop anywhere in this process!
    `-E` stops after preprocessing, `-S` after compiling, and `-c` after
    assembling.

SOURCES:

* `The C++ compilation process (Northern Illinois University) <http://faculty.cs.niu.edu/~mcmahon/CS241/Notes/compile.html>`_

* `How does the compilation/linking process work? (StackOverflow) <https://stackoverflow.com/questions/6264249/how-does-the-compilation-linking-process-work>`_

* `Beginner's Guide to Linkers (LurkLurk) <http://www.lurklurk.org/linkers/linkers.html>`_

..  _buildc_compiler_stdlib:

The Standard Libraries
-------------------------------------

Two critical dynamically-linked libraries are the **C standard library** and
**C++ standard library**. Nearly every operating system comes with a version of
these libraries, and note the word "version". Different operating systems have
different implementations of the libraries, and the exact version sometimes
varies from one operating system version to the next.

..  _buildc_compiler_stdlib_c:

The C Standard Library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The C standard library is usually an inherent part of the operating system, in
dynamically-linked library form, although you may sometimes need to install the
headers separately. (On Linux, you don't.)

* On Linux (and some other UNIX systems), GNU's `glibc` provides the C standard
  library. It is installed by default.

* On Microsoft Windows, the Microsoft C run-time library is part of Microsoft
  Visual C++.

* On Mac OS X, the C standard library, the system file `libSystem.dylib`
  provides the C standard library.

* On BSD systems, BSD's own `libc` provides the C standard library.

..  _buildc_compiler_stdlib_cpp:

The C++ Standard Library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The C++ standard library is usually preinstalled on the operating system, as
virtually all C++ programs rely on it.

* On Linux (and some other UNIX systems), GNU's `libstdc++` is the C++ standard
  library. It is installed by default.

* On Microsoft Windows, the C++ standard library is not installed by default.
  Instead, it is usually provided via the "Microsoft Visual C++ Redistributable".

* On Mac OS X, LLVM's `libc++` is the C++ standard library. It is installed
  by default.

* On BSD systems, GNU's `libstdc++` has historically been used. However, on
  FreeBSD 10 and later, `libc++` is used.

..  note:: The **C++ standard library** is *NOT* the same thing as the
    :abbr:`STL (standard template library)`. The STL is specifically the part
    of the C++ standard library that has to do with containers and algorithms.
    It was originally developed separately, and later integrated into the
    larger standard library. The two terms are often confused.
    (`SOURCE: What is the STL? <https://stackoverflow.com/a/827431/472647>`_)

..  _buildc_compiler_stdlib_gnuorllvm:

GNU vs. LLVM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It is important to note that, on Linux, one is not **required** to use
`libstdc++`. It is, in fact, possible to install and use LLVM's `libc++`,
although this often requires compiling it from source. Since this is a
dynamically-linked library, if you compile against `libc++` on Linux, you *will*
need to ensure end-users also have it on their systems.

Aside from this, it is perfectly possible to use both `libstdc++` and `libc++`
in the same environment, because the mangled names for the two are actually
different.

There are several advantages LLVM's `libc++` has over GNU's `libstdc++`:

* `libc++` has a significantly cleaner, better-designed modern code base.

* The entire LLVM toolchain, including `libc++`, is thoroughly documented.

* We get access to additional tools and optimizations in the LLVM Clang
  compiler toolchain.

Ideally, we want to use `libc++` as much as possible. However, because it is not
always practical to provide this dynamically-linked library to end users, we
much also be able to compile against `libstdc++`.

..  _buildc_compiler_commands:

Compiler Commands
-------------------------------------

..  note:: You should bookmark the `official documentation for Clang <https://clang.llvm.org/docs/>`_

We rarely need to compile things manually, but it is helpful to know
*how* regardless. Let's break down the compiler commands that our build
system automatically generates. Here's the Debug target compile line::

    /usr/bin/clang++-5.0   -I/home/user/Code/Repositories/pawlib/pawlib-source/include -I/home/user/Code/Repositories/pawlib/pawlib-source/../../libdeps/libs/include  -g   -Wall -Wextra -Werror -std=c++14 -o CMakeFiles/pawlib.dir/src/binconv.cpp.o -c /home/user/Code/Repositories/pawlib/pawlib-source/src/binconv.cpp

And here is the Release target compile line::

    /usr/bin/clang++-5.0   -I/home/jason/Code/Repositories/pawlib/pawlib-source/include -I/home/jason/Code/Repositories/pawlib/pawlib-source/../../libdeps/libs/include  -O3 -DNDEBUG   -Wall -Wextra -Werror -std=c++14 -o CMakeFiles/pawlib.dir/src/binconv.cpp.o -c /home/jason/Code/Repositories/pawlib/pawlib-source/src/binconv.cpp

Those are ugly, long, scary lines of code (as well as specific to my system),
so let's break them down and tame them.

..  _buildc_compiler_commands_invoke:

Compiler Path
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

First, we invoke the compiler itself. This command varies depending which
compiler you're using, and where it's located::

    /usr/bin/clang++-5.0

Many times, you'll actually see :code:`/usr/bin/c++`. If the user set up
their compilers following our Network Documentation, they probably used
:code:`sudo update-alternatives`, which creates aliases that can be quickly
and easily switched between Clang or GCC (or whatever other compiler).

..  note:: If you're not sure which compiler is being used by
    :code:`update-alternatives`, run :code:`sudo update-alternatives --config c++`
    and note which option is selected. Replace `c++` with `cc` in that command
    to switch the C compiler.

..  _buildc_compiler_commands_i:

Include Paths
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Next, we tell the compiler where to look for header files, using the :code:`-I`
argument. We may use either relative or absolute paths here. Often, relative
paths are easier to manually write, and are more likely to be portable. The
reason we see absolute paths here is because the command was automatically
generated; the absolute paths are an implementation detail of CMake.

We should always first search for header files in the project itself::

    -I/home/user/Code/Repositories/pawlib/pawlib-source/include

Second, we'll search through the headers files for any external dependencies::

    -I/home/user/Code/Repositories/pawlib/pawlib-source/../../libdeps/libs/include

..  _buildc_compiler_commands_w:

Warnings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The compiler, especially Clang, produces a lot of warnings which help us write
cleaner code. However, many of these are disabled by default. To turn them
on, we include the following flags::

    -Wall -Wextra

In those flags, the `W` stands for `Warnings`; we are enabling `Warnings all`
and `Warnings extra`. These are actually shorthand for a whole host of
flags. Reference the official documentation for an exhaustive list of these
flags, and which are enabled by the commands we just used.

At MousePaw Media, we don't want to just *see* the warnings, we want them
fixed! To do this, we need to ask the compiler to treat all warnings as
errors::

    -Werror

This literally prevents the code from compiling if it has any warnings. This
causes automatic builds to fail if there are warnings, and helps us write
cleaner code.

However, there is a downside to this flag! If we're compiling *someone else's*
code, they may have ignored warnings, suppressed some of them with compiler
flags, or maybe even used a compiler which threw less (such as GCC). If this
happens, we either have to fix the warnings ourselves, or else turn off
:code:`-Werror`. As such, we usually compile third-party code *without* this
flag.

..  _buildc_compiler_commands_std:

C++ Standard
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Next, we need to specify the C++ standard we are using. This affects compiler
errors and warnings - some things that are allowed in C++14 aren't permitted in
C++11, and so forth. If we omit this flag, the compiler will use its default
standard, and what standard that is varies by compiler and version! Thus, it is
always better to just specify standard explicitly::

    -std=c++14

..  _buildc_compiler_commands_g:

Debugging Symbols
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In the case of the Debug version of our project, we need to ask the compiler
to add **debugging symbols** to the code. This adds additional code that
aids debugging tools::

    -g

If you're ever using a code analysis or debugging tool, and you're seeing
raw memory addresses instead of variable and function names, you almost
certainly forgot this flag.

When we're compiling with debugging symbols, we must be sure our optimization
level is not above :code:`-O0` (see next section).

..  _buildc_compiler_commands_o:

Optimization
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If we are compiling the Release version of our project, we will omit the
:code:`-g` flag, and include another to optimize the code::

    -O3

Like the `-W` flags correspond to warnings, `-O` flags corresponding to
optimizations. There are multiple levels and types of optimizations:

* :code:`-O0`: No optimization. Must be used with :code:`-g`. This is the
  default.

* :code:`-O1`: Some optimization.

* :code:`-O2`: Enables most optimizations.

* :code:`-O3`: Includes all of :code:`-O2`, plus optimizations that may take
  longer to compile or may make the code larger. This is where we start trading
  off size for execution speed.

* :code:`-Ofast`: Includes all of :code:`-O3`, plus some more that may violate
  strict compliance with language standards.

* :code:`-Os`: Includes all of :code:`-O2`, but tries to make the code smaller.
  This is where we start trading off execution speed for size.

* :code:`-Oz`: Includes :code:`-Os`, and tries to make the code even smaller.

There are a few more types, which can be found in the official documentation.

We also see the following flag::

    -DNDEBUG

The :code:`-D` flags define macros, which as you may know from C and C++,
are like preprocessor variables and functions. This specific command is used
to define the macro :code:`NDEBUG`, which turns off :code:`assert` statements
in the code. This is useful for Release targets, as it prevents the shipped code
from randomly crashing (and confusing the end-user) because of an assertion
failure.

..  _buildc_compiler_commands_io:

Input and Output Paths
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Now we can actually generate the object files from our code. We specify our
output path with :code:`-o`, and our input path with :code:`-c`::

    -o CMakeFiles/pawlib.dir/src/binconv.cpp.o -c /home/user/Code/Repositories/pawlib/pawlib-source/src/binconv.cpp

Once again, these particular paths were generated by CMake.

We must compile each implementation file (`*.c` and `*.cpp`) in our project
to a corresponding object file.

SOURCES:

* `*Clang command line argument reference (Clang Documentation) <https://clang.llvm.org/docs/ClangCommandLineReference.html>`_

* `*Code Generation Options (Clang Documentation) <https://clang.llvm.org/docs/CommandGuide/clang.html#code-generation-options>`_

* `*Diagnostic flags in Clang (Clang Documentation) <https://clang.llvm.org/docs/DiagnosticsReference.html>`_

* `Does "-dndebug" do anything in g++? (StackOverflow) <https://stackoverflow.com/a/24257232/472647>`_

* `*GCC Option Index (GCC) <https://gcc.gnu.org/onlinedocs/gcc/Option-Index.html>`_

..  _buildc_compiler_linker:

Linker Commands
-------------------------------------

Once we have our `*.o` or `*.obj` files, we can link them together to generate
the final product.

..  note:: Remember that we don't link a statically-linked library! Rather, we
    generate an archive of the object files. (See :ref:`buildc_compiler_ar`)

Let's look at the linker commands automatically generated by CMake. Here's
the one for a Debug target::

    /usr/bin/clang++-5.0  -g   -fuse-ld=lld -rdynamic CMakeFiles/pawlib-tester.dir/main.cpp.o CMakeFiles/pawlib-tester.dir/src/TestSystem.cpp.o  -o ../../bin/Debug/pawlib-tester ../../../pawlib-source/lib/Debug/libpawlib.a ../../../../libdeps/libs/lib/libcpgf.a

And here is one for a Release target::

    /usr/bin/c++  -O3 -DNDEBUG   -fuse-ld=lld -rdynamic CMakeFiles/pawlib-tester.dir/main.cpp.o CMakeFiles/pawlib-tester.dir/src/TestSystem.cpp.o  -o ../../bin/Release/pawlib-tester ../../../pawlib-source/lib/Debug/libpawlib.a ../../../../libdeps/libs/lib/libcpgf.a

Note, we are once again invoking our *compiler* program. However, the commands
tell it to call the linker in this step. In reality, it is going to invoke
one of three possible linkers:

* :code:`ld.bfd`, GCC's linker.

* :code:`ld.lld`, LLVM's linker. Usually versioned, i.e.
  :code:`ld.lld-4.0` or :code:`ld.lld-5.0`.

* :code:`ld.gold`, a linker designed to be faster than :code:`ld.bfd`.

By default, :code:`ld.bfd` will always be used on Linux. To use a different
linker, we use the :code:`-fuse-ld=` flag. For example, to use the
:code:`ld.gold` linker, we'd pass the flag :code:`-fuse-ld=gold`.

To use :code:`ld.lld` specifically...

1. We must be using the Clang compiler.

2. We must have the corresponding version of :code:`lld` installed in
   :file:`/usr/bin`. If we are using :code:`clang++-5.0`, we must have
   :code:`/usr/bin/lld-5.0` installed as well. You can check for this
   with the command :code:`whereis lld-5.0`, and ensuring the expected
   path is returned.

3. We must pass the flag :code:`-fuse-ld=lld` to Clang when we invoke the
   linker.

Aside from this flag and a couple other technical details, the three linkers
have exactly the same usage.

..  _buildc_compiler_linker_g:

Debugging Symbols
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The flag :code:`-g` is ignored by the all three linkers altogether, and is
only accepted for compatibility with other tools.

:code:`-rdynamic` is important to debugging, as it ensures all the symbols
needed by debugging are put in the proper places by the linker. (That's
actually over-summarizing the technical explanation, so see the GCC docs
I linked to below if you're curious.)

..  _buildc_compiler_linker_o:

Optimization Levels
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The flags :code:`-O0`, :code:`-O1`, :code:`-O2`, and :code:`-O3` work in
much the same way with the linker as with the compiler. With :code:`ld.bfd`,
they only affect certain types of libraries, but future versions may offer
further optimizations.

In short, it may not *help* your particular code, but it can't *hurt*, so just
assume there's a point to it and include the appropriate flag.

..  note:: I was unable to find documentation on if or how :code:`lld` handles
    optimization flags differently from :code:`ld`. It is safest to assume
    it will be identical.

As with the compiler, we also have the flag :code:`-DNDEBUG`, which functions
in the same capacity here as it does there.

..  _buildc_compiler_linker_io:

Input and Output
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After our other flags, we specify the file path to each of our object files.
Then, we specify our output with :code:`-o`. This part of the earlier commands
looks like the following::

    CMakeFiles/pawlib-tester.dir/main.cpp.o CMakeFiles/pawlib-tester.dir/src/TestSystem.cpp.o  -o ../../bin/Release/pawlib-tester

Finally, we must specify the file paths the `*.a` files for any and all
statically-linked libraries we are linking to.

Remember, **the order is important here!** The linker will resolve these
external dependencies *in order*, from left to right. If B depends on A, but you
list them in the order "A B", then the linker will be unable to link A to B,
and the linking will fail with one or more "undefined reference" errors.

This part of the commands looks like this::

    ../../../pawlib-source/lib/Debug/libpawlib.a ../../../../libdeps/libs/lib/libcpgf.a

..  TODO:: Add section about `-l` flag!

SOURCES

* `LLD - The LLVM Linker (LLVM) <http://releases.llvm.org/5.0.0/tools/lld/docs/index.html>`_

* `Using the GNU Compiler Collection: Link Options (GCC) <https://gcc.gnu.org/onlinedocs/gcc/Link-Options.html>`_

..  _buildc_compiler_ar:

Archiver Command
-------------------------------------

When we're compiling a statically-linked library, we don't actually call the
linker. All linking occurs when the statically-linked library is itself linked
to an executable. However, we do need to package, or **archive**, our object
files.

This isn't really much different than creating a :code:`*.zip` file, except
we're using a different compression format.

On Linux, we do this with the command :code:`ar`::

    /usr/bin/ar qc ../../lib/Debug/libpawlib.a  CMakeFiles/pawlib.dir/src/binconv.cpp.o CMakeFiles/pawlib.dir/src/core_types.cpp.o

..  note:: I have greatly shortened the actual command, as it involves more
    than a few file paths.

The :code:`ar` program accepts all its flags strung together into a string
at the beginning. In this case, we're using the flags::

    qc

Referencing the official manual (in the terminal, you can access this via
:code:`man ar`), we learn that :code:`c` tells the program that we are creating
an archive.

Meanwhile, :code:`q` speeds up the archiving process by simply adding every
given file to the archive without checking if it replaces something. Since
either we or the compiling tools we use will definitely not try to add any
object file more than once, we might as well skip the checks and speed up the
process.

Next, we specify the file path for the output::

    ../../lib/Debug/libpawlib.a

Finally, we only need to list the file path to each object file we're
packaging::

    CMakeFiles/pawlib.dir/src/binconv.cpp.o CMakeFiles/pawlib.dir/src/core_types.cpp.o

That's it! Our statically-linked library archive file is created, and is ready
to be used.

..  _buildc_cmake:

Automating with CMake
=====================================

..  _buildc_cmake_why:

Our Options: Why CMake?
-------------------------------------

It is clearly impractical to write out those commands every time we want to
compile our code. We need a way to simplify things.

The fastest way to automate our build process is to write a **Makefile**,
whereby each collection of commands is run when we issue a single "make"
command. However, this approach is not inherently portable: different operating
systems, and even different computers, use varying pieces in their compiler
toolchains. File paths vary. Compilation preferences differ from one user to
the next. We need a more robust solution.

Now, we *will* revisit Makefiles in the next section, where we'll use them to
add another layer of simplicity and automation on top of what we're about to
build here. The principles of Makefiles are the same, however you use them.

In searching for a more portable, robust means of automating our build process,
we have two major options.

**Autotools** is the longtime standard of the Linux world. However, it is
sometimes criticized for being complicated to configure, even downright
unwieldy, and for being internally crufty and outdated. It also doesn't work
out-of-the-box on non-UNIX systems, such as Windows, but rather must be run
within a Linux compatibility layer like MSYS2.

Our second option is **CMake**, a clean, modern, and well-documented system
for creating portable automated build systems. CMake supports all major
operating systems out-of-the-box. This makes it ideal for our use case.

..  _buildc_cmake_how:

How CMake Works
-------------------------------------

CMake is a **build system generator**. Simply put, you tell CMake all the
information about your project, and it generates the files best suited for
building the project on your particular system. In the case of UNIX-like systems
such as Linux, this means it basically writes Makefiles.

All the information about the project is placed in a file called
:file:`CMakeLists.txt`, which usually lives in or near the source code it
is building.

An important concept to understand is the difference between *in-place builds*
and *out-of-place builds*. An **in-place build** puts the generated files into
the same directory as the source code, whereas an **out-of-place build** places
those files into a separate directory. The latter is *strongly* recommended,
because it enables the user to have multiple CMake builds from the same source
code. However, one cannot mix the two types of builds. If an in-place build
has been performed, out-of-place builds will be possible until you clean up the
files from that in-place build.

Our configuration performs **out-of-place builds**, because the files are
placed in a separate directory from the source code. In our case, we build
into a subdirectory of our source code folder. Understand that
*this is still an out-of-place build!* The built files are totally ignored by
other builds.

SOURCE:

* `CMake FAQ (Kitware) <https://www.paraview.org/Wiki/CMake_FAQ#What_is_an_.22out-of-source.22_build.3F>`_

..  _buildc_cmake_config:

Configuring CMake
-------------------------------------

All of the information about our project belongs in :file:`CMakeLists.txt`.
If you look again at :ref:`buildc_repo`, you'll notice that each compilable
project has its own version of this file, located at the root of the project's
folder.

In practice, this file is *huge*. CMake has excellent, though exhaustive,
documentation, so it is possible to fully understand any :file:`CMakeLists.txt`
you may encounter. However, our versions of this file always follow the same
pattern, so we'll break it down here.

As an example, we'll take a look at the :file:`CMakeLists.txt` for our
PawLIB library. Then, we'll note the differences in the version for PawLIB's
tester.

Let's start at the top.

..  _buildc_cmake_config_v:

Version
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: cmake

    # Specify the version being used.
    cmake_minimum_required(VERSION 3.1)

Every line that starts with :code:`#` is a comment. Remember, our company
commenting standards apply to :file:`CMakeLists.txt` as much as they do to any
source code.

We need to specify the earliest version of CMake that can be used with this
file. Since the newest features we're relying on were introduced in CMake
3.1, that's the version we use here.

..  _buildc_cmake_config_name:

Project and Target Name
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: cmake

    # CHANGEME: Name your project here
    project("PawLIB")
    set(TARGET_NAME "pawlib")

We need to tell CMake what the formal, print-friendly name of our project is,
using :code:`project()`.

Many CMake commands also require the technical name of the "target" we're
building. Normally, we would hard-code this into each command that requires it,
but this leaves a lot of room for error, especially when we tweak the file to
use with another project. To get around this, we create a custom variable named
:code:`TARGET_NAME`. By defining it once here, we ensure every command in the
file gets the same target name. We can change the target name simply by changing
this variable.

The :code:`set()` command creates or modifies a variable. It requires two
arguments - the variable name and the value, separated by a space. We'll see
this used many more times, so get comfortable with it.

..  note:: In CMake, arguments are separated by spaces instead of commas.
    Remember this!

..  _buildc_cmake_config_config:

Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We will be defining some variables externally (more about that later,
see :ref:`buildc_cmake_externconfig`).
We designed our system to expect to find this file in the root of the
repository, with the extension :code:`*.config`.

..  code-block:: cmake

    message("Using ${CONFIG_FILENAME}.config")
    include(${CMAKE_HOME_DIRECTORY}/../${CONFIG_FILENAME}.config)

The command :code:`include()` allows us to include any arbitrary text file
containing CMake configuration code. The variable :code:`CONFIG_FILENAME`
will need to be defined when we call CMake. The variable
:code:`CMAKE_HOME_DIRECTORY` is defined by CMake itself, and refers to the
directory where :file:`CMakeLists.txt` is found.

..  _buildc_cmake_config_compiler:

Checking Compilers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

CMake needs to behave differently, depending on which compiler is being used.
We also want to ensure that the end-user is actually building with a supported
compiler. We do that here...

..  code-block:: cmake

    # Compiler and Version check...
    # Allow Clang 3.4
    if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
        if(CMAKE_CXX_COMPILER_VERSION VERSION_LESS "3.4")
            message(FATAL_ERROR "Clang is too old. >= v3.4 required. Aborting build.")
        endif()
        set(COMPILERTYPE "clang")
        message("Clang 3.4 or later detected. Proceeding...")
    # Allow GCC 5.x
    elseif(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
        if(CMAKE_CXX_COMPILER_VERSION VERSION_LESS "5")
            message(FATAL_ERROR "GCC is too old. >= v5.x required. Aborting build.")
        endif()
        set(COMPILERTYPE "gcc")
        message("GCC 5.x or later detected. Proceeding...")
    # Allow compilers that simulate GCC 5.x.
    elseif(CMAKE_CXX_SIMULATE_ID STREQUAL "GNU")
        if(CMAKE_CXX_SIMULATE_VERSION VERSION_LESS "5")
            message(FATAL_ERROR "Not simulating GCC 5.x. Aborting build.")
        endif()
        set(COMPILERTYPE "gcc")
        message("GCC (5.x or later) simulation detected. Proceeding...")
    else()
        message(FATAL_ERROR "Not using or simulating a compatible compiler (minimum GCC 5.x, Clang 3.4). Other compilers are not yet supported. Aborting build.")
    endif()

The variable :code:`CMAKE_CXX_COMPILER_ID` is defined by CMake itself, and
indicates the type of compiler being used. (The full list of possible values
can be found here: `CMAKE_LANG_COMPILER_ID (CMake) <https://cmake.org/cmake/help/v3.9/variable/CMAKE_LANG_COMPILER_ID.html>`_).
For quality purposes, we only officially support compiling with GNU GCC or
LLVM Clang at this time. If it didn't matter what we were using, we could
actually skip this entire section.

If we're using Clang, we also want to ensure we're using a version capable of
support C++14, which we use. That support was added in 3.4, so we check
the value of the variable :code:`CMAKE_CXX_COMPILER_VERSION`. If it is less
than the target version, we need to abort with a message.
:code:`message(FATAL_ERROR "The message text")` aborts the process and prints
the given message on the command line.

If we *are* using the right version (thus bypassing that conditional),
we know we're using a support Clang compiler. For simplicity of code, we
create a new variable called :code:`COMPILERTYPE` and set its value to
"clang". We also display a regular message via
:code:`message("The message text.")` to tell the user what they're using.

In the same way, if we're using GCC, we need to ensure we're using version 5
or later.

In addition to supporting GCC and Clang, we also want to support compilers
which *simulate* GCC, which is functionally equivalent. To test for this,
we check the built-in variable :code:`CMAKE_CXX_SIMULATE_ID` and
:code:`CMAKE_CXX_SIMULATE_VERSION`, which work the same as their aforementioned
counterparts.

In our case, if all of these conditions fail, we can know we aren't using a
supported compiler, and we need to abort the build.

..  _buildc_cmake_config_lang:

Language Configurations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We need to tell CMake that we're using C++14, so it can tell the compiler
when creating the Makefiles. We also need to turn off **language extensions**,
additional language features added by the compiler, since supporting multiple
compilers makes it impractical to use them.

..  code-block:: cmake

    # Target C++14
    set(CMAKE_CXX_STANDARD 14)
    # Disable extensions (turns gnu++14 to c++14)
    set(CMAKE_CXX_EXTENSIONS OFF)

..  _buildc_cmake_config_arch:

Architectures
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As you may know, 32-bit systems and 64-bit systems use slightly different
assembly and machine instructions. This means that we must compile our code
separately for each! Thankfully, most modern compilers allow you to compile
32-bit versions from 64-bit systems. We simply need to tell the compiler
we *want* to do this.

To support this, we'll use a custom variable named :code:`ARCH`. CMake users
can set the value of any variable when they *call* CMake, so we can create
a handy little switch for 32-bit and 64-bit builds.

..  code-block:: cmake

    # Target 32-bit or 64-bit, based on ARCH variable.
    if(ARCH EQUAL 32)
        message("Triggered 32-bit build (-m32).")
        add_definitions(-m32)
    elseif(ARCH EQUAL 64)
        message("Triggered 64-bit build (-m64).")
        add_definitions(-m64)
    # If 32-bit or 64-bit wasn't specified, just use the default.
    elseif(NOT ARCH)
        message("Building for default architecture.")
    else()
        message(FATAL_ERROR "Invalid architecture (${ARCH}) specified. Aborting build.")
    endif()

The principle is simple. If :code:`ARCH` equals `32`, we will be using the
compiler flag :code:`-m32` to indicate that we want to build a 32-bit version.
We can add compiler flags using the CMake command :code:`add_definitions()`.
Similarly, if :code:`ARCH` is `64`, we want to force a 64-bit build with the
flag :code:`-m64`.

However, if the user didn't specify a value for :code:`ARCH` (as detected
by :code:`elseif(NOT ARCH)`), we don't supply any special flag, and we trust
the compiler to use its own default.

In any other condition, the variable :code:`ARCH` would have been specified
with a value other than `32` or `64`, so we throw a fatal error. Note how we
are substituting in the given value for :code:`ARCH` into our message string
using the code :code:`${ARCH}`.

..  _buildc_cmake_config_flags:

Compiler Flags
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Next, we can start adding our other compiler flags. First, we add the
flags that we *always* use, regardless of compiler or target.

..  code-block:: cmake

    # Our global compiler flags.
    add_definitions(-Wall -Wextra -Werror -pedantic)

Next, we make use of the :code:`COMPILERTYPE` variable we created earlier
to determine whether we're working with GCC or Clang.

..  code-block:: cmake

    if(COMPILERTYPE STREQUAL "gcc")
        # -Wimplicit-fallthrough=0 is required for
        # GCC 7.x and onward. That is, until we switch
        # to C++17
        if(CMAKE_CXX_COMPILER_VERSION VERSION_EQUAL "7" OR CMAKE_CXX_COMPILER_VERSION VERSION_GREATER "7")
            add_definitions(-Wimplicit-fallthrough=0)
        endif()
        # Set debug flags. -g is a default.
        #set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} ")
        # Set release flags. -O3 is a default.
        set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -s")
    elseif(COMPILERTYPE STREQUAL "clang")
        # Use libc++ if requested.
        if(LLVM)
            add_definitions(-stdlib=libc++)
            message("Using LLVM libc++...")
        endif()
        # Set debug flags. -g is a default.
        #set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} ")
        # Set release flags. -O3 is a default.
        #set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} ")
    endif()

The outer conditional switches between :code:`"gcc"` and :code:`"clang"`.

Inside, we have a conditional to check the compiler version for GCC. In
GCC 7.x, new warnings were introduced in regard to implicit fallthroughs
for C++ switch statements, because of some new features in C++17. However,
because we're still on C++14, we need to silence these warnings. Otherwise,
the build will abort because of the :code:`-Werror` flag later, that tells
the compiler to abort on warnings.

Thus, we have to first check if the GCC version is equal to or greater than
7.x, and then add the flag to mute the implicit fallthrough warnings. On
earlier versions of GCC, this flag doesn't exist; Clang also doesn't have it.

We also add compiler-specific flags to those used for :code:`Debug`
and :code:`Release`. To do this, we only need to :code:`set()` the
:code:`CMAKE_CXX_FLAGS_DEBUG` or :code:`CMAKE_CXX_FLAGS_RELEASE` built-in
variables. Since we only want to *add* to these variables, not overwrite
them entirely, we need to reattach the current contents of the variable to the
new version, by including :code:`${CMAKE_CXX_FLAGS_DEBUG}` (or `RELEASE`) at
the beginning of the new string.

You'll also notice that we've commented out some of these :code:`set()`
statements. This is because, at least in this example, we don't *need*
to add to the compiler flags for some scenarios. However, we leave the
commented-out line present, in case we need it later.

At the moment, we only need to add one flag if we're compiling :code:`Release`
on GCC: :code:`-s`, which is an optimization that removes all symbol table and
relocation information from the executable, thereby making a smaller program.

You will note that we never define the flags :code:`-g` or :code:`-O3`
anywhere. That's because CMake assumes these automatically for the :code:`Debug`
and :code:`Release` targets, respectively. We would need to specify flags that
override these to change them.

If we are using Clang, we also want to check for the definition of a custom
variable, :code:`LLVM`, which we'll discuss in a later section. If the variable
is defined, we add the flag :code:`-stdlib=libc++`, requesting that Clang
uses LLVM's `libc++`.

..  _buildc_cmake_config_linker:

Linker Flags
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

CMake sets our typical linker options automatically based on target. However,
we want to provide the option to use a different linker than the system
default. We can do that with the following...

..  code-block:: cmake

    if(LD)
        message("Using ${LD} linker...")
        set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fuse-ld=${LD}")
    endif()

By now, this should appear pretty straight-forward. If the custom variable
:code:`LD` is defined, we substitute its value into the linker flag
:code:`-fuse-ld=whatever`. The valid options here are :code:`bfd`,
:code:`gold`, and (on Clang only) :code:`lld`.

..  _buildc_cmake_config_io:

Input and Output
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Aside from the names at the top, the above code is the same between all
our :code:`CMakeLists.txt` files for C++ projects. From here on, we define the
input and output files, so the code varies greatly from one project to the
next.

We start by defining our output.

..  code-block:: cmake

    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/../../lib/$<CONFIG>")

Remember, for this file, we're creating a statically-linked library, so we must
use the variable :code:`CMAKE_ARCHIVE_OUTPUT_DIRECTORY`. The rest of the command
is always the same - we use the automatically defined variable
:code:`CMAKE_CURRENT_BINARY_DIR` to start our path within the directory
we're building in. Then we step back two levels and go into the :code:`lib`
folder, and either the :code:`Debug` or :code:`Release` folder (determined by
:code:`$<CONFIG>`).

Now we include all the directories containing the header files we need,
starting with our project's own...

..  code-block:: cmake

    include_directories(include)

...and adding the header file directories for any statically-linked libraries
we are using.

..  code-block:: cmake

    # CHANGEME: Include headers of dependencies.
    include_directories(${CPGF_DIR}/include)

You'll notice that we are using another custom variable, :code:`CPGF_DIR`,
while we define externally (we'll discuss that in a later section).

Now we add the actual source files for our project - both the headers and
implementation files! The paths here are all relative to the root of the
project, where our :code:`CMakeLists.txt` lives.

You should also note the first line of this section:
:code:`add_library(${TARGET_NAME} STATIC`. We are creating a *library* with
the :code:`TARGET_NAME` we defined at the top of the file (in this case,
:code:`pawlib`). We also specify :code:`STATIC` because our library
will be *statically-linked.*

..  note:: If you're having trouble getting the build system to compile,
    check here first!

..  code-block:: cmake

    # CHANGEME: Include files to compile.
    add_library(${TARGET_NAME} STATIC
        include/pawlib/avl_tree.hpp
        include/pawlib/base_flex_array.hpp
        include/pawlib/core_types.hpp
        include/pawlib/core_types_tests.hpp
        include/pawlib/flex_array.hpp
        include/pawlib/flex_array_tests.hpp
        include/pawlib/flex_bit_tests.hpp
        include/pawlib/flex_bit.hpp
        include/pawlib/flex_map.hpp
        include/pawlib/flex_queue.hpp
        include/pawlib/flex_queue_tests.hpp
        include/pawlib/flex_stack.hpp
        include/pawlib/flex_stack_tests.hpp
        include/pawlib/goldilocks.hpp
        include/pawlib/goldilocks_shell.hpp
        include/pawlib/iochannel.hpp
        include/pawlib/onechar.hpp
        include/pawlib/onestring.hpp
        include/pawlib/onestringbase.hpp
        include/pawlib/onestring_tests.hpp
        include/pawlib/pawsort.hpp
        include/pawlib/pawsort_tests.hpp
        include/pawlib/pool.hpp
        include/pawlib/pool_tests.hpp
        include/pawlib/quickstring.hpp
        include/pawlib/rigid_stack.hpp
        include/pawlib/singly_linked_list.hpp
        include/pawlib/stdutils.hpp

        src/core_types.cpp
        src/core_types_tests.cpp
        src/flex_array_tests.cpp
        src/flex_bit_tests.cpp
        src/flex_queue_tests.cpp
        src/flex_stack_tests.cpp
        src/goldilocks.cpp
        src/goldilocks_shell.cpp
        src/iochannel.cpp
        src/onechar.cpp
        src/onestring.cpp
        src/onestringbase.cpp
        src/onestring_tests.cpp
        src/pawsort_tests.cpp
        src/pool_tests.cpp
        src/quickstring.cpp
        src/stdutils.cpp
    )

Take note that we carefully organize this section, for easier maintainability!
Our company convention is to list the headers in the first section, and the
implementation files in a separate section. Within each section, the files
should *always* be in alphabetical order.

Next, we need to list any libraries we want to link to.

..  important:: The order is *critical* here! If you rely on A and B, and B
    also relies on A, you must list them in the order "B, A". If you're having
    trouble with linking, check here.

..  code-block:: cmake

    # CHANGEME: Link against dependencies.
    target_link_libraries(${TARGET_NAME} ${CPGF_DIR}/lib/libcpgf.a)

Once again, we're using our :code:`TARGET_NAME` variable from the top,
thereby telling the :code:`target_link_libraries()` command which target
it should be linking the external library to. (Yes, it is possible to
build multiple targets in one file, although we aren't doing that here.)

..  _buildc_cmake_config_sanitizers:

Sanitizers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The last part of this file is *also* pretty consistent across all versions
of :code:`CMakeLists.txt` in our company. We need to be able to use any of
Clang's many "sanitizers" - dynamic analysis tools that aid us in finding
bugs and errors.

..  code-block:: cmake

    if(COMPILERTYPE STREQUAL "clang")
        if(SAN STREQUAL "address")
            add_definitions(-O1 -fsanitize=address -fno-optimize-sibling-calls -fno-omit-frame-pointer)
            set_property(TARGET ${TARGET_NAME} APPEND_STRING PROPERTY LINK_FLAGS " -fsanitize=address")
            message("Compiling with AddressSanitizer.")
        elseif(SAN STREQUAL "leak")
            add_definitions(-fsanitize=leak)
            set_property(TARGET ${TARGET_NAME} APPEND_STRING PROPERTY LINK_FLAGS " -fsanitize=leak")
            message("Compiling with LeakSanitizer.")
        elseif(SAN STREQUAL "memory")
            if(LLVM)
                add_definitions(-O1 -fsanitize=memory -fno-optimize-sibling-calls -fno-omit-frame-pointer -fsanitize-memory-track-origins)
                set_property(TARGET ${TARGET_NAME} APPEND_STRING PROPERTY LINK_FLAGS " -fsanitize=memory")
                message("Compiling with MemorySanitizer.")
            else()
                message("Skipping MemorySanitizer: requires libc++")
            endif()
        elseif(SAN STREQUAL "thread")
            add_definitions(-O1 -fsanitize=thread)
            set_property(TARGET ${TARGET_NAME} APPEND_STRING PROPERTY LINK_FLAGS " -fsanitize=thread")
            message("Compiling with ThreadSanitizer.")
        elseif(SAN STREQUAL "undefined")
            add_definitions(-fsanitize=undefined)
            set_property(TARGET ${TARGET_NAME} APPEND_STRING PROPERTY LINK_FLAGS " -fsanitize=undefined")
            message("Compiling with UndefinedBehaviorSanitizer.")
        endif()
    endif()

Since these sanitizers only work when we're compiling with Clang, we need to
ensure that is the compiler we're using that. With that logic taken care of,
we are relying on the custom variable :code:`SAN` to determine which, if any
sanitizer we are to compile with.

The syntax should be pretty easy to discern by now, so let's talk about intent.
Each of the five sanitizers our build system supports need certain compiler
and linker flags for optimal performance (see their documentation). We
include each of those flags here.

One more consideration: :code:`MemorySanitizer` doesn't work well without
using the :code:`libc++` library, so we need to ensure that is being used
before trying to compile and link with that sanitizer.

..  _buildc_cmake_config_libvsexe:

Differences Compiling Libraries and Executables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If we compare the :code:`CMakeLists.txt` above with that for
:code:`pawlib-tester`, we'll note only a handful of details.

First, we specify our output using...

..  code-block:: cmake

    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/../../bin/$<CONFIG>")

This is identical to the same line for statically-linked libraries, except
we're using the variable :code:`CMAKE_RUNTIME_OUTPUT_DIRECTORY` instead of
:code:`CMAKE_ARCHIVE_OUTPUT_DIRECTORY`.

Second, while before we used the command :code:`add_library()` to list the
files in our project, we use...

..  code-block:: cmake

    # CHANGEME: Include files to compile.
    add_executable(${TARGET_NAME}
        main.cpp

        include/TestSystem.hpp

        src/TestSystem.cpp
    )

Our convention for listing files is still the same, although we list
:code:`main.cpp` by itself at the top.

We link to libraries in the same manner as before, except now we have one
more to link to - the version of PawLIB we *just* compiled.

..  code-block:: cmake

    # CHANGEME: Link against dependencies.
    target_link_libraries(${TARGET_NAME} ${CMAKE_HOME_DIRECTORY}/../pawlib-source/lib/$<CONFIG>/libpawlib.a)
    target_link_libraries(${TARGET_NAME} ${CPGF_DIR}/lib/libcpgf.a)

If you compare that to other projects that link to PawLIB, you'll notice that
here we are seeking out a copy of the compiled PawLIB within the project itself.
Elsewhere, such as in SIMPLEXpress, you'd expect to see...

..  code-block:: cmake

    # CHANGEME: Link against dependencies
    target_link_libraries(${TARGET_NAME} ${PAWLIB_DIR}/lib/libpawlib.a)
    target_link_libraries(${TARGET_NAME} ${CPGF_DIR}/lib/libcpgf.a)

..  _buildc_cmake_externconfig:

Configuration Files
-------------------------------------

Not all of the information we need has to be put directly into
:code:`CMakeLists.txt`. As you saw, we have several custom variables that
aren't even defined yet. This is where **configuration files** come in.

We can put additional information into any text file, so long as we use
the CMake syntax, and hand that text file to CMake when we invoke it.

We are allowed to name these files anything we want, but for our own
in-house build system, we follow the convention of ending the files with
:code:`.config`. We always provide :file:`default.config`, and allow the user
to define other custom :code:`.config` files, using the provided
:file:`build.config.txt` as a template.

Let's break down :file:`default.config` and see what's going on.

..  code-block:: cmake

    # DO NOT MODIFY THIS FILE!
    # Use build.config.txt to change the path.

Obviously, we don't want users to change the default configuration file,
thus the warning comments at the top.

All of the variables defined here were used in :file:`CMakeLists.txt` to
do different things. By themselves, they do nothing - they are part of
*our particular build system*, and aren't inherent to CMake itself.

..  code-block:: cmake

    set(LLVM false)

This line allows us to ask Clang to use of :code:`libc++`. Setting the value
to :code:`true` turns on this option.

..  code-block:: cmake

    # set(LD "lld")

This line asks the toolchain to use a particular linker. Based on how we
wrote :file:`CMakeLists.txt`, we can use the values :code:`"lld"`,
:code:`"gold"`, or :code:`"bfd"`.

..  code-block:: cmake

    set(CPGF_DIR
    	${CMAKE_HOME_DIRECTORY}/../../libdeps/libs
    )

Finally, we need to set the paths where we can find our dependencies. In this
example, we only need CPGF. The absolute path is generated by CMake, so we
only need to provide a relative path, starting from the directory where
:file:`CMakeLists.txt` is located. That starting position is provided by
CMake with the variable :code:`CMAKE_HOME_DIRECTORY`.

This default path is related to the conventional layout of repositories
our company uses. We typically clone all repositories into the same directory,
and we keep all our third-party static library dependencies in a repository
named :file:`libdeps`. Thus, this path steps back two levels, to the directory
of repositories, and then into the :file:`libdeps` repository.

Some projects also look for PawLIB, and we define the path for it in the same
basic manner...

..  code-block:: cmake

    set(PAWLIB_DIR
        ${CMAKE_HOME_DIRECTORY}/../../pawlib/pawlib
    )

...except now we look for the :file:`pawlib` repository.

Of course, all of this needs to be documented for the end user, so we provide
that :file:`build.config.txt` file, which contains a lot of comments describing
these variables and how to set them.

CMake can't actually see these files by default, so we'll need to point to the
file when we invoke CMake.

Whew, we've made it through the entirety of :file:`CMakeLists.txt` and
the config files. That was a lot of information, so take a deep breath.
We're about to put all this into action.

..  _buildc_cmake_build:

Building with CMake
-------------------------------------

CMake offers a *lot* of options, even from the command line. We won't
go into most of those - the official documents can help with that - but we
*will* discuss the ones relevant to us.

We can build our CMake project on Mac or Linux using the following commands.
These particular commands must be run from the same directory as
:file:`CMakeLists.txt`, but you could apply the concepts and build the project
elsewhere.

..  code-block:: bash

    $ cmake -E make_directory build_temp/Debug

One of CMake's biggest benefits is that it offers portable commands like
:code:`make_directory` which automatically perform the action as appropriate
to the system you're on. In this case, we're creating a new folder for our
temporary files.

We run any CMake command via :code:`cmake -E`.

..  code-block:: bash

    $ cmake -E chdir build_temp/Debug cmake ../.. -G"Unix Makefiles" -DCMAKE_BUILD_TYPE=Debug -DCONFIG_FILENAME=default

The command :code:`chdir` runs the given line of command-line code from the
specified path. In this case, we want to run the command from within the
directory we created a moment ago.

Let's break down the command in question. :code:`../..` is the path to the
:code:`CMakeLists.txt` we're using. :code:`-G"Unix Makefiles"` specifies what
we're making - in this case, the Makefiles for building our code on a Unix
system.

Any flag starting with :code:`-D` is Defining a CMake variable. This is
where we define those last few undefined variables we use in
:file:`CMakeLists.txt`. In this case, we are setting :code:`CMAKE_BUILD_TYPE`
to :code:`Debug`, and :code:`CONFIG_FILENAME` to :code:`default` (thereby
using the configuration file :code:`default.config`).

Once the build files have been generated, we're ready to build!

..  code-block:: bash

    $ cmake -E chdir build_temp/Debug make debug

This runs the :code:`make debug` in the directory we generated our build
files into. This is when the compiling and linking actually occurs.

That's it!

We could obviously compile the :code:`Release` target by using the flag
:code:`-DCMAKE_BUILD_TYPE=Release` in that second command. We could also
define other variables, including the custom variables we use in our
:file:`CMakeLists.txt`. For example, :code:`-DARCH=32` would request a
32-bit build, and :code:`-DSAN=address` would ask the compiler to use
AddressSanitizer.

..  _buildc_cmake_build_compiler:

Selecting a Compiler
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ideally, you should configure your compilers following the tutorial in
our Network Documentation, thereby allowing you to use
:code:`sudo update-alternatives` to switch them. However, if you don't want
to do this, you can also override which compiler the system uses.

To select a C compiler, use the terminal command...

..  code-block:: bash

    export CC=/usr/bin/gcc

...where you specify the absolute path to the compiler you want.

Similarly, you can change the C++ compiler using...

..  code-block:: bash

    export CXX=/usr/bin/g++

If you've set these previously, and you want to start using
:code:`sudo update-alternatives`, run the following.

..  code-block:: bash

    export CC=/usr/bin/cc
    export CXX=/usr/bin/c++

..  _buildc_makefiles:

Simplifying with Makefiles
=====================================

On UNIX systems, CMake automatically generates and runs Makefiles appropriate
to the environment. This saves us quite a bit of time, not to mention the
trouble of fussing with Autotools. Therefore, it may seem a bit paradoxical
that we'd be writing our own Makefiles.

This is where our philosophy of build systems has been called both "brilliant"
and "convoluted" by different people. We use top-level Makefiles to automate
the most common build tasks. Very rarely will a user on a UNIX system need to
directly invoke CMake, although they still can if they need to.

Coming from our discussion of CMake, it is important to point out that the
autogenerated Makefiles are in the temporary build directories.

..  _buildc_makefiles_syntax:

Makefile Syntax
-------------------------------------

The basic syntax of a Makefile is very simple. We specify named *rules* or
*targets*, which may depend on other targets being built first. Then, we run
the commands for that target. In short, each rule or target looks like this...

..  code-block:: make

    target: dependonme alsodependonme
        Commands here
        and here
        and so forth

There are a few special target names. Perhaps the most common is :code:`none:`,
which is run if someone executes the Makefile without a target specified
(e.g. just running `make`).

The other special target names start with a period (``.``), followed by
all uppercase letters. We won't use most of these, as they relate to building
with GNU Make and Autotools, instead of CMake.

The only one of particular importance is :code:`.PHONY`. Ordinarily, targets
all relate to files being built. In all other cases, we need to tell GNU Make
that nothing is being built, but rather that it's just a symbolic name for a
task (e.g. :code:`make clean`). We list all of these target names under
the :code:`.PHONY` target, in the same way as if they were dependencies.

..  code-block:: make

    .PHONY: clean help

It really is that simple.

SOURCES:

* `GNU make: Rule introduction <https://www.gnu.org/software/make/manual/html_node/Rule-Introduction.html#Rule-Introduction>`_

* `GNU make: Special Targets <https://www.gnu.org/software/make/manual/html_node/Special-Targets.html#Special-Targets>`_

..  _buildc_makefiles_syntax_tabs:

Tabs v. Spaces
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Makefiles are very particular about tabs and spaces. You *must* use a TAB
at the start of each line belonging to a target. If you use spaces, the
Makefile will throw a syntax error.

..  _buildc_makefiles_automating:

Automating CMake with Makefiles
-------------------------------------

We have two levels of Makefiles in our build systems. The inner level invokes
CMake and its own automatically generated Makefiles, and the outer level
Makefile invokes the inner level ones.

Since we've started at the lowest level of abstraction -- the compiler
commands themselves -- and are moving upward, we'll start by looking at
an inner-level Makefile. Specifically, we'll be breaking down
:file:`pawlib-source/Makefile` line-by-line first.

..  note:: :file:`pawlib-tester/Makefile` is functionally identical, so we
    won't worry about that one.

..  _buildc_makefiles_automating_variables:

Variables and Macros
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Because we effectively use the same inner Makefile over and over, we want
to minimize the changes we need to make to it. Variables enable us to change
critical pieces of information in on place, and have that change replicate
reliably across our entire file.

It is also helpful to break down some of the more commonly used commands into
reusable chunks called **macros**. While this might make the file a bit harder
to read initially, it is considerably easier to change the functionality later
if necessary.

Makefiles have many similarities to Bash scripts, including how variables
are declared. There are no practical differences between variables and macros
in shell scripts, nor in Makefiles. In both cases, we merely follow the
syntax of :code:`NAME = value`.

..  note:: The convention is for variables and macros to have UPPERCASE names,
    although this isn't strictly required.

When we use a macro or variable, we must enclose it in parenthesis, and precede
it with the :code:`$` character. For example, if our variable is named
:code:`FOO`, we would use, or substitute, it in via :code:`$(FOO)`.

We declare all our variables and macros at the top of the Makefile we use them
in.

..  code-block:: make

    CMAKE = cmake ../.. -G"Unix Makefiles"

This macro is for invoking CMake itself. You should remember from earlier
(see :ref:`buildc_cmake_build`) that we run CMake from within the directory
where we want the build files to be created. In this case, we run that from
:file:`pawlib-source/build_temp/Debug` or :file:`pawlib-source/build_temp/Release`,
so we must tell CMake to look back directory levels (:code:`../..`) to find
:file:`CMakeLists.txt`.

The flag :code:`-G"Unix Makefiles"` tells CMake to generate Makefiles for a
UNIX system. We'll wind up calling the generated file later in this Makefile.

Next, we create macros for the two options used to create Debug and Release
builds in CMake.

..  code-block:: make

    T_DEBUG = -DCMAKE_BUILD_TYPE=Debug
    T_RELEASE = -DCMAKE_BUILD_TYPE=Release

By turning the options into macros, it will shorten and simply our code later.

If you recall, we configured CMake to accept a :file:`.config` file.
(See :ref:`buildc_cmake_config_config` and :ref:`buildc_cmake_externconfig`.)
We need to allow the end-user to specify that when they call :code:`make`.

..  code-block:: make

    P_DEF_CONF = default
    P_CONF = -DCONFIG_FILENAME=
    ifneq ( , $(CONFIG))
        P_CONF_PATH=$(CONFIG)
    else
        P_CONF_PATH = $(P_DEF_CONF)
    endif

The first variable, :code:`P_DEF_CONF`, defines the name of the
default :file`.config` file. If the user doesn't specify another config
file name, this will be used.

Next is the macro :code:`P_CONF`, which is the first part of the flag we use
to pass the config file name to CMake. Note, we've got nothing after the
:code:`=`. We'll be combining this macro with the correct file name later.

Next, we check if the :code:`CONFIG` flag, which would be specified by the user
when they run the Makefile, has a value. If it does (:code:`ifneq ( , $(CONFIG))`),
we create a new variable called :code:`P_CONF_PATH`, and store the user-specified
value of :code:`CONFIG` in it. Otherwise (:code:`else`), we store the default
path in :code:`P_CONF_PATH`.

Next, we'll define three common commands.

..  code-block:: make

    MK_DIR = @cmake -E make_directory
    CH_DIR = @cmake -E chdir
    RM_DIR = @cmake -E remove_directory
    ECHO = @cmake -E echo

CMake offers these functions to make it easier for us to write cross-platform
scripts: they call the system-specific command for making a directory,
changing directory, removing a directory, or printing to the terminal, respectively.

By default, Makefiles print each line they're about to execute, before actually
running it. That's useful in some cases, and really noisy and annoying in
other cases, such as an :code:`echo` command to print something on the terminal.
The :code:`@` symbol tells Make not to print the command before running it.

Just to make things simple, let's get the name of the application from a
variable.

..  code-block:: make

    # CHANGEME: Project name
    NAME = "PawLIB (Static Library)"

Next up, we define the paths important to our build.

..  code-block:: make

    # CHANGEME: Set to 'lib' or 'bin'
    BUILD_DIR = lib

    TEMP_DIR = build_temp

The variable :code:`BUILD_DIR` contains the name of the subdirectory that the
finished binaries will be moved to. This should be set to :file:`lib` for
libraries, as seen here, and :file:`bin` for executables
(such as seen in :file:`pawlib-tester/Makefile`).

:file:`build_temp` is the name of the subdirectory that we'll be creating,
and then running CMake from within. When we clean the build, this directory
will be deleted.

Finally, we have the macro containing the first part of the command for
actually invoking the CMake-generated Makefile. There's quite a bit more to
the whole command, which we'll talk about later.

..  code-block:: make

    EXEC_BUILD = $(CH_DIR) $(TEMP_DIR)

In short, we are just changing directories (using the earlier macro) to the
temporary subdirectory we use for building.

..  _buildc_makefiles_automating_default:

Default Target and Help
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This Makefile is intended to be very user friendly, so we want to display some
usage documentation for the target :code:`make help`. We'll also make this the
default target, so if the user just runs :code:`make`, they'll see the
instructions.

..  code-block:: make

    none: help

    help:
        $(ECHO) "=== $(NAME) ==="
        $(ECHO) "Select a build target:"
        $(ECHO) "  make debug"
        $(ECHO) "  make release"
        $(ECHO)
        $(ECHO) "  make clean"
        $(ECHO) "  make cleandebug"
        $(ECHO) "  make cleanrelease"
        $(ECHO)
        $(ECHO) "Clang Sanitizers (requires Debug build and Clang.)"
        $(ECHO) "  SAN=address     Use AddressSanitizer"
        $(ECHO) "  SAN=leak        Use LeakSanitizer w/o AddressSanitizer (Linux only)"
        $(ECHO) "  SAN=memory      Use MemorySanitizer"
        $(ECHO) "  SAN=thread      Use ThreadSanitizer"
        $(ECHO) "  SAN=undefined   Use UndefiniedBehaviorSanitizer"
        $(ECHO)
        $(ECHO) "Optional Architecture"
        $(ECHO) "  ARCH=32         Make x86 build (-m32)"
        $(ECHO) "  ARCH=64         Make x64 build (-m64)"
        $(ECHO)
        $(ECHO) "Use Configuration File"
        $(ECHO) "  CONFIG=foo      Uses the configuration file 'foo.config'"
        $(ECHO) "                  in the root of this repository."
        $(ECHO) "  When unspecified, default.config will be used."

This is pretty straightforward. The first line (:code:`none: help` just defines
the :code:`help` target as the default.

The :code:`help:` target itself is comprised solely of echo statements, which
just print their text onto the terminal. If we expand out the :code:`$(ECHO)`
macro (and the :code:`$(NAME)` macro), substituting the macro's value for its
name in the code, the top "echo" line would be...

..  code-block:: bash

    @cmake -E echo "=== PawLIB (Static Library) ==="

If we were adapting this Makefile for another project, we would need to change
the title line, just below :code:`help:`. Otherwise, this is universal across
all our projects.

..  _buildc_makefiles_automating_clean:

Clean Targets
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Most Make users expect some standard targets for cleaning up build artifacts
and binaries. These targets are usually named :code:`clean`, :code:`cleandebug`,
and :code:`cleanrelease`.

..  code-block:: make

    clean:
        $(RM_DIR) $(BUILD_DIR)
        $(RM_DIR) $(TEMP_DIR)

    cleandebug:
        $(RM_DIR) $(BUILD_DIR)/Debug
        $(RM_DIR) $(TEMP_DIR)/Debug

    cleanrelease:
        $(RM_DIR) $(BUILD_DIR)/Release
        $(RM_DIR) $(TEMP_DIR)/Release

The :code:`clean:` target removes the :file:`build_temp` or :file:`lib`
(or :file:`bin`) directories that we defined in the :code:`BUILD_DIR` and
:code:`TEMP_DIR` variables earlier.

In the same way, the :code:`cleandebug:` and :code:`cleanrelease:` targets
remove their respective subdirectories from those directories. This is useful
if, for example, you want to rebuild the Debug version of the library, but
want to leave the Release version intact for comparison.

Remember, we're using variables and macros. If we were to perform all the
substitutions, this section would look like this...

..  code-block:: make

    clean:
        @cmake -E remove_directory lib
        @cmake -E remove_directory build_temp

    cleandebug:
        @cmake -E remove_directory lib/Debug
        @cmake -E remove_directory build_temp/Debug

    cleanrelease:
        @cmake -E remove_directory lib/Release
        @cmake -E remove_directory build_temp/Release

..  _buildc_makefiles_automating_build:

Invoking Builds
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Here's where the real magic happens!

..  code-block:: make

    debug:
        $(MK_DIR) $(TEMP_DIR)/Debug$(ARCH)
        $(CH_DIR) $(TEMP_DIR)/Debug$(ARCH) $(CMAKE) $(T_DEBUG) -DARCH=$(ARCH) -DSAN=$(SAN) $(P_CONF)$(P_CONF_PATH)
        $(EXEC_BUILD)/Debug$(ARCH) $(MAKE) VERBOSE=1

    release:
        $(MK_DIR) $(TEMP_DIR)/Release$(ARCH)
        $(CH_DIR) $(TEMP_DIR)/Release$(ARCH) $(CMAKE) $(T_RELEASE) -DARCH=$(ARCH) $(P_CONF)$(P_CONF_PATH)
        $(EXEC_BUILD)/Release$(ARCH) $(MAKE) VERBOSE=1

Both the :code:`debug:` and :code:`release:` targets do the same thing, just
for the Debug and Release builds respectively. We'll dig into :code:`debug:`
in more detail; you simply substitute "RELEASE" for "DEBUG" for the other
target.

First, we need to create the directory for building, which we do with
:code:`$(MK_DIR) $(TEMP_DIR)/Debug$(ARCH)`. If we expand that out using
our macros, it reads like this...

..  code-block:: bash

    @cmake -E make_directory build_temp/Debug

Of course, that last directory name might well be :file:`Debug32` or
:file:`Debug64` if the user specified an architecture with :code:`ARCH=32`
or :code:`ARCH=64`. If no architecture was specified, however, it'll just be
:file:`Debug`.

Next, we switch to that directory and invoke CMake. Expanding out that line,
and assuming :code:`ARCH=`, :code:`CONFIG=`, and :code:`SAN=` were not defined
by the user, we get...

..  code-block:: bash

    @cmake -E chdir build_temp/Debug cmake ../.. -G"Unix Makefiles" -DCMAKE_BUILD_TYPE=Debug -DARCH= -DSAN= -DCONFIG_FILENAME=default

That changes to the :file:`build_temp/Debug` directory we just created and
invokes CMake with the variables passed to it.

Finally, we call the generated Makefile itself. It's important how we do this!
Make defines two ways that one Makefile can call another; we are using
one of those ways here, and the other way in the outer-level Makefile we'll
see in a few moments. In both cases, we are using the built-in macro
:code:`$(MAKE)`, which allows the current Makefile to pass its flags and options
on automatically.

Also, note that we're using the macro :code:`EXEC_BUILD`, which we defined
earlier as the combination of :code:`CH_DIR` and :code:`TEMP_DIR`.

If we expand that last line out, we get...

..  code-block:: bash

    @cmake -E chdir build_temp/Debug $(MAKE) VERBOSE=1

That will run the generated Makefile, triggering the actual compile.

..  _buildc_makefiles_automating_details:

Last Details
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

At the bottom of our Makefile, we simply need to specify that we weren't
actually building anything with the cleaning or help targets.

..  code-block:: make

    .PHONY: clean cleandebug cleanrelease help

..  _buildc_makefiles_toplevel:

Top Level Makefile
-------------------------------------

The top-level Makefile, at :file:`pawlib/Makefile`, uses many of those same
principles. Its purpose is to perform the top-level automation that requires
calling both the :file:`pawlib` and :file:`pawlib-tester` Makefiles, as well as
the one provided with the Sphinx documentation (see :ref:`buildsphinx`.

..  _buildc_makefiles_toplevel_macros:

Macros
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We don't need quite so many variables in this Makefile, but we do need a few
additional macros for other CMake commands we'll be using.

..  code-block:: make

    MK_DIR = @cmake -E make_directory
    CH_DIR = @cmake -E chdir
    CP = @cmake -E copy
    CP_DIR = @cmake -E copy_directory
    RM = @cmake -E remove
    RM_DIR = @cmake -E remove_directory
    ECHO = @cmake -E echo
    LN = @cmake -E create_symlink

You'll recognize a number of those macros from earlier, but let's review them
all anyway:

* :code:`MK_DIR = @cmake -E make_directory` creates a new directory,

* :code:`CH_DIR = @cmake -E chdir` switches to a different directory,

* :code:`CP = @cmake -E copy` copies a file,

* :code:`CP_DIR = @cmake -E copy_directory` copies a directory,

* :code:`RM = @cmake -E remove` removes a file,

* :code:`RM_DIR = @cmake -E remove_directory` removes a directory,

* :code:`ECHO = @cmake -E echo` prints a message to the terminal,

* :code:`LN = @cmake -E create_symlink` creates a symbolic link (shortcut).

..  note:: :code:`create_symlink` only works on Unix systems, such as Linux,
    OpenBSD, and macOS. It won't work on non-Unix systems like Windows...but
    then, Makefiles won't work by default on Windows either.

..  _buildc_makefiles_toplevel_default:

Default Target and Help
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As before, we want to display the :code:`help:` target by default. Our help
is a little longer in this file.

You'll notice, we don't use the :code:`$(NAME)` variable in this Makefile.
This is because we have to change so many other things, such as directory and
compiled binary names, and project-specific instructions, we're better off
just rewriting this particular file to meet our needs.

..  code-block:: make

    none: help

    help:
        $(ECHO) "=== PawLIB 1.1 ==="
        $(ECHO) "Select a build target:"
        $(ECHO) "  make ready         Build PawLIB and bundles it for distribution."
        $(ECHO) "  make clean         Clean up PawLIB and Tester."
        $(ECHO) "  make cleandebug    Clean up PawLIB and Tester Debug."
        $(ECHO) "  make cleanrelease  Clean up PawLIB and Tester Release."
        $(ECHO) "  make docs          Generate HTML docs."
        $(ECHO) "  make docs_pdf      Generate PDF docs."
        $(ECHO) "  make pawlib        Build PawLIB as release."
        $(ECHO) "  make pawlib_debug  Build PawLIB as debug."
        $(ECHO) "  make tester        Build PawLIB Tester (+PawLIB) as release."
        $(ECHO) "  make tester_debug  Build PawLIB Tester (+PawLIB) as debug."
        $(ECHO) "  make all           Build everything."
        $(ECHO) "  make allfresh      Clean and rebuild everything."
        $(ECHO)
        $(ECHO) "Clang Sanitizers (requires Debug build and Clang.)"
        $(ECHO) "  SAN=address     Use AddressSanitizer"
        $(ECHO) "  SAN=leak        Use LeakSanitizer w/o AddressSanitizer (Linux only)"
        $(ECHO) "  SAN=memory      Use MemorySanitizer"
        $(ECHO) "  SAN=thread      Use ThreadSanitizer"
        $(ECHO) "  SAN=undefined   Use UndefiniedBehaviorSanitizer"
        $(ECHO)
        $(ECHO) "Optional Architecture"
        $(ECHO) "  ARCH=32         Make x86 build (-m32)"
        $(ECHO) "  ARCH=64         Make x64 build (-m64)"
        $(ECHO)
        $(ECHO) "Use Configuration File"
        $(ECHO) "  CONFIG=foo      Uses the configuration file 'foo.config'"
        $(ECHO) "                  in the root of this repository."
        $(ECHO) "  When unspecified, default.config will be used."
        $(ECHO)
        $(ECHO) "For other build options, see the 'make' command in 'docs/', 'pawlib-source/', and 'pawlib-tester/'.

..  _buildc_makefiles_toplevel_clean:

Clean Targets
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We want to allow the user to quickly clean up build artifacts and compiled
binaries from this top-level file.

..  code-block:: make

    clean:
        $(MAKE) clean -C pawlib-source
        $(MAKE) clean -C pawlib-tester
        $(RM) tester_debug
        $(RM) tester

    cleanall: clean
        $(MAKE) clean -C docs

    cleandebug:
        $(MAKE) cleandebug -C pawlib-source
        $(MAKE) cleandebug -C pawlib-tester
        $(RM) tester_debug

    cleanrelease:
        $(MAKE) cleanrelease -C pawlib-source
        $(MAKE) cleanrelease -C pawlib-tester
        $(RM) tester

You'll notice that we aren't actually many files ourselves. Rather, we are
asking the lower-level Makefiles to run their :code:`clean` targets
(or :code:`cleandebug`/:code:`cleanrelease`, as the case may be.)

You see here the other way of one Makefile invoking another.
We call :code:`$(MAKE)`, followed by the make target we want to call. All of
the flags and options from the call to the top-level Makefile are passed down
automatically by Make. We only need to use the special flag :code:`-C`, followed
by the name of the subdirectory containing the Makefile you're calling.

We do need to remove the symbolic links to the tester executables, which we'll
be discussing more in :ref:`buildc_makefiles_toplevel_build`.

You'll also notice that the :code:`cleanall:` target has a dependency; it calls
the :code:`clean` target before running its own commands.

..  _buildc_makefiles_toplevel_docs:

Building Docs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We want to provide a quick way to build the Sphinx documentation,
in either HTML or PDF formats.

..  code-block:: make

    docs:
        $(RM_DIR) docs/build/html
        $(MAKE) html -C docs
        $(ECHO) "-------------"
        $(ECHO) "<<<<<<< FINISHED >>>>>>>"
        $(ECHO) "View docs at 'docs/build/html/index.html'."
        $(ECHO) "-------------"

    docs_pdf:
        $(MAKE) latexpdf -C docs
        $(ECHO) "-------------"
        $(ECHO) "<<<<<<< FINISHED >>>>>>>"
        $(ECHO) "View docs at 'docs/build/latex/PawLIB.pdf'."
        $(ECHO) "-------------"

The first target, :code:`docs`, first cleans up the old HTML version of the
documentation, to ensure all files are rebuilt.

Beyond that, both of these targets basically do the same thing.
They call a target on :file:`docs/Makefile`, building either the
:code:`html` or :code:`latexpdf` versions of the Sphinx documentation.
Then, they print a nice little message on the terminal to announce that the
build is completed.

..  _buildc_makefiles_toplevel_build:

Building PawLIB
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We offer targets for the Release and Debug targets of PawLIB.
We generally assume that users of this Makefile implicitly want the
Release version (true of nearly all end users). Therefore, the target
:code:`pawlib:` will be for the Release, and we'll offer :code:`pawlib_debug`
for the Debug version.

..  code-block:: make

    pawlib:
        $(MAKE) release -C pawlib-source
        $(ECHO) "-------------"
        $(ECHO) "<<<<<<< FINISHED >>>>>>>"
        $(ECHO) "PawLIB is in 'pawlib-source/lib/Release'."
        $(ECHO) "-------------"

    pawlib_debug:
        $(MAKE) debug -C pawlib-source
        $(ECHO) "-------------"
        $(ECHO) "<<<<<<< FINISHED >>>>>>>"
        $(ECHO)  on "PawLIB is in 'pawlib-source/lib/Debug'."
        $(ECHO) "-------------"

We are calling the :code:`release` or :code:`debug` targets on
:file:`pawlib-source/Makefile`, using the :code:`-C` flag to change the
directory that Make is being run on.

We do the same thing for the testers, with a few major additions...

..  code-block:: make

    tester: pawlib
        $(MAKE) release -C pawlib-tester
        $(RM) tester
        $(LN) pawlib-tester/bin/Release/pawlib-tester tester
        $(ECHO) "-------------"
        $(ECHO) "<<<<<<< FINISHED >>>>>>>"
        $(ECHO) "PawLIB Tester is in 'pawlib-tester/bin/Release'."
        $(ECHO) "The link './tester' has been created for convenience."
        $(ECHO) "-------------"


    tester_debug: pawlib_debug
        $(MAKE) debug -C pawlib-tester
        $(RM) tester_debug
        $(LN) pawlib-tester/bin/Debug/pawlib-tester tester_debug
        $(ECHO) "-------------"
        $(ECHO) "<<<<<<< FINISHED >>>>>>>"
        $(ECHO) "PawLIB Tester is in 'pawlib-tester/bin/Debug'."
        $(ECHO) "The link './tester_debug' has been created for convenience."
        $(ECHO) "-------------"

First, you'll notice that :code:`pawlib` is a dependency of the :code:`tester`
target, and :code:`pawlib_debug` is a dependency of the :code:`tester_debug`
target. This is because we must build PawLIB *before* its tester.

As before, we call the :code:`release` or :code:`debug` target on
:file:`pawlib-tester/Makefile`.

Next, we want to create (or recreate) a symbolic link to the compiled tester.
This will appear in the root of the repository, as either :file:`tester`
or :file:`tester_debug`. We must first delete the existing symbolic link
(:code:`$(RM) tester`), and then create a new one
(:code:`$(LN) pawlib-tester/bin/Release/pawlib-tester tester`). If the link
did not exist when we tried to delete it, the :code:`$(RM)` command won't
do anything.

..  _buildc_makefiles_toplevel_ready:

Ready Target
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Now we're ready to tie everything together into a single magical command:
:code:`make ready`. This is intended to compile and bundle the library so
it can be immediately used by other projects.

Whatever our project is, and regardless of whether it is a library or
application, we want to place the files intended for distribution into a
directory with the same name as the project. In the case of PawLIB, this
directory is named :file:`pawlib/`.

..  code-block:: make

    ready: pawlib
        $(RM_DIR) pawlib
        $(ECHO) "Creating file structure..."
        $(MK_DIR) pawlib
        $(ECHO) "Copying PawLIB..."
        $(CP_DIR) pawlib-source/include/ pawlib/include/
        $(CP_DIR) pawlib-source/lib/Release/ pawlib/lib/
        $(ECHO) "Copying README and LICENSE..."
        $(CP) README.md pawlib/README.md
        $(CP) LICENSE.md pawlib/LICENSE.md
        $(ECHO) "-------------"
        $(ECHO) "<<<<<<< FINISHED >>>>>>>"
        $(ECHO) "The libraries are in 'pawlib'."
        $(ECHO) "-------------"

We obviously first compile the library, via the dependency (in this case,
:code:`pawlib`).

Next, we need to completely remove any previous version of the distribution
directory via :code:`$(RM_DIR) pawlib`.

Then we can create it again with :code:`$(MK_DIR) pawlib`.

Since this is a library, we need to copy the library's entire
:file:`include/` subdirectory to our distribution directory, via
:code:`$(CP_DIR) pawlib-source/include pawlib/`.
Then, we can copy the :file:`lib/Release` directory, which contains our
compiled library files (:code:`*.a` or :code:`*.so` files on Unix) to
:file:`pawlib/lib`.

..  note:: If we were building an application, instead of a library, we'd
    need to copy the :file:`bin/Release` subdirectory, instead of the
    :file:`include` and :file:`lib/Release` subdirectories.

Lastly, we copy the :file:`README.md` and :file:`LICENSE.md` files into our
distribution folder (:code:`$(CP) README.md pawlib/README.md` and
:code:`$(CP) LICENSE.md pawlib/LICENSE.md`), as these should be included
with all distributions of PawLIB.

Our project is now ready for use and distribution. Many of our projects look
for the :file:`pawlib` directory and link directly to it (see
:ref:`buildc_cmake_externconfig`). Otherwise, one might create a tarball of
this directory for distribution.

..  _buildc_overview:

Build System Overview
=====================================

We've covered an extraordinary amount of ground! To ensure everything makes
sense in context, let's review.

1. The top-level Makefile in a repository is run, e.g. :code:`make ready`.
   That runs...

2. The inner-level Makefile, which directly invokes...

3. CMake, which uses either the default or user-defined :code:`.config` file
   to build the code, using...

4. The C/C++ compiler and linker.

..  _buildc_overview_troubleshooting:

Troubleshooting Problems
-------------------------------------

Now that you know how things are *supposed* to work, you are also equipped
to debug problems. Here are a few tips:

* **Read the build system output, from the last line back.** More often than
  not, the exact nature of the problem will be described.

* When did the build stop working? Review the changes, especially to build
  system files.

* Are all the files being compiled listed in :file:`CMakeLists.txt`? This
  is the single most common reason for failures.

* Rule out problems with the code itself. These will always show up as
  errors or warnings.

* Are the indents in a :file:`Makefile` tabs? **Make does not support spaces
  for indentation.**

* Is a different version of the compiler being used? If so, try another.
  Once you isolate the exact compiler and version that fails, review the
  changelog for that compiler version.
