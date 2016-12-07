..  _cpp:

C++ and C Development Environment
###################################

We use C++ and C for most of our projects at MousePaw Games. This tutorial
will help you set up the development environment for working with those
languages on Linux.

..  _cpp_install_compiler:

Install the Compiler
============================

We use the GCC5 compiler. If you're not on a Debian-based Linux system,
you'll need to find out how to install this yourself.

We don't directly support Windows as a development environment. If you're
using Windows, you'll need to use MinGW or another GCC5-compatible C++ compiler.
**We have no plans to support MSVC.**

If you're on a Mac, you should install the Command Line Tools for Xcode,
which has GCC onboard.

..  WARNING:: If you're using an operating system based on a version of
    Ubuntu before 16.04, you may not be able to compile our code. There
    was a major bug in the last version of GCC5 for Ubuntu 14.04 which
    prevented our code from compiling.

Although GCC is available through the core repositories, we like using
the latest stable compiler builds. You can install those via...

..  code-block:: bash

    $ sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    $ sudo apt update
    $ sudo apt install gcc g++ gcc-5 g++-5 gcc-5-doc g++-5-doc

If you're on a 64-bit system, you'll need some additional packages...

..  code-block:: bash

    $ sudo apt-get install gcc-5-multilib g++-5-multilib libc6-dev-i386

Once you have everything installed, configure Ubuntu to allow you to switch
between versions of GCC and G++, in case you have multiple versions on your
system (like GCC 6).

..  code-block:: bash

    $ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 60 --slave /usr/bin/g++ g++ /usr/bin/g++-5
    $ sudo update-alternatives --config gcc

If the `auto mode` option has selected `gcc-5`, go with that option. Otherwise,
select `gcc-5` manually.

..  NOTE:: Did you know that GCC also has compilers for Java, Objective C, D,
    Fortran, and Ada? You can install and upgrade these as well, using the
    same method as above, substituting the appropriate name in for `g++` in
    most cases. When setting up alternatives, however, you will need to
    append multiple slaves, each preceded by the `--slave` flag.

+-------------+------------+
| Language    | Compiler   |
+=============+============+
| Ada         | `gnat`     |
+-------------+------------+
| D           | `gdc`      |
+-------------+------------+
| Fortran     | `gfortran` |
+-------------+------------+
| Java        | `gcj`      |
+-------------+------------+
| Objective C | `gobjc`    |
+-------------+------------+

`SOURCE: How Do I Use the Latest GCC (AskUbuntu) <http://askubuntu.com/questions/466651/how-do-i-use-the-latest-gcc-4-9-on-ubuntu-14-04>`_

..  _cpp_install_devtools:

Install Development Tools
==================================

We use a number of coding tools, both command-line and otherwise. You can
quickly install the whole batch (except the IDE) by running the following...

..  code-block:: bash

    $ sudo apt install build-essential cmake valgrind kcachegrind vim cccc

..  _cpp_install_codeblocks:

Code::Blocks 16.01
==================================

We use **Code::Blocks** as our C and C++ IDE.

Installing Code::Blocks
----------------------------------

While it is available directly from the Ubuntu repositories, that version is
vastly outdated for some distributions. You can get the newest stable version by
running the following...

..  code-block:: bash

    $ sudo add-apt-repository ppa:damien-moore/codeblocks-stable
    $ sudo apt update
    $ sudo apt install codeblocks codeblocks-contrib

That will install (or update) both `codeblocks` itself, and the plugins
(of which we use quite a few).

Trimming Down on Plugins
----------------------------------

With all of the plugins installed by default, Code::Blocks can be utterly
overwhelming. To make life easier, we're going to disable all but the most
essential plugins. Later on, you can re-enable those additional plugins that you
are interested in.

On the top menu, go to :menuselection:`Plugins --> Manage Plugins...`. Select
all of the plugins by clicking the first, scrolling to the bottom, holding
down :kbd:`Shift`, and clicking the last. Then, click :guilabel:`Disable`.

Now we will re-enable the plugins we most commonly use.

* Class wizard

* Code completion

* Code profiler

* Code snippets

* Code statistics

* Compiler

* CppCheck

* Debugger

* EditorTweaks

* Files extension handler

* Foreign projects importer

* HexEditor

* OccurrencesHighlighting

* Open files list

* Project options manipulator

* Regular expressions testbed

* Scripted wizard

* Source Exporter

* Source code formatter (AStyle)

* Todo List

Now click :guilabel:`OK`.

..  IMPORTANT:: In between changes, save the workspace by clicking
    :menuselection:`File --> Save workspace`.

..  HINT:: If you ever cannot see the :guilabel:`OK` button on a window,
    maximize the window. It will be at the bottom.

Configuring the Environment
--------------------------------------

Code::Blocks is highly customizable. We're going to make some changes to the
Environment, but while we do that, take some time and explore the other settings
there. Fine-tune things to your liking!

Go to :menuselection:`Settings --> Environment`.

Setting Your Terminal
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

I don't know about you, but I never did like the default Terminal in Linux.
There are a number of other fantastic terminal emulators on Linux. Below is a
table of some of the most common, the command to install it, and the launch
command. That launch command belongs in this :guilabel:`Environment` window,
under :guilabel:`Terminal to launch console programs:`.

Just for reference, Terminator is my personal favorite.

..  WARNING:: Guake is an awesome terminal, but I don't recommend it for this.

..  IMPORTANT:: Put a trailing space at the end of each of the Launch Commands!
    If you omit it, things will not work.

+--------------------------+-----------------------------------------+-------------------------------------+
| Terminal Name            | Install Command                         | Launch Command                      |
+==========================+=========================================+=====================================+
| Gnome Terminal           | :code:`sudo apt install gnome-terminal` | :code:`gnome-terminal -t $TITLE -x` |
+--------------------------+-----------------------------------------+-------------------------------------+
| Konsole                  | :code:`sudo apt install konsole`        | :code:`konsole -e`                  |
+--------------------------+-----------------------------------------+-------------------------------------+
| Terminator               | :code:`sudo apt install terminator`     | :code:`terminator -T $TITLE -x`     |
+--------------------------+-----------------------------------------+-------------------------------------+
| Terminology              | :code:`sudo apt install terminology`    | :code:`terminology -M -T $TITLE -e` |
+--------------------------+-----------------------------------------+-------------------------------------+
| XCFE 4 Terminal          | :code:`sudo apt install xfce4-terminal` | :code:`xfce4-terminal -T $TITLE -x` |
+--------------------------+-----------------------------------------+-------------------------------------+
| XTerm                    | :code:`sudo apt install xterm`          | :code:`xterm -T $TITLE -e`          |
+--------------------------+-----------------------------------------+-------------------------------------+

In Code::Blocks' Environment settings window, on the left, click
:guilabel:`General` settings. Most of the launch commands are pre-filled into
the :guilabel:`Terminal to launch console programs:` drop-down box. Select the
one you want, or paste yours into the box.

Set Up Todo List
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The `Todo List` plugin displays a clickable list of all the `//TODO`, `//NOTE`,
and `//FIXME` comments in the open files. In the Environment settings window,
on the left, scroll down to :guilabel:`Todo list`.

Make sure :guilabel:`Auto-refresh` is UNCHECKED, and the
:guilabel:`Include the Todo list in the messages pane` is CHECKED.

..  IMPORTANT:: At this point, make sure you click OK, save the workspace,
    and restart Code::Blocks.

Configuring Default Compiler Settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

While CMake handles our actual compiler flags, we need to change the default
compiler settings in Code::Blocks a little to offer the C++14 standard.
Most compiler settings should actually go either on
:menuselection:`Project --> Build Options...` or in the CMake configuration
files, but this one change should be global.

Go to :menuselection:`Settings --> Compiler...`. Make sure you're in
:menuselection:`Global compiler settings --> Compiler settings --> Compiler Flags`.
Look for the option :guilabel:`Have g++ follow the C++11 ISO...`, which should
be under :guilabel:`General`. If you upgraded from an earlier version of
Code::Blocks, it may be under :guilabel:`Warnings`. Right-click that option,
and click :guilabel:`New Flag...`.

Fill out the options as follows:

* Name: `Have g++ follow the C++14 ISO C++ Language standard`

* Compiler flags: `-std=c++14`

* Category: `General`

* Supersedes: `-std=c++98 -std=c++0x =std=c++11`

* Exclusive: False

Then, click :guilabel:`OK`. Make sure you check this option, to ensure the
compiler always uses C++14. (Some of our code won't work unless you're on
that standard!)

Configuring Source Formatter
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It can be hard to be 100% adherent to standardized coding style. We make regular
use of the **AStyle** plugin on Code::Blocks to make sure everything is
formatted correctly before uploading. However, in order to use this plugin, it
must be configured.

To get to these settings, go to :menuselection:`Settings --> Editor...` and, on
the left, scroll down to Source formatter. Let's walk through the settings, tab
by tab, setting by setting.

**Style**

* Bracket style: Allman (ANSI)

**Brackets**

* Attach classes: no

* Attach "extern c": no

* Attach namespaces: no

* Attach inlines: no

**Indentation**

* Indentation size (in space): 4

* Use TABs instead of spaces: no

* Force using TABs: no

* Indent case: statement switches: YES

* Indent classes: YES

* Indent modifiers: no

* Indent labels: YES

* Indent namespaces: YES

* Indent switches: no

* Indent preprocessor blocks at bracket level zero: no

* Indent multi-line preprocessor definitions ending with a backslash: no

* Indent preprocessor conditions: YES

* Indent C++ comments beginning in column one: no

* Minimal indent added...: 2

* Maximum of # spaces to indent a continuation line...: 40

**Formatting**

* Break closing headers...: YES

* Break 'else if()' header combinations...: no

* Add brackets to unbracketed one line conditional statements: YES

* Add one line brackets to unbracketed one line conditional statements: no

* Remove brackets from conditional statements: no

* Don't break one-line blocks: YES

* Don't break complex statements and multiple statements residing...: no

* Convert TABs to spaces: YES

* Closes whitespace in the angle brackets of template definitions: YES

* Remove the preceding '*' in multi-line comment...: no

* Enable line breaking: YES

* Break lines after amount of chars...: 80

**Padding**

* Pad empty lines around header blocks: no

* Insert space padding around operators: YES

* Insert space padding around parenthesis on the outside: no

* Insert space padding around parenthesis on the inside: no

* Insert space padding between a header and the following paren: YES

* Remove extra space padding around parenthesis: no

* Delete empty lines within a function or method: no

* Fill empty lines with the whitespace of their previous lines: no

* Pointer alignment: Type

* Reference alignment: Type

Changing Your Theme
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Yes, there are multiple themes available for Code::Blocks! I'm glad of it, too,
because I rely heavily on dark themes to be able to work for long periods of
time.

You might have those themes already present. Go to
:menuselection:`Settings --> Editor...`, and click
:guilabel:`Syntax highlighting` on the left. Then, check the
:guilabel:`Colour theme:` drop down box. If you see lots of options, pick the
one you want.

Once you have a theme picked out, you can further fine-tune it to your liking by
using the other controls on that panel. All the themes are fully customizable!

Installing Themes
"""""""""""""""""""""""""""

If you don't see any themes (besides default), you'll need to set them up.
First, close out of Code::Blocks. Download and save
`color_themes.conf <https://mousepawmedia.net/downloads/color_themes.conf>`_
to your computer.

In your terminal, run...

..  code-block:: bash

    $ cp ~/.config/codeblocks/default.conf ~/.config/codeblocks/backup.conf
    $ cb_share_config

On the window that pops up, click the three dots under
:guilabel:`Source configuration file...`, and browse to the `color_themes.conf`
file you just saved. Then, click the three dots under
:guilabel:`Destination configuration file...` and select `default.conf`, which
should be sitting right in front of you on the browse window by default.

Next, check all the boxes on the left side. [Sorry, there's no fast way to do
that!] Click :guilabel:`Transfer >>`, confirm :guilabel:`Yes`, and then click
:guilabel:`Save` and confirm :guilabel:`Yes`.

Finally, click :guilabel:`Close`.

Once you've done all that, start Code::Blocks, go to
:menuselection:`Settings --> Editor... --> Syntax highlighting`. You can now
browse the imported themes. Use the rest of the controls on that panel to
customize any of them to your liking.

Further Configuring Environment Colors
"""""""""""""""""""""""""""""""""""""""""""""

Unfortunately, the color themes only affect code files themselves. It is
possible to further customize some of the other environment colors to match.

To do this, you'll first want to open a code file, so you can more easily sample
the background color. Then, go to :menuselection:`Settings --> Environment... --> Colours`.
Most of these colors can stay as-is, but there are a few I recommend changing
to match your theme.

You can change any color by clicking its name, and then the color box at the
right side of the panel. Then, tap the eyedropper towards lower-left, and click
the color anywhere on your computer screen (even outside of Code::Blocks!) to
sample it.

You will be using the background color ("Background") and typical text color
("Text") from your selected color scheme.

Change the following colors:

+-------------------------------------------------+-----------------------------------------------------------------+
| Color                                           | Change to...                                                    |
+=================================================+=================================================================+
| Code completion: Documentation popup background | Background                                                      |
+-------------------------------------------------+-----------------------------------------------------------------+
| Code completion: Documentation popup text       | Text                                                            |
+-------------------------------------------------+-----------------------------------------------------------------+
| Editor: Caret                                   | White for dark themes, black for light themes.                  |
+-------------------------------------------------+-----------------------------------------------------------------+
| Code completion: Documentation popup link       | Similar to Text                                                 |
+-------------------------------------------------+-----------------------------------------------------------------+
| Editor: Right margin                            | I recommend a bright color that contrasts with the background.  |
+-------------------------------------------------+-----------------------------------------------------------------+
| Editor: Line numbers background colour          | Background                                                      |
+-------------------------------------------------+-----------------------------------------------------------------+
| Editor: Line numbers foreground colour          | Text                                                            |
+-------------------------------------------------+-----------------------------------------------------------------+
| Editor: Margin chrome colour                    | Background                                                      |
+-------------------------------------------------+-----------------------------------------------------------------+
| Editor: Margin chrome highlight colour          | Background                                                      |
+-------------------------------------------------+-----------------------------------------------------------------+
| Start here page: Background colour              | Background                                                      |
+-------------------------------------------------+-----------------------------------------------------------------+
| Start here page: Link colour                    | Similar to Text                                                 |
+-------------------------------------------------+-----------------------------------------------------------------+
| Start here page: Text colour                    | Text                                                            |
+-------------------------------------------------+-----------------------------------------------------------------+

Once you've made your changes, click :guilabel:`OK`, and then save your
workspace via :menuselection:`File --> Save workspace`.

-------------------

**That's it!** The essential setup is finished for Code::Blocks. I recommend
you go back and browse through the other settings, and get familiar with the
coding environment.
