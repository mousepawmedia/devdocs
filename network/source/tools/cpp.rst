..  _cpp:

C++ and C Development Environment
###################################

We use C++ and C for most of our projects at MousePaw Media. This tutorial
will help you set up the development environment for working with those
languages on Linux.

..  _cpp_install_compiler:

Install the Compiler
============================

..  NOTE:: Updated 19 May 2018

We use the Clang compiler primarily, and GCC secondarily. If you're not on a
Debian-based Linux system, you'll need to find out how to install these yourself.

We don't directly support Windows as a development environment. If you're
using Windows, you'll need to use Clang or a GCC5-compatible C++ compiler
(such as MinGW). **We have no plans to support MSVC.**

If you're on a Mac, you should install the Command Line Tools for Xcode,
which has Clang onboard.

..  WARNING:: If you're using an operating system based on a version of
    Ubuntu before 16.04, you may not be able to compile our code with GCC.
    There was a major bug in the last version of GCC5 for Ubuntu 14.04 which
    prevented our code from compiling.

We'll start by installing Clang and LLVM. We require Clang 3.4 or later, but
recommend 5.0 for all company developers. We'll be using
`LLVM's official stable apt repository <http://apt.llvm.org/>`_ instead of
using the older version in the default system repository.

Below are the instructions for adding and installing from the official
LLVM/Clang repository.

..  IMPORTANT:: If you're using a version of Ubuntu other than 18.04, be sure to change
    the `add-apt-repository` line to your version (instead of `bionic`).

..  code-block:: bash

    $ sudo add-apt-repository "deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-5.0 main"
    $ wget -O - http://apt.llvm.org/llvm-snapshot.gpg.key|sudo apt-key add -
    $ sudo apt update
    $ sudo apt install python-lldb-5.0
    $ sudo apt install clang-5.0 clang-5.0-doc libclang-common-5.0-dev libclang-5.0-dev libclang1-5.0 libclang1-5.0-dbg libllvm5.0 libllvm5.0-dbg lldb-5.0 llvm-5.0 llvm-5.0-dev llvm-5.0-doc llvm-5.0-examples llvm-5.0-runtime clang-format-5.0 python-clang-5.0 libfuzzer-5.0-dev
    $ sudo ln -sf /usr/bin/llvm-symbolizer-5.0 /usr/bin/llvm-symbolizer
    $ sudo ln -sf /usr/bin/lldb-server-5.0 /usr/lib/llvm-5.0/bin/lldb-server-5.0.0

If you are upgrading from 4.0, be sure to run...

..  code-block:: bash

    $ sudo apt remove clang-4.0 clang-4.0-doc libclang-common-4.0-dev libclang-4.0-dev libclang1-4.0 libclang1-4.0-dbg libllvm-4.0-ocaml-dev libllvm4.0 libllvm4.0-dbg lldb-4.0 llvm-4.0 llvm-4.0-dev llvm-4.0-doc llvm-4.0-examples llvm-4.0-runtime clang-format-4.0 python-clang-4.0 libfuzzer-4.0-dev

We also try to ensure our code builds on GCC. Although GCC is available through
the core repositories, we like using the latest stable compiler builds. You can
install those via...

..  code-block:: bash

    $ sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    $ sudo apt update
    $ sudo apt install gcc g++ gcc-7 g++-7 gcc-7-doc

..  NOTE:: Many Ubuntu systems older than 16.04 can still install the correct
    version via the PPA `ppa:ubuntu-toolchain-r/test`.

If you're on a 64-bit system, you'll need some additional packages...

..  code-block:: bash

    $ sudo apt-get install gcc-7-multilib g++-7-multilib libc6-dev-i386

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
    $ sudo update-alternatives --install /usr/bin/cc cc /usr/bin/clang-5.0 30
    $ sudo update-alternatives --install /usr/bin/cc cc /usr/bin/gcc-7 20
    $ sudo update-alternatives --install /usr/bin/cc cc /usr/bin/gcc 10
    $ sudo update-alternatives --remove-all c++
    $ sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang++-5.0 30
    $ sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++-7 20
    $ sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++ 10

Once you've set all that up, you can easily switch between options using the
following command, substituting ``cc`` for ``c++`` if you're changing the C
compiler.

..  code-block:: bash

    $ sudo update-alternatives --config c++

You can generally just leave each on auto.

Our build systems all use the ``cc`` and ``c++`` commands for compiling, so
whatever you select for the compiler will be used.

..  _cpp_install_devtools:

Install Development Tools
==================================

We use a number of coding tools, both command-line and otherwise. You can
quickly install the whole batch (except the IDE) by running the following...

..  code-block:: bash

    $ sudo apt install build-essential cmake valgrind kcachegrind vim cccc

..  _cpp_install_ide:

Choosing an IDE
=========================

There are many IDEs available for the C++ and C languages. Personally, we
recommend and support :ref:`vscode` or :ref:`codeblocks`. Otherwise, there
are plenty of other options out there.

Optionally, you could just use a plain text editor and a terminal to work
with C++. (A couple of us do actually use Vim!)