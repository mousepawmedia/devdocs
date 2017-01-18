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

.. _python_configure_ide:

Configuring the IDE
=========================

NINJA-IDE
--------------------------

We need to change a few settings in NINJA-IDE. Go to
:menuselection:`Edit --> Preferences`, and change the following settings.

Under :menuselection:`General --> Execution`, set :guilabel:`Python Path:` to
`python3`. This will ensure NINJA-IDE uses Python3 instead of Python2,
especially since both are typically installed side-by-side on a typical
Ubuntu-based system.

On that tab, also set the following flags:

* :code:`-tt`: ON

* :code:`-W`: ON

* :code:`-3`: OFF

Next, go to the tab :menuselection:`Editor --> Configuration`. Set the following
to comply with our Python3 coding standards:

* Indentation Length: 4 (space)

    * Use Tabs: NO

* Margin Line: 80

    * Show Margin Line: YES

* Use Platform End Of Line: YES

* Find and Show Errors: YES

    * Show Tool tip information about the errors: YES

* Find and Show Check Style errors: YES

    * Show Tool tip information about the PEP8 errors: YES

* Show Python3 Migration Tips: NO

* Remove Trailing Spaces and add Last Line automatically: YES

* Show Tabs and Spaces: YES

* Allow Word Wrap: NO

* Check for Docstrings in Classes and Functions: NO

..  TIP:: You may want to adjust the settings in the tab
    :menuselection:`Editor --> Completion` for your preferences.

Finally, click :guilabel:`Save`.

Atom
---------------------------

If you're looking to install Atom, see :ref:`sphinx_install_atom`.

In Atom, you will need to install the following packages. To install packages,
go to :menuselection:`Edit --> Preferences --> Install`. Search for each
package name and click :guilabel:`Install`.

* `language-python`

* `linter-pylint`

* `autocomplete-python` (optional)

* `python-tools` (optional)

* `python-indent` (optional)

Each of those packages has some settings you can play with under
:menuselection:`Edit --> Preferences --> Packages`. Click :guilabel:`Settings`
on the package you want to modify.

At minimum, you'll want to edit the settings for `linter-pylint`, setting
the following:

* Executable: :code:`pylint3`

* RC File: :code:`%p/pylintrc`

To control how Pylint3 works, you'll need to create a :file:`pylintrc` file
in your project directory. You can create on quickly on the command line
by running the following Terminal commands on your project directory:

..  code-block:: bash

    $ pylint3 --generate-rcfile > pylintrc

Then you can open and modify this file as needed.
