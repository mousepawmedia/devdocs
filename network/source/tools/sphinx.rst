.. _sphinx:

Sphinx Development Environment
###################################

We use a Python library called Sphinx to generate documentation from
:abbr:`ReST (Restructured Text)` files.

.. _sphinx_install:

Installing Sphinx
=========================

In Ubuntu, Sphinx is very easy to install. You can install it for either
Python 2 or Python 3. Since we use the latter, we'll install that.

..  NOTE:: If you don't have Python3 installed, see :doc:`/tools/python`

..  code-block:: bash

    $ sudo apt update
    $ sudo apt install python3-sphinx pandoc

.. _sphinx_install_atom:

Installing Atom
=========================

ReST files are just plain text files written in the ReStructuredText markup
language. Thus, you can edit them in any text editor. However, I've found that
Atom handles ReST files uncommonly well.

Atom is a FOSS multi-language text and code editor with a lot of nifty features.
It's also completely customizable, allowing you to add functionality and themes
from the built-in packages. (If you're a programmer, you can also write your
own!)

We'll be installing Atom from a
`third-party repository <http://www.webupd8.org/2014/05/install-atom-text-editor-in-ubuntu-via-ppa.html>`_.

..  code-block:: bash

    $ sudo add-apt-repository ppa:webupd8team/atom
    $ sudo apt update
    $ sudo apt install atom

Configuring Atom
-------------------------------

Atom has a lot of pretty impressive features that I won't be covering in the
tutorial. Consider taking some time to play around with it.

It doesn't come with very good built-in support of ReST, so there are a couple
of packages you'll want to add to Atom. Go to
:menuselection:`Edit --> Preferences`, and then select :guilabel:`Install` on
the left hand menu. You'll want to install the following packages:

* :code:`language-restructuredText`
* :code:`rst-preview-pandoc`

.. _sphinx_setup_docs:

Setting Up Documentation
=====================================

..  WARNING:: If your project already has documentation, skip this section!

In the repository for the project that you want to create the documentation for,
run...

..  code-block:: bash

    $ mkdir docs
    $ cd docs

This creates a new folder :file:`docs` for our documentation, and navigates into
it. Then, run...

..  code-block:: bash

    $ sphinx-quickstart

* Below are the settings you should select during Quickstart. I've marked ENTER
  for those options that you should use the default on.

* Root path: ENTER

* Separate source and build: :code:`y`

* Name prefix for templates and static dir: :code:`.`

* Project name: <name of your project>

* Author name(s): MousePaw Media

* Project version: <short project version>

* Project release: <long project version>

* Project language: :kbd:`Enter`

* Source file suffix: :kbd:`Enter`

* Name of your master document: :kbd:`Enter`

* Do you want to use the epub builder?: :code:`y`

* **Use defaults for the rest of the options.**

..  NOTE:: You can change most of those options again later.

Before continuing, you should also edit your :file:`.gitignore` file, adding
the line::

    build/

This ensures that Sphinx's output is not tracked by the repository.

Configuring
---------------------------

:file:`source/conf.py` is the configuration file for Sphinx. This is where you
change things like project name, author, copyright, and version, as well as
build options and theme.

If you just created this documentation directory, open this file in Atom,
and then look for :code:`html_theme`. Change this from :code:`alabaster` to
:code:`sphinx_rtd_theme`. The new line should look like this::

    html_theme = 'sphinx_rtd_theme'

.. _sphinx_edit_docs:

Writing and Editing Documentation
=========================================

To get started editing documentation, open Atom and go to
:menuselection:`File → Open Folder...`. Go to the repository you want to
work in, and just click :guilabel:`OK`. This will tell Atom that you're working
in that folder.

The file tree on the left will show you the entire repository. If you don't see
the file tree, click :kbd:`Ctrl + \\` to toggle it. Sphinx should have a
dedicated folder, which is docs for most projects. The ReST (\*.rst) files can
be found in :file:`docs/source`.

By the way, Atom has some pretty fancy integration with Git. Newly added files
appear in the file tree as green, and modified files as orange. (Those colors go
away once you commit your changes.) On the bottom-right corner of Atom, you can
see the branch that you're currently on.

Single-click a file in the file tree to preview it (the name in the tab will be
in italics), and double-click a file to open it. Then, just start editing!

To preview how a `*.rst` would look once rendered by Sphinx, press
:kbd:`Ctrl + Shift + E`. The preview isn't fancy, but it is sufficient to
give you a basic idea while working.

..  HINT:: Panes are resizable. I usually make the preview window just narrow
    enough that I can see the right-hand margin line on the editing window.

Index
--------------------------

:file:`source/index.rst` is the main file in your documentation. Open the file
in Atom. To add a file to the automatic table of contents tree, list it below
this section::

    .. toctree::
       :maxdepth: 2

You only need to list the name of the file, without the extension. If it is
within a subfolder in :file:`source/`, just write out the relative path.

For example, if you had the file :file:`foo.rst` in :file:`source/` and
:file:`baz.rst` in :file:`source/bar/`, you can add them like this::

    .. toctree::
       :maxdepth: 2

       foo
       bar/baz

..  IMPORTANT::  Note that I lined everything up, so both of my new entries have
    the same number of leading spaces as :code:`:maxdepth: 2`. ReST is VERY
    finicky! You should line things up exactly.

Adding a New File
----------------------------

To add a new file to your documentation, in Atom, go to
:menuselection:`File → New File`. Press :kbd:`Ctrl + S` and save the file in
the :file:`docs/source` folder (or a subfolder thereof) with the :code:`.rst`
extension.

A good filename should be all lowercase, with underscores where necessary. For
example, :file:`goldilocks.rst` would be a good file name for the Goldilocks
documentation.

Documentation pages can be quite long, and that's fine. Sphinx subdivides large
documents beautifully, so you can generally devote one single document to one
single module, section, or topic.

Once you click :guilabel:`Save`, Atom will know to treat the file as a ReST
document.

Rendering Final Output
----------------------------

It is super simple to render the gorgeous output of your documentation with
Sphinx. In your Terminal, go to your :file:`docs/` folder, and simply type...

..  code-block:: bash

    $ make

...to list out all the supported formats. My favorite is HTML, which generates a
snazzy, searchable web version. To output to HTML, type...

..  code-block:: bash

    $ make html

Double-check the Terminal output. If it complains about any errors or warnings,
be sure to fix them! Errors are pretty obvious in Sphinx - missing documents,
malformed tables, and so on.

..  HINT:: Because Sphinx is the standard way of creating documentation for
    Python projects, the #python IRC channel is a really good place to get help.

Once the HTML is created, go to :file:`build/html/`, and open :file:`index.html`
in your favorite web browser.

--------------

ReStructuredText is a markup language all its own. It has a ton and a half of
awesome features, but it's hard to know them all. Check out
`Sphinx's documentation <http://www.sphinx-doc.org/en/stable/rest.html>`_ for
help with all things Sphinx and ResT! I've linked you above to the best page
to start with.
