..  _cpp:

C++ and C Development Environment
###################################

We use C++ and C for most of our projects at MousePaw Media. This tutorial
will help you set up the development environment for working with those
languages on Linux.

..  _cpp_install_compiler:

Install the Compiler
============================

..  NOTE:: Updated 07 January 2019

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
    $ sudo apt install python-lldb-7
    $ sudo apt install libllvm-7-ocaml-dev libllvm7 llvm-7 llvm-7-dev llvm-7-doc llvm-7-examples llvm-7-runtime clang-7 clang-tools-7 clang-7-doc libclang-common-7-dev libclang-7-dev libclang1-7 clang-format-7 libfuzzer-7-dev lldb-7 lld-7 libc++-7-dev libc++abi-7-dev libomp-7-dev
    $ sudo ln -sf /usr/bin/llvm-symbolizer-7 /usr/bin/llvm-symbolizer
    $ sudo ln -sf /usr/bin/lldb-server-7 /usr/lib/llvm-7/bin/lldb-server-7.0.0

If you are upgrading from 6.0, be sure to run...

..  code-block:: bash

    sudo apt remove -f clang-6.0 clang-6.0-doc libclang-common-6.0-dev libclang-6.0-dev libclang1-6.0 libclang1-6.0-dbg libllvm6.0 libllvm6.0-dbg lldb-6.0 llvm-6.0 llvm-6.0-dev llvm-6.0-doc llvm-6.0-examples llvm-6.0-runtime clang-format-6.0 python-clang-6.0 libfuzzer-6.0-dev

If you are upgrading from 5.0, be sure to run...

..  code-block:: bash

    $ sudo add-apt-repository --remove "deb http://apt.llvm.org/`lsb_release -sc`/ llvm-toolchain-`lsb_release -sc`-5.0 main"
    $ sudo apt remove -f clang-5.0 clang-5.0-doc libclang-common-5.0-dev libclang-5.0-dev libclang1-5.0 libclang1-5.0-dbg libllvm-5.0-ocaml-dev libllvm5.0 libllvm5.0-dbg lldb-5.0 llvm-5.0 llvm-5.0-dev llvm-5.0-doc llvm-5.0-examples llvm-5.0-runtime clang-format-5.0 python-clang-5.0 libfuzzer-5.0-dev

..  WARNING:: If you're using an operating system based on a version of
    Ubuntu before 16.04, you may not be able to compile our code with GCC.
    There was a major bug in the last version of GCC5 for Ubuntu 14.04 which
    prevented our code from compiling.

We also try to ensure our code builds on GCC. Although GCC is available through
the core repositories, we like using the latest stable compiler builds.

..  code-block:: bash

    $ sudo apt update
    $ sudo apt install gcc g++ gcc-doc

If you're on a 64-bit system, you'll need some additional packages...

..  code-block:: bash

    $ sudo apt-get install gcc-multilib g++-multilib libc6-dev-i386

Once you have everything installed, configure Ubuntu to allow you to switch
between GCC and Clang.

..  IMPORTANT:: If you already have this configured and know what you're doing,
    you should modify your `update-alternatives` yourself to better fit your
    own workflow.

If you aren't familiar with `update-alternatives`, we can *completely reset*
and configure this tool using the following commands.

..  WARNING:: The following will remove any existing `update-alternatives`
    configuration for `cc` and `c++`. This is **strongly** recommended if you
    followed this guide before.

..  code-block:: bash

    $ sudo update-alternatives --remove-all cc
    $ sudo update-alternatives --install /usr/bin/cc cc /usr/bin/clang-7 30
    $ sudo update-alternatives --install /usr/bin/cc cc /usr/bin/gcc 10
    $ sudo update-alternatives --remove-all c++
    $ sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang++-7 30
    $ sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++ 10

Once you've set all that up, you can easily switch between options using the
following command, substituting ``cc`` for ``c++`` if you're changing the C
compiler.

..  code-block:: bash

    $ sudo update-alternatives --config c++

You can generally just leave each on auto.

Our build systems all use the ``cc`` and ``c++`` commands for compiling, so
whatever you select for the compiler will be used.

Mac
----------------------------

If you're on a Mac, you should install ``Command Line Tools for Xcode``,
which has Clang onboard.

Optionally, if you want to install the GCC compilers, you can do so using
Macports.

Windows
----------------------------

We don't directly support Windows as a development environment. If you're
using Windows, you'll need to use Clang or a GCC5-compatible C++ compiler
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
