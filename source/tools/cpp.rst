..  _cpp:

C++ and C Development Environment
###################################

We use C++ and C for most of our projects at MousePaw Media. This tutorial
will help you set up the development environment for working with those
languages on Linux.

..  _cpp_install_compiler:

Install the Compiler
============================

..  note:: Updated 18 November 2023

We use the Clang compiler primarily, and GCC secondarily. If you're not on a
Debian-based Linux system, you'll need to find out how to install these yourself.

Linux
----------------------------

We'll start by installing Clang and LLVM. We require Clang 3.4 or later, but
recommend 16.0 for all company developers. We'll be using
`LLVM's official stable apt repository <https://apt.llvm.org/>`_ instead of
using the older version in the default system repository.

Below are the instructions for adding and installing from the official
LLVM/Clang repository.

..  code-block:: bash

    wget -qO- https://apt.llvm.org/llvm-snapshot.gpg.key | sudo tee /etc/apt/trusted.gpg.d/apt.llvm.org.asc
    sudo add-apt-repository "deb http://apt.llvm.org/`lsb_release -sc`/ llvm-toolchain-`lsb_release -sc`-16 main"
    sudo apt install libllvm-16-ocaml-dev libllvm16 llvm-16 llvm-16-dev llvm-16-doc llvm-16-examples llvm-16-runtime clang-16 clang-tools-16 clang-16-doc libclang-common-16-dev libclang-16-dev libclang1-16 clang-format-16 lldb-16 lld-16 libc++-16-dev libc++abi-16-dev libomp-16-dev
    sudo ln -sf /usr/bin/llvm-symbolizer-16 /usr/bin/llvm-symbolizer

..  warning:: If you're using an operating system based on a version of
    Ubuntu before 16.04, you may not be able to compile our code with GCC.
    There was a major bug in the last version of GCC 5 for Ubuntu 14.04 which
    prevented our code from compiling.

We also try to ensure our code builds on GCC. You may use the version provided
by your distribution, as long as it is GCC 7 or later.

..  code-block:: bash

    sudo apt update
    sudo apt install gcc g++ gcc-doc

If you're on a 64-bit system, you'll need some additional packages...

..  code-block:: bash

    sudo apt-get install gcc-multilib g++-multilib libc6-dev-i386

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

    sudo update-alternatives --remove-all cc
    sudo update-alternatives --install /usr/bin/cc cc /usr/bin/clang-16 30
    sudo update-alternatives --install /usr/bin/cc cc /usr/bin/gcc 10
    sudo update-alternatives --remove-all c++
    sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang++-16 30
    sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++ 10

Once you've set all that up, you can easily switch between options using the
following command, substituting ``cc`` for ``c++`` if you're changing the C
compiler.

..  code-block:: bash

    sudo update-alternatives --config c++

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

While we do not directly support Windows operating systems, if you're using
Windows 10 or Windows 11, you can use the `Windows Subsystem for Linux <https://learn.microsoft.com/en-us/windows/wsl/install>`_.

If you choose to use the Windows Subsystem for Linux, you can generally follow
our instructions for Ubuntu Linux.

..  note:: If you use the Windows Subsystem for Linux, you can also run some
    graphical Linux applications, by following `this guide from HowToGeek <https://www.howtogeek.com/261575/how-to-run-graphical-linux-desktop-applications-from-windows-10s-bash-shell/>`_.

There are two alternative approaches, although we do not provide technical
support for them:

- You may use `MinGW-w64 <https://www.mingw-w64.org/>`_ for C++ compiling.
- You can have `Visual Studio use the Clang compiler <https://devblogs.microsoft.com/cppblog/use-any-c-compiler-with-visual-studio/>`_.

..  _cpp_install_devtools:

Install Development Tools
==================================

We use a number of coding tools, both command-line and otherwise.

-*Required:**
- CMake

-*Recommended:**
- Valgrind
- KCachegrind
- Vim *or* Emacs

Linux
----------------------------

On Ubuntu, you can quickly install the whole batch (except the IDE), plus the
essential packages for compiling code, by running the following...

..  code-block:: bash

    sudo apt install build-essential cmake cppcheck valgrind kcachegrind vim emacs cccc

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

    port install cmake emacs valgrind qcachegrind

Installing Via Homebrew
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: bash

    brew install cmake emacs valgrind qcachegrind

Alternative
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you prefer, you can download the official installer for CMake from
`<https://cmake.org/download/>`_.


Windows
----------------------------

Most of the development tools can be downloaded and installed. Please note,
Valgrind can only be run under certain circumstances.

- **CMake**: Official download from `<https://cmake.org/download/>`_.
- **Emacs**: Official download from `<https://www.gnu.org/software/emacs/download.html#windows>`_.
- **QCachegrind**: Unofficial binary download from `<https://sourceforge.net/projects/qcachegrindwin/>`_.
- **Valgrind**: Can only be installed via ``Windows Subsystem for Linux`` or ``MinGW``.
- **Vim**: Official download from `<https://www.vim.org/download.php/>`_

..  _cpp_install_ide:

Choosing an IDE
=========================

There are many IDEs available for the C++ and C languages. Personally, we
recommend and support :ref:`vscode`. Otherwise, there are plenty of other
options out there.

Optionally, you could just use a plain text editor and a terminal to work
with C++. (A couple of us do actually use Vim!)
