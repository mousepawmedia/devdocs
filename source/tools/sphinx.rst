.. _sphinx:

Sphinx Development Environment
###################################

We use a Python library called Sphinx to generate documentation from
:abbr:`ReST (Restructured Text)` files.

.. _sphinx_install:

Installing Python
=========================

If you haven't already installed Python 3, see :ref:`python_installing`.
When you come to the end of the Python installation section, come back to
this guide.

Installing Sphinx
=========================

Installing Sphinx on Linux
------------------------------

In Ubuntu, Sphinx is very easy to install. You can install it for either
Python 2 or Python 3. Since we use the latter, we'll install that.

To install on Ubuntu, simply run...

..  code-block:: bash

    $ sudo apt install python3-sphinx pandoc

Installing Sphinx on Mac
------------------------------

We can install Sphinx on Mac using either Homebrew or MacPorts.

Installing Via MacPorts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: bash

    $ sudo port install py36-sphinx pandoc
    $ sudo port select --set sphinx py36-sphinx

`SOURCE: Installing Sphinx - MacPorts <http://www.sphinx-doc.org/en/master/usage/installation.html#macports>`_

Installing Via Homebrew
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  code-block:: bash

    $ brew install sphinx-doc pandoc

`SOURCE: Installing Sphinx - Homebrew <http://www.sphinx-doc.org/en/master/usage/installation.html#homebrew>`_

Installing Sphinx on Windows
------------------------------

You can install Sphinx using Python pip.

..  code-block:: batch

    python -m pip install -U sphinx

Then, download and install Pandoc from its official website:
`<http://www.pandoc.org/installing.html>`_.

`SOURCE: Installing Sphinx - Windows <http://www.sphinx-doc.org/en/master/usage/installation.html#windows>`_

.. _sphinx_editor:

Choosing an Editor
=========================

ReST files are just plain text files written in the ReStructuredText markup
language. Thus, you can edit them in any text editor.

However, we've found that :ref:`vscode` and :ref:`atom` handle ReST files
uncommonly well. You can also use a plain text and code editor, such as
:ref:`geany`.

.. _sphinx_edit_docs:

Writing and Editing Documentation
=========================================

To get started editing documentation, create or open a file in the
:file:`docs/source` directory of the repository for whatever project you're
documenting. (Some projects may put documentation in a different outer
directory than :file:`docs/`.

Index
--------------------------

:file:`source/index.rst` is the main file in your documentation. To add a
file to the automatic table of contents tree, list it below this section::

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

..  important::  Note that I lined everything up, so both of my new entries have
    the same number of leading spaces as :code:`:maxdepth: 2`. ReST is VERY
    finicky! You should line things up exactly.

Adding a New File
----------------------------

To add a new file to your documentation, create a file in the
:file:`docs/source` directory (or a subdirectory thereof) with the :code:`.rst`
extension.

A good filename should be all lowercase, with underscores where necessary. For
example, :file:`goldilocks.rst` would be a good file name for the Goldilocks
documentation.

Documentation pages can be quite long, and that's fine. Sphinx subdivides large
documents beautifully, so you can generally devote one single document to one
single module, section, or topic.

Once you click :guilabel:`Save`, your editor will probably know to treat the
file as a ReST document.

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

.. _sphinx_setup_docs:

Setting Up Documentation
=====================================

..  warning:: **If your project already has documentation, skip this section!**

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

..  note:: You can change most of those options again later.

Before continuing, you should also edit your :file:`.gitignore` file, adding
the line::

    build/

This ensures that Sphinx's output is not tracked by the repository.

Configuring
---------------------------

:file:`source/conf.py` is the configuration file for Sphinx. This is where you
change things like project name, author, copyright, and version, as well as
build options and theme.

If you just created this documentation directory, open this file
and then look for :code:`html_theme`. Change this from :code:`alabaster` to
:code:`sphinx_rtd_theme`. The new line should look like this::

    html_theme = 'sphinx_rtd_theme'

.. _sphinx_learning-rst:

Learning ReStructuredText
=====================================

ReStructuredText is a markup language all its own. It has a ton and a half of
awesome features, but it's hard to know them all. Check out
`Sphinx's documentation <http://www.sphinx-doc.org/en/stable/rest.html>`_ for
help with all things Sphinx and ResT! I've linked you above to the best page
to start with.
