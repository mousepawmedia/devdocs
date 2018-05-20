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

We'll also install the libraries and development tools we use for Python3.

..  code-block:: bash

    $ sudo apt update
    $ sudo apt install python3 python3-pip python3-virtualenv pylint3 python-dev python3-dev

..  WARNING:: Do not **ever** use :code:`sudo pip`. If you want to install
    packages via pip, use a virtual environment.

Install Kivy
-----------------------------

We first need to install all the dependencies for Kivy.

..  code-block:: bash

    $ sudo apt install build-essential git ffmpeg libsdl2-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-ttf-dev libportmidi-dev libswscale-dev libavformat-dev libavcodec-dev zlib1g-dev libgstreamer1.0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good

Then, we'll create a new virtual environment for Kivy. In this example, we'll
just make a virtual environment called ``kivy-venv`` in our home folder, but
you can put it wherever you like by changing the path in the first command
below.

..  code-block:: bash

    $ virtualenv --no-site-packages ~/kivy-venv
    $ source ~/kivy-venv/bin/activate
    $ pip install Cython==0.28.2
    $ pip install kivy
    $ deactivate

Any time you want to use that virtual environment, just run
:code:`source ~/kivy-venv/bin/activate`. The command :code:`deactivate`
exits the virtual environment.

.. _python_ide:

Choosing an IDE
=========================

There are quite a few IDEs and editors for the Python language. We personally
recommend :ref:`vscode` or :ref:`ninjaide`. :ref:`atom` is another option.

If none of these strike your fancy, there are dozens of other options,
including PyCharm, Brackets, EricIDE, Spyder, and PyPE.