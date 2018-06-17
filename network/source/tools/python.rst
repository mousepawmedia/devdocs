.. _python:

Python3 Development Environment
######################################

We use the latest version of Python 3 for a lot of our application
and GUI development. Kivy is our GUI toolkit.

We recommend either VSCode, Atom, or NINJA-IDE as our main Python IDE, although
you can use whatever you're most comfortable with.

.. _python_installing:

Installing Python
===========================

Installing Python on Linux
---------------------------------

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

Installing Python on Mac
---------------------------------

We can install Python and everything else we need using Homebrew or MacPorts.

Installing Via MacPorts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: bash

    $ sudo port install python36 py36-pip py36-virtualenv py36-pylint
    $ sudo port select --set python python36
    $ sudo port select --set pip py36-pip
    $ sudo port select --set virtualenv py36-virtualenv
    $ sudo port select --set pylint py36-pylint

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

.. _python_kivy:

Installing Kivy
=============================

Installing Kivy on Linux
-----------------------------

We first need to install all the dependencies for Kivy. On Ubuntu, we
can run...

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

`SOURCE: Kivy: Installation in a Virtual Environment <https://kivy.org/docs/installation/installation-linux.html#installation-in-a-virtual-environment>`_

Installing Kivy on Mac
-----------------------------

We can install Kivy using either MacPorts or Homebrew.

Installing Kivy via MacPorts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: bash

    $ sudo port install libsdl2 libsdl2_image libsdl2_ttf libsdl2_mixer
    $ pip install Cython==0.26.1
    $ pip install kivy

`SOURCE: Kivy: Installation on OS X - Using MacPorts with pip <https://kivy.org/docs/installation/installation-osx.html#using-macports-with-pip>`_

Installing Kivy via Homebrew
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: bash

    $ brew install pkg-config sdl2 sdl2_image sdl2_ttf sdl2_mixer gstreamer
    $ pip install Cython==0.26.1
    $ pip install kivy

`SOURCE: Kivy: Installation on OS X - Using Homebrew with pip <https://kivy.org/docs/installation/installation-osx.html#using-homebrew-with-pipl>`_

Installing Kivy on Windows
-----------------------------

In your Command Prompt, run the following...

..  code-block:: batch

    python -m pip install docutils pygments pypiwin32 kivy.deps.sdl2 kivy.deps.glew
    python -m pip install kivy.deps.gstreamer
    python -m pip install kivy.deps.angle
    python -m pip install kivy

`SOURCE: Kivy: Installation on Windows <https://kivy.org/docs/installation/installation-windows.html#installation>`_

.. _python_ide:

Choosing an IDE
=========================

There are quite a few IDEs and editors for the Python language. We personally
recommend :ref:`vscode` or :ref:`ninjaide`. :ref:`atom` is another option.

If none of these strike your fancy, there are dozens of other options,
including PyCharm Community Edition, Brackets, EricIDE, Spyder, and PyPE.