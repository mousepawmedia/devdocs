..  _buildc:

C/C++ Build Systems
###################################

We have developed a hybrid build system for all our C and C++ projects, which
simplifies the build process without sacrificing portability or control. It
consists of several pieces:

* **CMake** is used for the heavy lifting. Our projects are configured to take
  full advantage of CMake's features, meaning that users familiar with the
  system can do custom builds.

* **Makefiles** are used to automate the most common build processes on UNIX
  systems. This is useful because, while CMake is powerful, using it is a
  fairly involved process. Since many users may not know CMake that well,
  Makefiles serve as a beginner-friendly wrapper, providing one-command builds.

* **Clang** is our primary compiler, which we use because of its superior
  feature set and portability. However, we also support **GCC**, which is the
  standard on Linux.

..  WARNING:: We do *not* support the Microsoft Visual C++ [MSVC] compiler,
    and have no plans to officially support it in the future. Both Clang and
    GCC can be used to compile software on Windows.

To best understand this, we will start from the bottom of the stack and work
our way up the layers of abstraction.

Understanding Libraries
=====================================

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
**increase the liklihood of user-end errors** (dynamic linking). Some
problems with DLLs on Windows can be mitigated by shipping the right versions
of the libraries with the application, and placing them all in the install
directory. However, on Linux, the analogous `.so` files are usually shared
between programs, so ensuring a consistent version can be far more difficult.

Structure of the Repository
=====================================

Every C/C++ project repository follows the same structure.

..  NOTE:: Folders marked with (*) are untracked in the Git repository

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
    │   ├── library.cbp ← The CodeBlocks project for the library.
    │   └── Makefile ← The Makefile that automatically runs CMake.
    ├── library-tester ← The library tester.
    │   ├── bin (*) ← The compiled tester.
    │   ├── build_temp (*) ← Temporary build stuff. Also where CMake is run from.
    │   ├── include ← The tester's header files (.hpp).
    │   ├── src ← The tester's implementation files (.cpp).
    │   ├── CMakeLists.txt ← The CMake build instructions for the tester.
    │   ├── library-tester.cbp ← The CodeBlocks project for the tester.
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
have their own :file:`Makefile`, :file:`CMakeLists.txt`, and `*.cbp`
(CodeBlocks project) files. We'll be discussing these files later.

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
    │   ├── project.cbp ← The CodeBlocks project for the project.
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

..  NOTE:: Technically, when compiling a static library, things stop here.
    The `*.a` or `*.lib` is just an archive of object code.

The **linker** now fills in those blanks. It also completes the linking for
the static libraries, and brings the appropriate code into the finished
result. Errors relating to undefined references usually originate at this step.

Besides this, the linker may also rearrange things to make the program run
better, and to make the operating system happy. The end result is the compiled
executable or dynamic library.

..  NOTE:: We can force the compiler to stop anywhere in this process!
    `-E` stops after preprocessing, `-S` after compiling, and `-c` after
    assembling.

SOURCES:

* `The C++ compilation process (Northern Illinois University) <http://faculty.cs.niu.edu/~mcmahon/CS241/Notes/compile.html>`_

* `How does the compilation/linking process work? (StackOverflow) <https://stackoverflow.com/questions/6264249/how-does-the-compilation-linking-process-work>`_

* `Beginner's Guide to Linkers (LurkLurk) <http://www.lurklurk.org/linkers/linkers.html>`_

The Standard Libraries
-------------------------------------

Two critical dynamic libraries are the **C standard library** and
**C++ standard library**. Nearly every operating system comes with a version of
these libraries, and note the word "version". Different operating systems have
different implementations of the libraries, and the exact version sometimes
varies from one operating system version to the next.

The C Standard Library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The C standard library is usually an inherent part of the operating system,
in dynamic library form, although you may sometimes need to install the headers
separately. (On Linux, you don't.)

* On Linux (and some other UNIX systems), GNU's `glibc` provides the C standard
  library. It is installed by default.

* On Microsoft Windows, the Microsoft C run-time library is part of Microsoft
  Visual C++.

* On Mac OS X, the C standard library, the system file `libSystem.dylib`
  provides the C standard library.

* On BSD systems, BSD's own `libc` provides the C standard library.

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

..  NOTE:: The **C++ standard library** is *NOT* the same thing as the
    :abbr:`STL (standard template library)`. The STL is specifically the part
    of the C++ standard library that has to do with containers and algorithms.
    It was originally developed separately, and later integrated into the
    larger standard library. The two terms are often confused.
    (`SOURCE: What is the STL? <https://stackoverflow.com/a/827431/472647>`_)

GNU vs. LLVM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It is important to note that, on Linux, one is not **required** to use
`libstdc++`. It is, in fact, possible to install and use LLVM's `libc++`,
although this often requires compiling it from source. Since this is a dynamic
library, if you compile against `libc++` on Linux, you *will* need to ensure
end-users also have it on their systems.

Aside from this, it is perfectly possible to use both `libstdc++` and `libc++`
in the same environment, because the mangled names for the two are actually
different.

There are several advantages LLVM's `libc++` has over GNU's `libstdc++`:

* `libc++` has a signficiantly cleaner, better-designed modern code base.

* The entire LLVM toolchain, including `libc++`, is thoroughly documented.

* We get access to additional tools and optimizations in the LLVM Clang
  compiler toolchain.

Ideally, we want to use `libc++` as much as possible. However, because it
is not always practical to provide this dynamic library to end users, we much
also be able to compile against `libstdc++`.

Compiler Commands
-------------------------------------

..  NOTE:: You should bookmark the `official documentation for Clang <https://clang.llvm.org/docs/>`_

We rarely need to compile things manually, but it is helpful to know
*how* regardless. Let's break down the compiler commands that our build
system automatically generates. Here's the Debug target compile line::

    /usr/bin/clang++-5.0   -I/home/user/Code/Repositories/pawlib/pawlib-source/include -I/home/user/Code/Repositories/pawlib/pawlib-source/../../libdeps/libs/include  -g   -Wall -Wextra -Werror -std=c++14 -o CMakeFiles/pawlib.dir/src/binconv.cpp.o -c /home/user/Code/Repositories/pawlib/pawlib-source/src/binconv.cpp

And here is the Release target compile line::

    /usr/bin/clang++-5.0   -I/home/jason/Code/Repositories/pawlib/pawlib-source/include -I/home/jason/Code/Repositories/pawlib/pawlib-source/../../libdeps/libs/include  -O3 -DNDEBUG   -Wall -Wextra -Werror -std=c++14 -o CMakeFiles/pawlib.dir/src/binconv.cpp.o -c /home/jason/Code/Repositories/pawlib/pawlib-source/src/binconv.cpp

Those are ugly, long, scary lines of code (as well as specific to my system),
so let's break them down and tame them.

Compiler Path
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

First, we invoke the compiler itself. This command varies depending which
compiler you're using, and where it's located::

    /usr/bin/clang++-5.0

Many times, you'll actually see :code:`/usr/bin/c++`. If the user set up
their compilers following our Network Documentation, they probably used
:code:`sudo update-alternatives`, which creates aliases that can be quickly
and easily switched between Clang or GCC (or whatever other compiler).

..  NOTE:: If you're not sure which compiler is being used by
    :code:`update-alternatives`, run :code:`sudo update-alternatives --config c++`
    and note which option is selected. Replace `c++` with `cc` in that command
    to switch the C compiler.

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

C++ Standard
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Next, we need to specify the C++ standard we are using. This affects compiler
errors and warnings - some things that are allowed in C++14 aren't permitted in
C++11, and so forth. If we omit this flag, the compiler will use its default
standard, and what standard that is varies by compiler and version! Thus, it is
always better to just specify standard explicitly::

    -std=c++14

Debugging Symbols
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In the case of the Debug version of our project, we need to ask the compiler
to add **debugging symbols** to the code. This adds additional code that
aids debugging tools::

    -g

If you're ever using a code analysis or debugging tool, and you're seeing
raw memory addresses instead of variable and function names, you almost
certainly forgot this flag.

When we're compiling with debugging symbols, we must be sure our optimation
level is not above :code:`-O0` (see next section).

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

* :code:`-O2`: Enables most optimiziations.

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

Input and Output Paths
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Now we can actually generate the object files from our code. We specify our
output path with :code:`-o`, and our input path with :code:`-c`::

    -o CMakeFiles/pawlib.dir/src/binconv.cpp.o -c /home/user/Code/Repositories/pawlib/pawlib-source/src/binconv.cpp

Once again, these particular paths were generated by CMake.

We must compile each implementation file (`*.c` and `*.cpp`) in our project
to a corresponding object file.

SOURCES:

* `Clang command line argument reference (Clang Documentation) <https://clang.llvm.org/docs/ClangCommandLineReference.html>`_

* `Code Generation Options (Clang Documentation) <https://clang.llvm.org/docs/CommandGuide/clang.html#code-generation-options>`_

* `Diagnostic flags in Clang (Clang Documentation) <https://clang.llvm.org/docs/DiagnosticsReference.html>`_

* `Does "-dndebug" do anything in g++? (StackOverflow) <https://stackoverflow.com/a/24257232/472647>`_

..  _buildc_ld:

Linker Commands
-------------------------------------

Once we have our `*.o` or `*.obj` files, we can link them together to generate
the final product.

..  NOTE:: Remember that we don't link a static library! Rather, we generate
    an archive of the object files. (See :ref:`buildc_ar`)

Let's look at the linker commands automatically generated by CMake. Here's
the one for a Debug target::

    /usr/bin/clang++-5.0  -g   -fuse-ld=lld -rdynamic CMakeFiles/pawlib-tester.dir/main.cpp.o CMakeFiles/pawlib-tester.dir/src/TestSystem.cpp.o  -o ../../bin/Debug/pawlib-tester ../../../pawlib-source/lib/Debug/libpawlib.a ../../../../libdeps/libs/lib/libcpgf.a

And here is one for a Release target::

    /usr/bin/c++  -O3 -DNDEBUG   -fuse-ld=lld -rdynamic CMakeFiles/pawlib-tester.dir/main.cpp.o CMakeFiles/pawlib-tester.dir/src/TestSystem.cpp.o  -o ../../bin/Release/pawlib-tester ../../../pawlib-source/lib/Debug/libpawlib.a ../../../../libdeps/libs/lib/libcpgf.a

Note, we are once again invoking our *compiler* program. However, the commands
tell it to call the linker in this step. In reality, it is going to invoke
either the program :code:`ld` (GCC's linker) or :code:`lld`
(LLVM's linker).

By default, :code:`ld` will always be used on Linux. To use :code:`lld` in
that situation...

1. We must be using the Clang compiler.

2. We must have the corresponding version of :code:`lld` installed in
   :path:`/usr/bin`. If we are using :code:`clang++-5.0`, we must have
   :code:`/usr/bin/lld-5.0` installed as well. You can check for this
   with the command :code:`whereis lld-5.0`, and ensuring the expected
   path is returned.

3. We must pass the flag :code:`-fuse-ld=lld` to Clang when we invoke the
   linker.

Aside from this flag and a couple other technical details, :code:`ld` and
:code:`lld` have exactly the same usage.

Debugging Symbols
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The flag :code:`-g` is ignored by the :code:`ld` and :code:`lld` linkers
altogether, and is only accepted for compatibility with other tools.

:code:`-rdynamic` is important to debugging, as it ensures all the symbols
needed by debugging are put in the proper places by the linker. (That's
actually over-summarizing the technical explaination, so see the GCC docs
I linked to below if you're curious.)

Optimization Levels
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The flags :code:`-O0`, :code:`-O1`, :code:`-O2`, and :code:`-O3` work in
much the same way with the linker as with the compiler. With :code:`ld`,
they only affect certain types of libraries, but future versions may offer
further optimizations.

In short, it may not *help* your particular code, but it can't *hurt*, so just
assume there's a point to it and include the appropriate flag.

..  NOTE:: I was unable to find documentation on if or how :code:`lld` handles
    optimization flags differently from :code:`ld`. It is safest to assume
    it will be identical.

As with the compiler, we also have the flag :code:`-DNDEBUG`, which functions
in the same capacity here as it does there.

Input and Output
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After our other flags, we specify the file path to each of our object files.
Then, we specify our output with :code:`-o`. This part of the earlier commands
looks like the following::

    CMakeFiles/pawlib-tester.dir/main.cpp.o CMakeFiles/pawlib-tester.dir/src/TestSystem.cpp.o  -o ../../bin/Release/pawlib-tester

Finally, we must specify the file paths the `*.a` files for any and all static
libraries we are linking to.

Remember, **the order is important here!** The linker will resolve these
external dependencies *in order*, from left to right. If B depends on A, but you
list them in the order "A B", then the linker will be unable to link A to B,
and the linking will fail with one or more "undefined reference" errors.

This part of the commands looks like this::

    ../../../pawlib-source/lib/Debug/libpawlib.a ../../../../libdeps/libs/lib/libcpgf.a

SOURCES

* `LLD - The LLVM Linker (LLVM) <http://releases.llvm.org/5.0.0/tools/lld/docs/index.html>`_

* `Using the GNU Compiler Collection: Link Options (GCC) <https://gcc.gnu.org/onlinedocs/gcc/Link-Options.html>`_

..  _buildc_ar:

Archiver Command
-------------------------------------

Automating with CMake
=====================================

Simplifying with Makefiles
=====================================
