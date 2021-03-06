..  _cpp:

C++ and C Development Environment
###################################

We use C++ and C for most of our projects at MousePaw Media. This tutorial
will help you set up the development environment for working with those
languages on Linux.

..  _cpp_install_compiler:

Install the Compiler
============================

..  note:: Updated 10 April 2020

We use the Clang compiler primarily, and GCC secondarily. If you're not on a
Debian-based Linux system, you'll need to find out how to install these yourself.

Linux
----------------------------

We'll start by installing Clang and LLVM. We require Clang 3.4 or later, but
recommend 6.0 for all company developers. We'll be using
`LLVM's official stable apt repository <http://apt.llvm.org/>`_ instead of
using the older version in the default system repository.

Below are the instructions for adding and installing from the official
LLVM/Clang repository.

..  code-block:: bash

    $ sudo add-apt-repository "deb http://apt.llvm.org/`lsb_release -sc`/ llvm-toolchain-`lsb_release -sc` main"
    $ wget -O - http://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -
    $ sudo apt update
    $ sudo apt install libllvm-9-ocaml-dev libllvm9 llvm-9 llvm-9-dev llvm-9-doc llvm-9-examples llvm-9-runtime clang-9 clang-tools-9 clang-9-doc libclang-common-9-dev libclang-9-dev libclang1-9 clang-format-9 libfuzzer-9-dev lldb-9 lld-9 libc++-9-dev libc++abi-9-dev libomp-9-dev
    $ sudo ln -sf /usr/bin/llvm-symbolizer-9 /usr/bin/llvm-symbolizer

If you are upgrading from Clang 7, be sure to run...

..  code-block:: bash

    $ sudo apt remove -f clang-7 clang-7-doc libclang-common-7-dev libclang-7-dev libclang1-7 libllvm7 libllvm7 lldb-7 llvm-7 llvm-7-dev llvm-7-doc llvm-7-examples llvm-7-runtime clang-format-7 python-clang-7 libfuzzer-7-dev

If you are upgrading from Clang 6.0, be sure to run...

..  code-block:: bash

    $ sudo apt remove -f clang-6.0 clang-6.0-doc libclang-common-6.0-dev libclang-6.0-dev libclang1-6.0 libclang1-6.0-dbg libllvm6.0 libllvm6.0-dbg lldb-6.0 llvm-6.0 llvm-6.0-dev llvm-6.0-doc llvm-6.0-examples llvm-6.0-runtime clang-format-6.0 python-clang-6.0 libfuzzer-6.0-dev

If you are upgrading from Clang 5.0, be sure to run...

..  code-block:: bash

    $ sudo add-apt-repository --remove "deb http://apt.llvm.org/`lsb_release -sc`/ llvm-toolchain-`lsb_release -sc`-5.0 main"
    $ sudo apt remove -f clang-5.0 clang-5.0-doc libclang-common-5.0-dev libclang-5.0-dev libclang1-5.0 libclang1-5.0-dbg libllvm-5.0-ocaml-dev libllvm5.0 libllvm5.0-dbg lldb-5.0 llvm-5.0 llvm-5.0-dev llvm-5.0-doc llvm-5.0-examples llvm-5.0-runtime clang-format-5.0 python-clang-5.0 libfuzzer-5.0-dev

..  warning:: If you're using an operating system based on a version of
    Ubuntu before 16.04, you may not be able to compile our code with GCC.
    There was a major bug in the last version of GCC 5 for Ubuntu 14.04 which
    prevented our code from compiling.

We also try to ensure our code builds on GCC. You may use the version provided
by your distribution, as long as it is GCC 7 or later.

..  code-block:: bash

    $ sudo apt update
    $ sudo apt install gcc g++ gcc-doc

If you're on a 64-bit system, you'll need some additional packages...

..  code-block:: bash

    $ sudo apt-get install gcc-multilib g++-multilib libc6-dev-i386

Once you have everything installed, configure Ubuntu to allow you to switch
between GCC and Clang.

..  important:: If you already have this configured and know what you're doing,
    you should modify your `update-alternatives` yourself to better fit your
    own workflow.

If you aren't familiar with `update-alternatives`, we can *completely reset*
and configure this tool using the following commands.

..  warning:: The following will remove any existing `update-alternatives`
    configuration for `cc` and `c++`. This is **strongly** recommended if you
    followed this guide before.

..  code-block:: bash

    $ sudo update-alternatives --remove-all cc
    $ sudo update-alternatives --install /usr/bin/cc cc /usr/bin/clang-9 30
    $ sudo update-alternatives --install /usr/bin/cc cc /usr/bin/gcc 10
    $ sudo update-alternatives --remove-all c++
    $ sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang++-9 30
    $ sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++ 10

Once you've set all that up, you can easily switch between options using the
following command, substituting ``cc`` for ``c++`` if you're changing the C
compiler.

..  code-block:: bash

    $ sudo update-alternatives --config c++

You can generally just leave each on auto.

Our build systems all use the ``cc`` and ``c++`` commands for compiling, so
whatever you select for the compiler will be used.

..  warning:: If you use any drivers that require recompiling when you update
    the kernel, you will need to switch back to GCC before running updates!
    ``sudo update-alternatives --config cc`` and
    ``sudo update-alternatives --config c++``. Just be sure to switch to
    Clang before you start working with your code again.

Mac
----------------------------

We don't directly support macOS as a development environment. If you're using
macOS, you should install ``Command Line Tools for Xcode``,
which has Clang onboard.

Optionally, if you want to install the GCC compilers, you can do so using
Macports.

Windows
----------------------------

We don't directly support Windows as a development environment. If you're
using Windows, you'll need to use Clang or a GCC7-compatible C++ compiler
(such as MinGW). **We have no plans to support MSVC.**

* If you're using Windows 10, you may use the `Windows Subsystem for Linux <https://docs.microsoft.com/en-us/windows/wsl/install-win10>`_. (Recommended)
* You may use `MinGW <http://mingw.org/>`_ for C++ compiling.
* You can have `Visual Studio use the Clang compiler <https://blogs.msdn.microsoft.com/vcblog/2017/03/07/use-any-c-compiler-with-visual-studio/>`_.

If you're using the Windows Subsystem for Linux, you can follow the Linux
version of the instructions in this guide.

..  _cpp_install_devtools:

Install Development Tools
==================================

We use a number of coding tools, both command-line and otherwise.

**Required:**
* CMake 

**Recommended:**
* Valgrind
* KCachegrind
* Vim *or* Emacs

Linux
----------------------------

On Ubuntu, you can quickly install the whole batch (except the IDE), plus the
essential packages for compiling code, by running the following...

..  code-block:: bash

    $ sudo apt install build-essential cmake valgrind kcachegrind vim emacs cccc

You can install the same packages on most other versions of Linux.

Mac
----------------------------

By installing the Command Line Tools for Xcode, you should have most of the
essentials already installed.

Instead of KCachegrind, you may have an easier time installing QCachegrind,
which is practically identical.

Installing Via Macports
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: bash

    $ port install cmake emacs valgrind qcachegrind

Installing Via Homebrew
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: bash

    $ brew install cmake emacs valgrind qcachegrind

Alternative
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you prefer, you can download the official installer for CMake from
`<https://cmake.org/download/>`_.


Windows
----------------------------

Most of the development tools can be downloaded and installed. Please note,
Valgrind can only be run under certain circumstances.

* **CMake**: Official download from `<https://cmake.org/download/>`_.
* **Emacs**: Official download from `<https://www.gnu.org/software/emacs/download.html#windows>`_.
* **QCachegrind**: Unofficial binary download from `<https://sourceforge.net/projects/qcachegrindwin/>`_.
* **Valgrind**: Can only be installed via ``Windows Subsystem for Linux`` or ``MinGW``.
* **Vim**: Official download from `<https://www.vim.org/download.php/>`_

..  _cpp_install_ide:

Choosing an IDE
=========================

There are many IDEs available for the C++ and C languages. Personally, we
recommend and support :ref:`vscode`. Otherwise, there are plenty of other
options out there.

Optionally, you could just use a plain text editor and a terminal to work
with C++. (A couple of us do actually use Vim!)
