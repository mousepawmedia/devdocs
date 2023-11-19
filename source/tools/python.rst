.. _python:

Python3 Development Environment
######################################

We use Python 3.10 (or later) for a lot of our application and GUI development.
We recommend :ref:`vscode` as your main Python IDE.

.. _python_installing:

Installing Python
===========================

Installing Python on Linux
---------------------------------

Operating systems based on Ubuntu 22.04 or later ship with Python 3.10 or later
by default. It is included in the installation command below anyway, just
in case you've removed it, or are on a system where it isn't installed
by default.

..  important:: If you type in the following commands, make sure you scroll and
    get the entire command. If you prefer to copy and paste, triple-click the third
    command so you get the whole thing.

We'll also install the libraries and development tools we use for Python3.

..  code-block:: bash

    sudo apt update
    sudo apt install python3 python3-pip python3-virtualenv python-dev python3-dev virtualenv

..  warning:: Do not **ever** use :code:`sudo pip`. If you want to install
    packages via pip, use a virtual environment.

Installing Python on Mac
---------------------------------

We can install Python and everything else we need using Homebrew or MacPorts.

Installing Via MacPorts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: bash

    sudo port install python310 py310-pip py310-virtualenv
    sudo port select --set python python310
    sudo port select --set pip py310-pip
    sudo port select --set virtualenv py310-virtualenv

Installing Via Homebrew
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: bash

    brew install python@3.10
    pip install virtualenv

Installing Python on Windows
---------------------------------

Download and install the latest version of Python 3.6 from
`<https://www.python.org/downloads/windows/>`_.

Once that is installed, start your Command Prompt, and run the following
command to install the rest of what we need.

..  code-block:: batch

    python -m pip install --upgrade pip wheel setuptools
    python -m pip install virtualenv

.. _python_ide:

Choosing an IDE
=========================

There are quite a few IDEs and editors for the Python language. We personally
recommend :ref:`vscode`.

If you want something else, there are dozens of other options,
including PyCharm Community Edition, Brackets, EricIDE, and Spyder.
