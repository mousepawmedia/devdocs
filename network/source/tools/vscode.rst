..  _vscode:

Visual Studio Code
#################################

MousePaw Media officially recommends the  **Visual Studio Code** IDE for
all development work. All of our projects are preconfigured to support
this IDE.

Installing
=================================

If you're on Ubuntu Linux, you can install from the official apt repository.

..  code-block:: bash

    $ sudo apt install curl
    $ curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    $ sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    $ sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'
    $ sudo apt update
    $ sudo apt install code

Alternatively, on most Linux systems, you can install from Snapcraft:

..  code-block:: bash

    $ sudo snap install code

Or, you can install from Flatpak on most Linux systems:

..  code-block:: bash

    $ flatpak install flathub com.visualstudio.code

For Windows and Mac, you can download and install VSCode from
`code.visualstudio.com <https://code.visualstudio.com/>`_. If you're installing
on another type of Linux system, you can find instructions on the official
article `Running VS Code on Linux <https://code.visualstudio.com/docs/setup/linux>`_.

Extensions
=================================

We recommend the following extensions (author name in parenthesis). Make sure
you read the description of each, so you understand what it does and how it
should be configured:

* C/C++ (Microsoft)
* C/C++ Advanced Lint (Joseph Benden)
* C/C++ Snippets (Harsh)
* CMake (twxs)
* CodeLLDB (Vadim Chugunov)
* Docker (Microsoft)
* gitignore (CodeZombie)
* Live Share (Microsoft)
* Live Share Extension Pack (Microsoft)
* Makefiles support for VSCode (Lilian Besson)
* markdownlint (David Anson)
* Phabricator (Christian Vuerings)
* Phabriator Links (matt-good)
* Python (Microsoft)
* Python Test Explorer for Visual Studio Code (Little Fox Team)
* Test Explorer UI (Holger Benl)
* reStructuredText (LeXtudio)
* Settings Sync (Shan Khan)
* Table Formatter (Shuzo Iwaski)
* XML Tools (Josh Johnson)

..  IMPORTANT:: ``C/C++ Clang Command Adapter`` makes the linter act weird.
    Do not install it.

..  NOTE:: ``cppcheck`` has extremely high CPU usage, so we use
    ``C/C++ Advanced Linter`` instead.

You may also consider the following helpful extensions. You should read the
description of each before installing:

* Apache Conf (mrminc)
* Auto Close Tag (Jus Han)
* Better C++ Syntax (Jeff Hykin)
* Bookmarks (Alessandro Fragnani)
* Bracket Pair Colorizer (CoenraadS)
* Clock in status bar (Compulim)
* Code Spell Checker (Street Side Software)
* Comment Anchors (Exodius Studios)
* Contextual Duplicate (Lars Fernhomberg)
* GitHub (KnisterPeter)
* GitLens (Eric Amodio)
* indent-rainbow (oderwat)
* Insert Unicode (brunnerh)
* Markdown All in One (Yu Zhang)
* Overtype (Adam Maras)
* Path Intellisense (Christian Kohler)
* Paste URL (kukushi)
* Peacock (John Papa)
* Phabricator Links (matt-good)
* Reflow Paragraph (Troels Damgaard)
* shell-format (foxundermoon)
* Sort lines (Daniel Imms)
* TODO Highlight (Wayou Liu)
* Toggle Quotes (BriteSnow)
* Unicode code point of current character (zeithaste)
* Visual Studio IntelliCode -- Preview (Microsoft DevLabs)
* VSCode Great Icons (Emmanuel Béziat)
* XML (Red Hat)

Settings
=================================

You should go through the settings on
:menuselection:`File --> Preferences... --> Settings`, and set them to your
preferences.

If you're not sure how to get started, you can use Jason C. McDonald's default
settings, which can be found on
`Phabricator P37 <https://phabricator.mousepawmedia.net/P37>`_. Just remove
the settings for any extensions you don't use.
