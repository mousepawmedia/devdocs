Ubuntu Setup: Configuring
##################################

Now that you have a fresh installation of Ubuntu Linux, let's install some
packages (applications) that will make your life easier.

Below are the packages to install, based on my experience setting up and
repairing Ubuntu machines.

Unless otherwise noted, run all of these in the Terminal, which you can bring up
via :kbd:`Ctrl+Alt+T`. Many commands require you to enter sudo, which will often
prompt you for your computer password. As you type, it will show nothing, not
even asterisks. Just finish typing it and press enter.

In general, when installing via :code:`apt install`, I will list multiple
packages, each one separated from the last by a space. if one fails, the whole
thing fails. Just find and remove any packages that you cannot install, and
the rest will install just fine.

You can also copy and paste to the Terminal. Just be sure a) Not to copy the
$ in the command, and b) to use :kbd:`Ctrl+Shift+V` to paste in the Terminal.
(:kbd:`Ctrl+v` doesn't work.)

Intro to Ubuntu
================================================

Pop!_OS
------------------------------------------------

You have GNOME3 by default, but if you want a different desktop environment,
it's easy to install:

For MATE, run the following in a terminal:

..  code-block:: bash

    sudo apt install mate-desktop-environment mate-desktop-environment-extras ubuntu-mate-desktop

If you've installed MATE, or are sticking with GNOME3, you can follow the
instructions from the rest of this tutorial as if you had Ubuntu MATE or
Ubuntu respectively.

For Cinnamon, run the following:

..  code-block:: bash

    sudo apt install cinnamon-desktop-environment

For KDE Plasma (from Kubuntu), run:

..  code-block:: bash

    sudo apt install kde-standard

For XFCE (on lightweight systems), run:

..  code-block:: bash

    sudo apt install xfce4 xfce4-goodies

For LXDE (another lightweight option), run:

..  code-block:: bash

    sudo apt install lxde

After installing, log out, and then at the login screen, find the option to
switch which desktop environment you're using.

Ubuntu MATE
------------------------------------------------

The ``Welcome`` application will walk you through the initial setup process
for Ubuntu MATE, and introduce you to many features in the operating system.

Early on, you should also start the ``MATE Tweak`` application, select
``Panel``, and try out each of the eight panel layouts. By default, the
``Familiar`` layout is used.

- If you liked the original Ubuntu Unity, try ``Mutiny``.
- If you like Microsoft Windows, try ``Redmond``.
- If you like Apple macOS, try ``Cupertino``.
- For the best of both worlds, try ``Pantheon`` (Jason McDonald's favorite).
- If you have a very small screen, try ``Netbook``.

Ubuntu (GNOME3)
------------------------------------------------

You can learn how to use Ubuntu from the official
`Ubuntu 22.04 Desktop Guide <https://help.ubuntu.com/lts/ubuntu-help/index.html>`_.

You can customize many aspects of Ubuntu MATE using the ``GNOME Tweak``
application.

..  important:: The rest of the instructions are universal to all varieties
    of Ubuntu 22.04.

Pop!_OS
------------------------------------------------

There are several additional features of Pop!_OS you should be aware of if
you're using that system. Take a look at the
`official Pop!_OS documentation <https://support.system76.com/#pop>`_
for more information.

Updating System
================================================

Before we tackle anything else, let's install any waiting updates. This can
take a little bit of time, depending on your internet connection speed.

Ordinarily, you can just use the Software Updater program, but I like to
use the Terminal.

..  code-block:: bash

    sudo apt update
    sudo apt full-upgrade
    sudo apt autoremove
    sudo apt autoclean

Let's break this down quickly. ``apt`` is a command-line program that installs
and manages packages on your system. The ``update`` command fetches the
latest list of packages available for installation. ``dist-upgrade``
installs all available upgrades, including new versions of software.

..  note:: There is a minor debate about whether one should update via
    ``apt upgrade`` or ``apt dist-upgrade``. The former doesn't upgrade to a new
    major version of software, meaning things are less likely to break.
    However, many bugfixes and security fixes are addressed in new versions
    of software. Personally, in the years I've been using Linux, I've never
    regretted running ``apt dist-upgrade``.

Next, we run ``autoremove`` to have apt get rid of any unnecessary packages,
and ``autoclean`` to remove old installation data and other cruft (garbage).
You should run these regularly.

..  note:: The Software Updater program doesn't run ``autoremove`` and ``autoclean``
    automatically - you'll need to do that yourself.

Installing Essential Packages
=============================================

There are a handful of tools that I find extremely useful in maintaining an
Ubuntu system. We'll install those now:

..  code-block:: bash

    sudo apt install gdebi synaptic apt-xapian-index gufw ubuntu-restricted-extras

While those install, here's a quick breakdown of what those packages are for...

- ``gdebi`` makes it easier to install packages from downloaded installers.
- ``synaptic`` enables easier package selection and installation.
- ``apt-xapian-index`` is needed by Synaptic.
- ``gufw`` is for managing your firewall.
- ``ubuntu-restricted-extras`` installs extra media codecs and tools that, while free, are not FOSS.

Before we can use synaptic, we need to run...

..  code-block:: bash

    sudo update-apt-xapian-index -vf

For the rest of this tutorial, you can either install packages via the
`sudo apt install <packagename>` command given, or you can
select and install them via Synaptic. It's up to you.

..  important:: **Restart your computer now!**

Alternative Package Sources
---------------------------------------

In addition to the `apt` package manager default to Debian-based systems
like Ubuntu, packages can be installed via Snapcraft, Flathub, or Appimage.

-*Snapcraft** runs applications in containers with partial or full isolation
from the rest of the system. It's portable, working on nearly all Linux systems.
Your system may already be configured to use Snapcraft, but you can make sure
by running the following:

..  code-block:: bash

    sudo apt install snapcraft snapd

You may need to restart after installation before installing anything with
Snapcraft.

You can search for packages with :code:`sudo snap search <package>`, and
install with :code:`sudo snap install <package>`.

-*Flathub** is a portable packaging format that works on most Linux systems.
It installs the package onto the system itself, instead of running it in a
container, so packages installed via Flatpak are likely to make better use of
system resources than Snapcraft.

You can install via the following:

..  code-block:: bash

    sudo apt install flatpak
    flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

You will need to restart after installation before installing anything with
Flatpak.

Flatpak maintains a store called `Flathub <https://flathub.org/home>`_, which
you can browse online. The installation and run commands are at the bottom of
each package's page (although Flatpak packages will appear on your menu like
any other program once installed.)

-*Appimage** is a packaging format that requires nothing else to be installed
on your system. Just make it executable, double-click it, and go! It's a
containerized format, much like snapcraft, but the system integration is more
portable and lightweight.

Security Stuff
==========================================

Firewall Settings
------------------------

In the previous section, we installed ``gufw`` for controlling our firewall.
You can now launch the "Firewall Configuration" application. It may also appear
in your System Settings.

Once you bring up Firewall Configuration, set the :guilabel:`Status` switch
to "On". You can open and close extra ports as needed on this window as well.

..  note:: Unfortunately, the firewall does not change profiles automatically
    based on what network you connect to. Bear that in mind.

Virus Scanning
------------------------

While there are very few viruses for Linux, all of which require a password to
be installed, there are plenty of viruses for Windows and Mac. To make sure
you don't accidentally infect a non-Linux machine, it is helpful to have an
anti-virus. There's only one trusted source for that on Linux -
the open-source ClamAV.

..  code-block:: bash

    sudo apt install clamav clamtk clamav-daemon

You will need to run Clam manually when you want to check for viruses.

Office Applications
==========================================

Updating LibreOffice
-----------------------------

The version of LibreOffice that ships with Ubuntu is slightly older than the
latest version. To get that "Fresh" version, you'll need to add the
-*PPA**, or "personal package archive," for LibreOffice "Fresh", so ``apt``
can download the newer version. Just be aware that this version can have
some bugs...but it also means you get the latest and greatest features.

Once you've added the new PPA, update the apt package lists and install all
the now-available updates.

..  code-block:: bash

    sudo add-apt-repository ppa:libreoffice/ppa
    sudo apt update
    sudo apt dist-upgrade

Alternative Office Suites
-----------------------------

While we use LibreOffice for all of our document work at MousePaw Media, there
are three other office suites available that might be useful to you personally.

Calligra
^^^^^^^^^^^^^^^^

One is `Calligra <https://duckduckgo.com/?q=calligra&t=opera&ia=web>`_,
which sports a rather usual interface. Some people love it, and some people
hate it. If you'd like to try it out, just install the ``calligra`` package.

FreeOffice
^^^^^^^^^^^^^^^^^

If you're pining for the familiar design of Microsoft Office™, consider the
proprietary **FreeOffice**. It is completely free on both Windows and Linux,
and is designed to completely replace Word, Excel, and PowerPoint. Best of all,
it is capable of working with both the Office and OpenDocument formats.

You can download FreeOffice from `the FreeOffice webside <www.freeoffice.com/>`_.
Click ``Download``, and register as a user to receive your product key. Then,
download ``FreeOffice for Linux`` from that page. Save the ``*.deb`` file to
your computer, and then browse to it in your File Browser. Right-click it and
open with "GDebi Package Installer", then click :guilabel:`Install`.

Web Browser
==============================

Installing Brave or Chrome
--------------------------------

-*Firefox** is installed by default, and works quite well. However, there are a
number of other browsers available. On Ubuntu MATE, these can all be installed
using the ``Software Boutique``. On Ubuntu, most can be installed from
``Software``. (Vivaldi must be installed by downloading the official ``.DEB``
file from their website.)

- **Brave**: A privacy-focused browser. `brave.com <https://www.brave.com/>`_
- **Chromium**: The open source version of the Google Chrome browser.
  (We recommend this over Google Chrome for privacy reasons.)
- **Vivaldi**: A highly customizable browser, based on Chromium. `vivaldi.com <https://vivaldi.com/>`_

A Note On Web Search Engines
--------------------------------------

When most people think 'web search', they say "Google!" However, there are
two problems with Google, and many other search engines:

1) Tracking: Google tracks your web history, search data, and a bunch of
   other personal stuff. A lot of this information is used to advertise to you.
2) The Bubble: Google will adjust search results to show you what it thinks
   you want to see, making it harder to find objective information.

DuckDuckGo is an open-source search engine that is dedicated to total privacy.
They will never track or use your history or web searches in any way. This
also means that the results you get for a web search will be the same as for
anyone else!

In addition to this, DuckDuckGo offers a number of unique features!

- Search inside thousands of websites with **bangs**: searching "!w butterflies"
  searches Wikipedia for "butterflies". Use "!a" for Amazon, "!g" for Google,
  "!nasa" for NASA, and thousands of others!
- One of the largest collections of "instant answers," all open source. Try
  "weather in spokane", "dancing cat gif", "python syntax", or "ubuntu unity
  cheatsheet" (*I* made that last one!)
- Customizable interface - colors, text, and layout.
- Always-on SSL search - no one else can spy on you either!
- The option to turn off all ads.
- Web of Trust integration.
- All results on one page.
- Search by region.

To set DuckDuckGo as your default search engine, follow these instructions:

In Brave
^^^^^^^^^^^^^^^^^^^^^^
Click the hamburger menu (upper right, just below the Lion shield), and
click :guilabel:`Settings...`. Select :guilabel:`Search` from the left.
Click the "DuckDuckGo" line to set that as the default search engine.

In Chrome/Chromium
^^^^^^^^^^^^^^^^^^^^^^
Go to the menu (upper right of Chrome) and click :guilabel:`Settings`. Scroll
down to "Search". If "DuckDuckGo" is not in the list (which, suspiciously,
it has been absent from for years), click :guilabel:`Manage search engines...`.
Towards the bottom, in the box marked "Add a new search engine", type
"DuckDuckGo". For "Keyword" type "duckduckgo.com", and for "URL" type
"https://duckduckgo.com/". Press :kbd:`Enter`. Then, hover over the new entry
in the list and click :guilabel:`Make default`.

In Firefox
^^^^^^^^^^^^^^^^^^^^^^
Go to the menu (upper right of Firefox) and click :guilabel:`Preferences`.
Click :guilabel:`Search` on the left side, and select "DuckDuckGo" from the
menu under "Default Search Engine".

In Vivaldi
^^^^^^^^^^^^^^^^^^^^^^
Go to the menu (upper left of Vivaldi) and :guilabel:`Tools` → :guilabel:`Settings`.
Select :guilabel:`Search`, select ``DuckDuckGo`` from the list of Search Engines,
and check the boxes :guilabel:`Set as Default Search` and
:guilabel:`Set as Private Search`. Click :guilabel:`Save`.

DVD Playback
====================================

..  warning:: Under the DMCA, it is technically *illegal* to play any disc with
    copy protection on Linux (unless you use the non-free Fluendo DVD Player
    application). DVDs with copy protection have a label indicating it on the
    case, usually on the bottom of the back of the case. To date, there has
    never been legal action taken against a user for playing copy-protected
    DVDs on Linux, and the viability of the law is under heavy debate. Just
    be aware of the law and decide for yourself.

Want to play DVDs? Yes, Ubuntu can do that, but you have to set it up first.

..  code-block:: bash

    sudo apt install libdvd-pkg
    sudo dpkg-reconfigure libdvd-pkg

..  note:: In my experience, mpv Media Player (package ``mpv``) provides the
    best video and DVD playback experience.

Helpful Settings
===========================================

Keyboard Settings
--------------------------------------------

There are two keyboard settings I always change when I set up Ubuntu.

Ctrl+Alt+Delete
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The first is to duplicate Windows' Ctrl+Alt+Del functionality, which is
sadly missing by default on Linux. Thankfully, you can set up custom
keyboard shortcuts for anything you like, so adding that in is easy!

First, we need to free up the keyboard shortcut for our use.
Bring up the ``Keyboard Shortcuts`` application (``Keyboard`` on GNOME).
Under the :guilabel:`Desktop` section, double-click the ``Ctrl-Alt-Delete``
entry in the row for "Log out" and press :kbd:`Ctrl+Alt+Backspace`. This will
be the keyboard shortcut if you want to quickly log off your computer.

Now, click :guilabel:`+ Add` at the bottom of the screen. Set the name to
"System Monitor" and the command to :code:`gnome-system-monitor` on Ubuntu,
or :code:`mate-system-monitor` on Ubuntu MATE. Press :guilabel:`Apply`.

Finally, double-click ``Disabled`` on the "System Monitor" row in the list and press
:kbd:`Ctrl+Alt+Delete`.

..  note:: If you're on Linux Mint, :kbd:`Ctrl+Alt+Delete` is already used for
    restarting the system. You can probably shut this off in Startup Applications.

Compose Key
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

One of the coolest features in Ubuntu is the ability to type accented
characters very quickly. To do this, you'll need to turn on your Compose Key.

Ubuntu MATE
""""""""""""""""""""""""""""""""""

Bring up the ``Keyboard`` preferences. Click :guilabel:`Layouts` and
:guilabel:`Options...`. Find and click :guilabel:`Position of Compose key`,
and check the box for :guilabel:`Right Alt`. Close both windows.

Ubuntu
"""""""""""""""""""""""""""""""""""

Make sure ``gnome-tweaks`` is installed, and then launch it. Go to the
:guilabel:`Keyboard & Mouse` section. Next to "Compose Key", click
:guilabel:`Disabled`. Toggle the switch at the top to the on position, and
then select the option for :guilabel:`Right Alt`.

Tweaks
-------------------------------------------

Both Ubuntu and Ubuntu MATE allow you to set a lot of hidden options.

On Ubuntu, you should install the ``gnome-tweaks`` package via
:code:`sudo apt install gnome-tweaks`, after which you can use the
``GNOME Tweaks`` application.

On Ubuntu MATE, ``MATE Tweaks`` is already installed by default.

Browse through your system's tweak tool and try out the different options.
Customize things to your liking!

-------------------------------------

This should be enough information to get you started! From here, you can keep
adjusting things to your tastes.

You should now go through :ref:`genv`, following the instructions for
Ubuntu Linux.
