.. _python:

Python3 Development Environment
######################################

We use Python 3.7 (or later) for a lot of our application and GUI development.
PySide2 is our GUI toolkit.

We recommend either VSCode or Atom as our main Python IDE, although
you can use whatever you're most comfortable with.

.. _python_installing:

Installing Python
===========================

Installing Python on Linux
---------------------------------

Operating systems based on Ubuntu 16.04 or later ship with Python 3.7 or later
by default. It is included in the installation command below anyway, just
in case you've removed it, or are on a system where it isn't installed
by default.

..  IMPORTANT:: If you type in the following commands, make sure you scroll and
    get the entire command. If you prefer to copy and paste, triple-click the third
    command so you get the whole thing.

We'll also install the libraries and development tools we use for Python3.

..  code-block:: bash

    $ sudo apt update
    $ sudo apt install python3 python3-pip python3-virtualenv pylint3 python-dev python3-dev virtualenv

..  WARNING:: Do not **ever** use :code:`sudo pip`. If you want to install
    packages via pip, use a virtual environment.

Installing Python on Mac
---------------------------------

We can install Python and everything else we need using Homebrew or MacPorts.

Installing Via MacPorts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: bash

    $ sudo port install python37 py37-pip py37-virtualenv py37-pylint
    $ sudo port select --set python python37
    $ sudo port select --set pip py37-pip
    $ sudo port select --set virtualenv py37-virtualenv
    $ sudo port select --set pylint py37-pylint

Installing Via Homebrew
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: bash

    $ brew install python
    $ pip install virtualenv pylint

Installing Python on Windows
---------------------------------

Download and install the latest version of Python 3.6 from
`<https://www.python.org/downloads/windows/>`_.

Once that is installed, start your Command Prompt, and run the following
command to install the rest of what we need.

..  code-block:: batch

    python -m pip install --upgrade pip wheel setuptools
    python -m pip install virtualenv pylint

.. _python_pyside2:

Installing PySide2
=============================

Installing PySide2 on Linux or Mac
------------------------------------

We'll create a new virtual environment for PySide2. You can reuse these
instructions to create a different virtual environment for any project
you're working on using PySide2, but this provides a good general testing
ground.

In your terminal, run the following...

..  code-block:: bash

    $ python3.7 -m venv pyside2-venv
    $ source ~/pyside2-venv/bin/activate
    $ pip install pyside2
    $ deactivate

Any time you want to use that virtual environment, just run
:code:`source ~/pyside2-venv/bin/activate` in the directory containing the
virtual environment's folder. The command :code:`deactivate` exits the virtual
environment.

`SOURCE: Qt For Python/Getting Started <https://wiki.qt.io/Qt_for_Python/GettingStarted>`_

Installing PySide2 on Windows
-------------------------------

We'll create a new virtual environment for PySide2. You can reuse these
instructions to create a different virtual environment for any project
you're working on using PySide2, but this provides a good general testing
ground.

In your Command Prompt, run the following...

..  code-block:: batch

    python3.7 -m venv pyside2-venv
    pyside2-venv\Scripts\activate.bat
    pip install pyside2
    venv\Scripts\deactivate.bat

Any time you want to use that virtual environment, just run
:code:`pyside2-venv\Scripts\activate.bat` in the directory containing the
virtual environment's folder. The command :code:` venv\Scripts\deactivate.bat`
exits the virtual environment.

.. _python_ide:

Choosing an IDE
=========================

There are quite a few IDEs and editors for the Python language. We personally
recommend :ref:`vscode`.

If you want something else, there are dozens of other options,
including PyCharm Community Edition, Brackets, EricIDE, and Spyder.
