..  _ninjaide:

NINJA-IDE
#################################

NINJA-IDE is a simple, clean, powerful IDE for the Python language.
The current release, version 2.x runs on Ubuntu 16.04 and earlier.

..  WARNING:: NINJA-IDE v2.x relies on Qt4, which is no longer installable
    on Ubuntu 17.04 and onward. NINJA-IDE v3.x will be the first to work with
    Qt5, and thus the newer versions of Ubuntu. It is still in active
    development.

Installation
==============================

On Ubuntu, you can install NINJA-IDE directly from the :code:`apt` repositories.

..  WARNING:: While NINJA-IDE appears to install on Ubuntu 17.04 and later, it
    *will not run* (see the WARNING above).

..  code-block:: bash

    $ sudo apt install ninja-ide

For all other operating systems, you can download an official package from
the `NINJA-IDE website <http://www.ninja-ide.org/downloads/>`_.

Configuring
==============================

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