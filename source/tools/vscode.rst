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

    sudo apt install curl
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'
    sudo apt update
    sudo apt install code

Alternatively, on most Linux systems, you can install from Snapcraft:

..  code-block:: bash

    sudo snap install code

Or, you can install from Flatpak on most Linux systems:

..  code-block:: bash

    flatpak install flathub com.visualstudio.code

For Windows and Mac, you can download and install VSCode from
`code.visualstudio.com <https://code.visualstudio.com/>`_. If you're installing
on another type of Linux system, you can find instructions on the official
article `Running VS Code on Linux <https://code.visualstudio.com/docs/setup/linux>`_.

Using with Windows and WSL
=================================

If you're using the Windows Subsystem for Linux (recommended for Windows users),
you will need to install the WSL extension. This will allow you to run
VSCode *within* WSL, and thus access your Ubuntu-based development environment.

To do this, open the Extensions pane and search for the official "WSL" extension
by Microsoft. Install it.

On the far left of the bottom status bar of Visual Studio Code, you will now
see a new button with two arrows pointing to each other. When you hover over it,
you'll see :guilabel:`Open a Remote Window`. Click that button, and then from
the menu that appears towards the top of the window, click
:guilabel:`Connect to WSL`. This will launch a new window.

You can know whether your current window is running in WSL by looking at this
leftmost button on the status bar. If it's running in Ubuntu in WSL, for
example, it will say :guilabel:`WSL: Ubuntu`.

Extensions
=================================

We recommend the following extensions (author name in parenthesis). Make sure
you read the description of each, so you understand what it does and how it
should be configured.

..  note:: If you're using VSCode with WSL, ensure you install the extensions
    from within a VSCode window running in WSL.

* C/C++ (Microsoft)
* C/C++ Advanced Lint (Joseph Benden)
* C/C++ Themes (Microsoft)
* C/C++ Extension Pack (Microsoft)
* C/C++ Snippets (Harsh)
* CMake (twxs)
* CMake Tools (Microsoft)
* CodeLLDB (Vadim Chugunov)
* Deno (denoland)
* Docker (Microsoft)
* Esbonio (Swyddfa)
* gitignore (CodeZombie)
* GitLab Workflow (GitLab)
* GitLens (GitKraken)
* Live Share (Microsoft)
* Makefile Tools (Microsoft)
* markdownlint (David Anson)
* Python (Microsoft)
* Python Test Explorer for Visual Studio Code (Little Fox Team)
* Test Explorer UI (Holger Benl)
* Table Formatter (Shuzo Iwaski)
* XML Tools (Josh Johnson)

..  important:: ``C/C++ Clang Command Adapter`` makes the linter act weird.
    Do not install it.

..  note:: ``cppcheck`` has extremely high CPU usage, so we use
    ``C/C++ Advanced Linter`` instead.

You may also consider the following helpful extensions. You should read the
description of each before installing:

* Apache Conf (mrminc)
* Auto Close Tag (Jus Han)
* Better C++ Syntax (Jeff Hykin)
* Bookmarks (Alessandro Fragnani)
* Clock in status bar (Compulim)
* Code Spell Checker (Street Side Software)
* Comment Anchors (Exodius Studios)
* Contextual Duplicate (Lars Fernhomberg)
* Dev Containers (Microsoft)
* Hex Editor (Microsoft)
* indent-rainbow (oderwat)
* Insert Unicode (brunnerh)
* Markdown All in One (Yu Zhang)
* Overtype (DrMerfy)
* Path Intellisense (Christian Kohler)
* Paste URL (kukushi)
* Peacock (John Papa)
* Reflow Paragraph (Troels Damgaard)
* shell-format (foxundermoon)
* Sort lines (Daniel Imms)
* TODO Highlight v2 (Jonathan Clark)
* Toggle Quotes (BriteSnow)
* Unicode code point of current character (zeithaste)
* VSCode Great Icons (Emmanuel Béziat)
* XML (Red Hat)

Connect to GitLab
=================================

In a browser, log into the MousePaw Media GitLab. Click your profile picture
at the top of the left pane and select :guilabel:`Preferences`. On the left,
select :guilabel:`Access Tokens`. Click :guilabel:`Add new token`.

Give your token a name, and select the scopes :guilabel:`api` and
:guilabel:`read_user`. Then click :guilabel:`Create personal access token`.

A green box will appear at the top of the page with the heading
:guilabel:`Your new personal access token`. Click the clipboard icon to the
left of the text field to copy the access token.

If you've installed the GitLab Workflow extension mentioned above, you will
see a GitLab icon on your VSCode side pane. Select the option to add an
access token. When prompted, enter the url :code:`https://gitlab.mousepawmedia.com`.
Then, when prompted for the access token, paste the token you copied
from GitLab.

Settings
=================================

You should go through the settings on
:menuselection:`File --> Preferences --> Settings`, and set them to your
preferences.
