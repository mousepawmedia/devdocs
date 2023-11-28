.. _genv:

Guide: Development Environment
#####################################

This guide will walk you through the process of setting up your computer
for MousePaw Media development work.

If you're not a programmer, you may jump to the relevent section of this guide:

* :ref:`genv_content`
* :ref:`genv_designprod`

.. _genv_programming:

Environment: Programming
====================================

Operating Systems
-------------------------------------------

We officially support the following operating systems for development work:

* Ubuntu Linux (recommended)
* Debian Linux
* Other Linux
* macOS
* FreeBSD

..  note:: We do not provide technical support for third-party development
    tools on non-Debian systems.

Developing on Windows
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

While we do not directly support Windows operating systems, if you're using
Windows 10 or Windows 11, you can use the `Windows Subsystem for Linux <https://learn.microsoft.com/en-us/windows/wsl/install>`_.

To install WSL, start Terminal, and run the following:

..  code-block:: powershell

    wsl --install -d Ubuntu

This will install the latest version of Ubuntu. Restart your computer after
the installation is complete.

In Terminal again, launch WSL:

..  code-block:: powershell

    wsl

Follow the prompts to set up a user account for the Ubuntu instance.

With Ubuntu installed via WSL, you can now use the instructions in this
documentation for Ubuntu Linux.

..  note:: If you use the Windows Subsystem for Linux, you can also run some
    graphical Linux applications, by following `this guide from HowToGeek <https://www.howtogeek.com/261575/how-to-run-graphical-linux-desktop-applications-from-windows-10s-bash-shell/>`_.

There are two alternative approaches, although we do not provide technical
support for them:

* You may use `MinGW-w64 <https://www.mingw-w64.org/>`_ for C++ compiling.
* You can have `Visual Studio use the Clang compiler <https://devblogs.microsoft.com/cppblog/use-any-c-compiler-with-visual-studio/>`_.

Git
-------------------------------------

Regardless what part of the project you're working on, you'll need access to our
Git repositories. For setup instructions for Git, see :ref:`git`.

C/C++ Development
-------------------------------------

For C/C++ development:

* **C/C++ compiler and toolchain**: Clang (recommended) or GCC.
* **CMake**
* **A C++ IDE**: We strongly recommend VSCode.

..  note:: We have **no plans** to support MSVC. Use Clang for compiling on
    Microsoft Windows.

For setup instructions, see :ref:`cpp`.

Python Development
-------------------------------------

For Python development:

* **Python 3**
* **A Python IDE**: We strongly recommend VSCode.

For setup instructions, see :ref:`python`.

Sphinx Development
-------------------------------------

For Sphinx development:

* **Python 3**
* **Sphinx**
* **A code editor**: We strongly recommend VSCode.

For setup instructions, see :ref:`sphinx`.

Selecting an IDE
-------------------------------------

We have guides for setting up several IDEs and editors...

* :ref:`vscode` (recommended)
* :ref:`geany`

General Coding Tools
-------------------------------------

In addition to our language-specific tools, we also use the following
tools for general work.

* **Dia**: Flowcharting
* **Meld**: Comparing and merging files.
* **Pencil**: GUI prototyping
* **SpeedCrunch**: Scientific calculator.

For setup instructions, see :ref:`devtools`.

Fonts
-------------------------------------

We have a collection of approved fonts we use at MousePaw Media. We require all
staff to install these on their computers, as these fonts are used in all
web design, user interface design, graphics design, and official documents.

These fonts are all 100% free and open source, so they're great additions to
your font collection anyhow!

..  note:: The font ``Hack`` is included, which we highly recommend for use
    in your editor or IDE!

For instructions, see :ref:`fonts`.

.. _genv_content:

Environment: Content Development
====================================

Operating Systems
-------------------------------------------

We officially support the following operating systems for content development
work:

* Ubuntu Linux (recommended)
* Debian Linux
* Other Linux
* FreeBSD

..  note:: We do not provide technical support for third-party development
    tools on non-Debian systems.

Git
-------------------------------------

Content development still involves code! Regardless what part of the project
you're working on, you'll need access to our Git repositories.
For setup instructions for Git, see :ref:`git`.

Sphinx Development
-------------------------------------

As part of content development, you will be working with Sphinx for authoring
official scripts and other production documents.

* **Python 3**
* **Sphinx**
* **A code editor**: We recommend either VSCode or Atom.

For setup instructions, see :ref:`sphinx`.

Selecting an IDE
-------------------------------------

You should install one or both of these text and code editors.

* :ref:`vscode` (recommended)
* :ref:`geany`

Fonts
-------------------------------------

We have a collection of approved fonts we use at MousePaw Media. You should
ensure you have these installed on your computer.

For instructions, see :ref:`fonts`.

.. _genv_designprod:

Environment: Design and Production
====================================

Operating Systems
-------------------------------------------

We officially support the following operating systems for design and production
work:

* Ubuntu Linux (recommended)
* Debian Linux
* Other Linux
* FreeBSD

..  note:: We do not offer help with third-party tools on non-Debian systems.

Graphics Tools
------------------------------------

We use the following graphics design tools:

* **Birdfont**
* **Blender**
* **Darktable**
* **GIMP**
* **Gpick**
* **Inkscape**
* **Krita**
* **MyPaint**
* **Scribus**

For setup instructions, see :ref:`graphics`.

Wacom Tablet
-------------------------------------

Ubuntu (GNOME) allows you to control Wacom tablets out of the box, while
Ubuntu MATE (and some other distros) require additional tools to control
your tablet.

For instructions, see :ref:`wacom`.

Fonts
-------------------------------------

We have a collection of approved fonts we use at MousePaw Media. You should
ensure you have these installed on your computer.

For instructions, see :ref:`fonts`.

Audio/Video Tools
-------------------------------------

We use the following audio and video tools:

* **Audacity**
* **Audio Recorder**
* **Handbrake**
* **Kdenlive**
* **Musescore**

Depending on your assignment, there are several other applications in this
category we recommend.

For setup instructions, see :ref:`avtools`.
