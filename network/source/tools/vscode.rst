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

For Windows and Mac, you can download and install VSCode from
`code.visualstudio.com <https://code.visualstudio.com/>`_. If you're installing
on another type of Linux system, you can find instructions on the official
article `Running VS Code on Linux <https://code.visualstudio.com/docs/setup/linux>`_.

Extensions
=================================

We recommend the following extensions (author name in parenthesis):

* C/C++ (Microsoft)
* C/C++ Advanced Lint (Joseph Benden)
* C/C++ Snippets (Harsh)
* CMake (twxs)
* gitignore (CodeZombie)
* Kivy (BattleBas)
* CodeLLDB (Vadim Chugunov)
* markdownlint (David Anson)
* Python (Microsoft)
* reStructuredText (LeXtudio)
* XML Tools (Josh Johnson)

..  IMPORTANT:: ``C/C++ Clang Command Adapter`` makes the linter act weird.
    Do not install it.

..  NOTE:: ``cppcheck`` has extremely high CPU usage, so we use
    ``C/C++ Advanced Linter`` instead.

You may also consider the following helpful extensions:

* Auto Close Tag (Jus Han)
* Bookmarks (Alessandro Fragnani)
* Bracket Pair Colorizer (CoenraadS)
* Code Spell Checker (Street Side Software)
* Contextual Duplicate (Lars Fernhomberg)
* GitLens (Eric Amodio)
* Reflow Paragraph (Troels Damgaard)
* TODO Highlight (Wayou Liu)

Settings
=================================

You should go through the settings on
:menuselection:`File --> Preferences... --> Settings`, and set them to your
preferences.

If you're not sure how to get started, you can use Jason C. McDonald's default
settings, which can be found on
`Phabricator P37 <https://phabricator.mousepawmedia.net/P37>`_. Just remove
the settings for any extensions you don't use.
