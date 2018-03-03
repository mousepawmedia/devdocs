.. _python:

Python3 Development Environment
######################################

We use the latest version of Python 3 for a lot of our application
and GUI development. Kivy is our GUI toolkit.

We recommend either NINJA-IDE or Atom as our main Python IDE, although you can
use whatever you're most comfortable with.

.. _python_installing:

Installing Packages
===========================

Operating systems based on Ubuntu 16.04 or later ship with Python3 by
default. It is included in the installation command below anyway, just
in case you've removed it, or are on a system where it isn't installed
by default.

..  IMPORTANT:: If you type in the following commands, make sure you scroll and
    get the entire command. If you prefer to copy and paste, triple-click the third
    command so you get the whole thing.

We'll also install the libraries and development tools we use for Python3. We'll be getting
the latest stable version of Kivy from a third-party repository.

..  code-block:: bash

    $ sudo add-apt-repository ppa:kivy-team/kivy
    $ sudo apt update
    $ sudo apt install python3 python3-kivy python3-lxml python3-pip python3-virtualenv pylint3 ninja-ide

..  WARNING:: Do not **ever** use :code:`sudo pip`. If you want to install
    packages via pip, use a virtual environment.

.. _python_ide:

Choosing an IDE
=========================

There are quite a few IDEs and editors for the Python language. We personally
recommend :ref:`vscode` or :ref:`ninjaide`. :ref:`atom` is another option.

If none of these strike your fancy, there are dozens of other options,
including PyCharm, Brackets, EricIDE, Spyder, and PyPE.