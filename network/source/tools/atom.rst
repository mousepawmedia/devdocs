..  _atom:

Atom
#################################

You can use the Atom text and code editor with many MousePaw Media projects.
While this isn't our officially recommended IDE, many of our staff members use
it.

..  NOTE:: We have found that Atom's support for C++ leaves a lot to be
    desired. Consider :ref:`vscode` or :ref:`codeblocks` for C++ instead.

Installing
===============================

Atom is a FOSS multi-language text and code editor with a lot of nifty features.
It's also completely customizable, allowing you to add functionality and themes
from the built-in packages. (If you're a programmer, you can also write your
own!)

Installing on Linux
-------------------------------

If you're on Ubuntu, you can install Atom from a
`third-party repository <http://www.webupd8.org/2014/05/install-atom-text-editor-in-ubuntu-via-ppa.html>`_.

..  code-block:: bash

    $ sudo add-apt-repository ppa:webupd8team/atom
    $ sudo apt update
    $ sudo apt install atom

On other operating systems, you can install Atom from the official packages
on `the Atom website <https://atom.io/>`_.

`SOURCE: Installing Atom - Linux <https://flight-manual.atom.io/getting-started/sections/installing-atom/#platform-linux>`_

Installing on Mac
-------------------------------

On Mac, you can go to the `official Atom releases page <https://github.com/atom/atom/releases/>`_
and download the file :file:`atom-mac.zip` for the latest stable release.

Extract the ZIP file, and drag the ``Atom`` application into your
:file:`Applications` folder.

`SOURCE: Installing Atom - Mac <https://flight-manual.atom.io/getting-started/sections/installing-atom/#platform-mac>`_

Installing on Windows
-------------------------------

On Windows, you can go to the `official Atom releases page <https://github.com/atom/atom/releases/>`_
and download the file :file:`AtomSetup.exe` for the latest stable release.

Run the executable to launch the Atom installer.

`SOURCE: Installing Atom - Windows <https://flight-manual.atom.io/getting-started/sections/installing-atom/#platform-windows>`_

Configuring Atom
===============================

Atom has a lot of pretty impressive features that I won't be covering in the
tutorial. Consider taking some time to play around with it.

You'll want to install some packages to improve Atom's handling of various
file types and languages. Go to :menuselection:`Edit --> Preferences`, and
then select :guilabel:`Install` on the left hand menu.

We recommend the following packages:

* :code:`autocomplete-clang`
* :code:`autocomplete-cmake`
* :code:`autocomplete-python`
* :code:`autocomplete-xml`
* :code:`language-cmake`
* :code:`language-kivy`
* :code:`language-python`
* :code:`language-restructuredText`
* :code:`linter`
* :code:`linter-cppcheck`
* :code:`linter-pylint`
* :code:`linter-xmllint`
* :code:`python-indent`
* :code:`python-tools`
* :code:`rst-preview-pandoc`

You may also consider the following helpful packages:

* :code:`cursor-history`
* :code:`file-icons`
* :code:`highlight-selected`
* :code:`minimap`
* :code:`minimap-bookmarks`
* :code:`minimap-codeglance`
* :code:`minimap-cursorline`
* :code:`minimap-findandreplace`
* :code:`minimap-highlight-selected`
* :code:`minimap-selected`
* :code:`todo-show`

..  sidebar:: Configuring Pylint3

    To control how Pylint3 works, you'll need to create a
    :file:`pylintrc` file in your project directory. **Our projects already
    have this file**. If you need to make one for your own project, run the
    following command in your project directory:
    :code:`pylint3 --generate-rcfile > pylintrc`
    Then you can open and modify this file as needed.

Each of those packages has some settings you can play with under
:menuselection:`Edit --> Preferences --> Packages`. Click :guilabel:`Settings`
on the package you want to modify.

At minimum, you'll want to edit the settings for `linter-pylint`, setting
the following:

* Executable: :code:`pylint3`

* RC File: :code:`%p/pylintrc`