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

In general, when installing via apt-get install, I will list multiple packages,
each one separated from the last by a space. if one fails, the whole thing
fails. Just find and remove any packages that you cannot install, and the rest
will install just fine.

You can also copy and paste to the Terminal. Just be sure a) Not to copy the
$ in the command, and b) to use :kbd:`Ctrl+Shift+V` to paste in the Terminal.
(:kbd:`Ctrl+v` doesn't work.)

Intro to Ubuntu Unity
================================================

If you installed Ubuntu, as opposed to one of the other distros, you're using
the interface called "Unity". Using it is very simple:

- The bar on the left is called the **Launcher**.
- The top button on the Launcher is called the **Dash**. You can search for and
  open applications and files from here. You can also bring this up by tapping
  your **Super key** (or "Windows key").
- The second button on the Launcher is your **File Browser**. The package that
  Ubuntu Unity uses for this is technically called ``nautilus``.
- The toolbar at the top of the screen is called the **Menu Bar**. When you
  hover your mouse over it, the menus ("File", "Edit", etc.) for the program
  you're using appear.
- Tapping :kbd:`Left Alt` brings up the **HUD**, which you can use to search
  through the options in the menus.
- The right side of the Menu Bar is the **Panel**.
- The upper right button (at the far right of the Menu Bar) is the
  **Power Menu**. You can access System Settings and power functions here.\
- Hold down the Super Key to see all of the default keyboard shortcuts in Unity.

Updating System
================================================

Before we tackle anything else, let's install any waiting updates. This can
take a little bit of time, depending on your internet connection speed.

Ordinarily, you can just use the Software Updater program, but I like to
use the Terminal.

..  code-block:: bash

    $ sudo apt update
    $ sudo apt dist-upgrade
    $ sudo apt autoremove
    $ sudo apt autoclean

Let's break this down quickly. ``apt`` is a command-line program that installs
and manages packages on your system. The ``update`` command fetches the
latest list of packages available for installation. ``dist-upgrade``
installs all available upgrades, including new versions of software.

..  NOTE:: There is a minor debate about whether one should update via
    ``apt upgrade`` or ``apt dist-upgrade``. The former doesn't upgrade to a new
    major version of software, meaning things are less likely to break.
    However, many bugfixes and security fixes are addressed in new versions
    of software. Personally, in the years I've been using Linux, I've never
    regretted running ``apt dist-upgrade``.

Next, we run ``autoremove`` to have apt get rid of any unnecessary packages,
and ``autoclean`` to remove old installation data and other cruft (garbage).
You should run these regularly.

..  NOTE:: The Software Updater program doesn't run ``autoremove`` and ``autoclean``
    automatically - you'll need to do that yourself.

Installing Essential Packages
=============================================

There are a handful of tools that I find extremely useful in maintaining an
Ubuntu system. We'll install those now:

..  code-block:: bash

    $ sudo apt install gdebi synaptic apt-xapian-index gufw ubuntu-restricted-extras

    # If you're on Ubuntu itself, run this command as well.
    $ sudo apt install unity-tweak-tool compizconfig-settings-manager

While those install, here's a quick breakdown of what those packages are for...

- ``gdebi`` makes it easier to install packages from downloaded installers.
- ``synaptic`` enables easier package selection and installation.
- ``apt-xapian-index`` is needed by Synaptic.
- ``gufw`` is for managing your firewall.
- ``ubuntu-restricted-extras`` installs extra media codecs and tools that,
  while free, are not FOSS.
- ``unity-tweak-tool`` and ``compizconfig-settings-manager`` make it easier to
  change some of Ubuntu's hidden settings.

Before we can use synaptic, we need to run...

..  code-block:: bash

    $ sudo update-apt-xapian-index -vf

For the rest of this tutorial, you can either install packages via the
`sudo apt install <packagename>` command given, or you can
select and install them via Synaptic. It's up to you.

..  IMPORTANT:: **Restart your computer now!**

Security Stuff
==========================================

Firewall Settings
------------------------

In the previous section, we installed ``gufw`` for controlling our firewall.
You can now launch the "Firewall Configuration" application. It may also appear
in your System Settings.

Once you bring up Firewall Configuration, set the :guilabel:`Status` switch
to "On". You can open and close extra ports as needed on this window as well.

..  NOTE:: Unfortunately, the firewall does not change profiles automatically
    based on what network you connect to. Bear that in mind.

Virus Scanning
------------------------

While there are all of about 30 viruses for Linux in existence (and all of them
require you to give them your password), there are plenty of viruses for Windows
and Mac. To make sure you don't accidentally infect a non-Linux machine, it is
helpful to have an anti-virus. There's only one trusted source for that on Linux -
the open-source ClamAV.

..  code-block:: bash

    $ sudo apt install clamav clamtk clamav-daemon

You will need to run Clam manually when you want to check for viruses.

Office Applications
==========================================

Updating LibreOffice
-----------------------------

The version of LibreOffice that ships with Ubuntu is slightly older than the
latest stable version. To get that stable version, you'll need to add the
**PPA**, or "personal package archive," for LibreOffice "Fresh", so ``apt``
can download the newer version.

Once you've added the new PPA, update the apt package lists and install all
the now-available updates.

..  code-block:: bash

    $ sudo add-apt-repository ppa:libreoffice/ppa
    $ sudo apt update
    $ sudo apt dist-upgrade

Alternative Office Suites
-----------------------------

While we use LibreOffice for all of our document work at MousePaw Games, there
are two other office suites available that might be useful to you personally.

Calligra
^^^^^^^^^^^^^^^^

One is `Calligra <https://duckduckgo.com/?q=calligra&t=opera&ia=web>`_,
which sports a rather usual interface. Some people love it, and some people
hate it. If you'd like to try it out, just install the ``calligra`` package.

WPS Office
^^^^^^^^^^^^^^^^^

If you're pining for the familiar design of Microsoft Office™, check out the
proprietary **WPS Office** from China-based Kingsoft. Although it's not FOSS,
it is free on Linux. It can open Microsoft Office formats, and offers familiar
replacements for Word, Excel, and PowerPoint. Unfortunately, WPS Office cannot
work with the OpenDocument formats (`*.odt`, `*.ods`, etc.).

You can download WPS Office from `their website <https://www.wps.com/>`_. Save
the ``*.deb`` file to your computer, and then browse to it in your File Browser.
Right-click it and open with "GDebi Package Installer", then click Install.

Web Browser
==============================

Installing Chrome or Opera
--------------------------------

Firefox is probably installed by default. However, if you want Google Chrome
or Opera, you can install those easily.

..  NOTE:: Google Chrome has some major bugs that appear on certain Mac and
    Linux systems. If you're looking for a different browser that supports
    Chrome Extensions, try Opera. (It's also faster!)

You can download `Google Chrome here <https://www.google.com/chrome/browser/desktop/>`_,
and `Opera here <http://www.opera.com/>`_. For either, save the ``*.deb`` file
to your computer, browse to it in your File Browser, right-click, and open it
with "GDebi Package Installer."

Installing Adobe Flash
---------------------------------

After not supporting Linux for a few years, Adobe has started offering an
official package for the Adobe Flash plugin. This package works for Firefox,
Chrome, and Opera.

To install, you first must enable the Canonical Partners package repository. You
can do so by going to Power Menu → :guilabel:`System Settings` →
:guilabel:`Software and Updates`. Go to the :guilabel:`Other Software` tab and
check the box next to ``Canonical Partners``. (NOT next to "Canonical Partners
(Source code).") Click :guilabel:`Close`. When prompted, allow the repository
to update.

Next, install...

..  code-block:: bash

    sudo apt install adobe-flashplugin adobe-flash-properties-gtk

Restart your web browser, and then go to `<https://www.adobe.com/software/flash/about/>`_
to test the installation.

..  IMPORTANT:: Adobe Flash will now manage its own updates. You should never
    have to download any update of Flash through the web browser. On Linux,
    Flash can only be installed and updated through ``apt``.

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

In Firefox
^^^^^^^^^^^^^^^^^
Go to the menu (upper right of Firefox) and click :guilabel:`Preferences`.
Click :guilabel:`Search` on the left side, and select "DuckDuckGo" from the
menu under "Default Search Engine".

In Opera
^^^^^^^^^^^^^^^^^^
Go to :guilabel:`Edit` and :guilabel:`Preferences...`. Select
:guilabel:`Browser` on the left side. Under "Search", select "DuckDuckGo"
from the drop-down list.

In Chrome
^^^^^^^^^^^^^^^^^^
Go to the menu (upper right of Chrome) and click :guilabel:`Settings`. Scroll
down to "Search". If "DuckDuckGo" is not in the list (which, suspiciously,
it has been absent from for years), click :guilabel:`Manage search engines...`.
Towards the bottom, in the box marked "Add a new search engine", type
"DuckDuckGo". For "Keyword" type "duckduckgo.com", and for "URL" type
"https://duckduckgo.com/". Press :kbd:`Enter`. Then, hover over the new entry
in the list and click :guilabel:`Make default`.

DVD Playback
====================================

..  WARNING:: Under the DMCA, it is technically *illegal* to play any disc with
    copy protection on Linux (unless you use the non-free Fluendo DVD Player
    application). DVDs with copy protection have a label indicating it on the
    case, usually on the bottom of the back of the case. To date, there has
    never been legal action taken against a user for playing copy-protected
    DVDs on Linux, and the viability of the law is under heavy debate. Just
    be aware of the law and decide for yourself.

Want to play DVDs? Yes, Ubuntu can do that, but you have to set it up first.

..  code-block:: bash

    sudo apt install libdvd-pkg

After installation, follow the instructions on the screen.

While the default movie player works fine in Ubuntu, consider installing ``vlc``
if you want additional features for video and DVD playback.

Customizing Ubuntu
=====================================

Themes and Icons
-------------------------------------

There are some amazing themes and icons available from
`Noobslab <http://www.noobslab.com/p/themes-icons.html>`_. However, I
believe the best themes and icons are the ones from the Ravefinity project
(and a few others). Having tried a number of themes, I find that these provide
the cleanest and most consistent results. Plus, they come in a wide variety of
colors!

Installing Themes and Icons
---------------------------------------

If you're on any of the Ubuntu-based distros (besides elementaryOS), you can
install these themes with the following commands. The lines starting with
`#` are comments describing the command after it. Skip the commands that
don't apply to you.

..  code-block:: bash

    sudo add-apt-repository ppa:noobslab/themes
    sudo add-apt-repository ppa:ravefinity-project/ppa

    # Install icon sets...
    sudo apt install vivacious-colors vibrancy-colors

    # Install themes...
    sudo apt install radiance-flat-colors radiance-colors ambiance-blackout-colors ambiance-blackout-flat-colors ambiance-colors ambiance-crunchy ambiance-flat-colors ambiance-lime vivacious-colors-gtk-dark vivacious-colors-gtk-light ambiance-radiance-xfce-lxde

    # Run if you're on Ubuntu Unity...
    sudo apt install vivacious-unity-gtk-dark vivacious-unity-gtk-light

(I might be a little biased towards the Vibrancy icon set, as I contributed
several icons to it.)

Choosing a Theme (Unity)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Start the Unity Tweak Tool, and click :guilabel:`Themes`. Select a theme from
the list to view it. (The left and right list selections will always
automatically match each other.)

..  WARNING:: The "Ambiance Blackout" themes cause some CSS problems with
    Firefox, and make parts of the Opera interface unreadable. Be forewarned.

Next, select the :guilabel:`Icons` tab. The Vibrancy and Vivacious icon sets
come in many colors, but the main idea of both is to make all your icons look
unified. Use a "Dark" set if you're using an "Ambiance" theme, and a "Light"
set if you're using a "Radiance" theme.

Choosing a Theme (Linux Mint)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Go to your System Settings and select :guilabel:`Themes`. Try the different
options out to find a combination you like.

..  WARNING:: The "Ambiance Blackout" themes cause some CSS problems with
    Firefox, and make parts of the Opera interface unreadable. Be forewarned.

The Vibrancy and Vivacious icon sets come in many colors, but the main idea of
both is to make all your icons look unified. Use a "Dark" set if you're using
an "Ambiance" theme, and a "Light" set if you're using a "Radiance" theme.

Screensavers
------------------------------------------

Personally, I love screensavers. Besides being practical, they're *fun*.
Linux has hundreds of screensavers to choose from, so there's something for
everyone.

However, these screensavers are not present by default. Let's install them:

..  code-block:: bash

    sudo apt install xscreensaver xscreensaver-data xscreensaver-data-extra xscreensaver-gl xscreensaver-gl-extra  xscreensaver-screensaver-bsod
    sudo apt purge gnome-screensaver

Note, we had to completely remove ``gnome-screensaver`` from the system, to
prevent a conflict between the two screensaver programs.

If you're on Linux Mint, that's all you have to do! You can now go to
System Settings and :guilabel:`Screensavers` to select one.

If you're on one of the Ubuntu distros (Ubuntu, Xubuntu, Ubuntu Studio, etc),
you'll need to add XScreensaver to the list of programs to start automatically.
Look for the program "Startup Applications". Click :guilabel:`Add`. Under
"Name", type "Xscreensaver". For "Command", enter "xscreensaver&".
Click :guilabel:`Add` again, and then :guilabel:`Close`.

Now, start the program "Screensavers" and begin exploring your options! Most of
them even have lots of options you can tweak.

..  WARNING:: One of the screensavers, BSOD, simulates the "Blue Screen of
    Death" for various systems, including, Windows, Mac, Linux, BSD, and even
    ATMs and GLaDOS. While this is fun for computer nerds, it's probably a
    good idea to turn off the Linux-based BSODs in the screensaver settings,
    to avoid confusing yourself into hard-booting the computer in a panic.
    Also, warn your roommates, so *they* don't "helpfully" restart your machine
    for you. :)

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
Go to Power Menu → :guilabel:`System Settings` → :guilabel:`Keyboard`.
Select :guilabel:`Shortcuts` if necessary, and then go to :guilabel:`System`.
Double-click the ``Ctrl-Alt-Delete`` entry in the row for "Log out" and
press :kbd:`Ctrl+Alt+Backspace`. This will be the keyboard shortcut if you
want to quickly log off your computer.

Now, go to :guilabel:`Custom Shortcuts`. Click :guilabel:`+`. Set the name
to "System Monitor" and the command to "gnome-system-monitor". Press
:guilabel:`Apply`.

Finally, double-click ``Disabled`` on the "System Monitor" row in the list and press
:kbd:`Ctrl+Alt+Delete`.

..  NOTE:: If you're on Linux Mint, :kbd:`Ctrl+Alt+Delete` is already used for
    restarting the system. You can probably shut this off in Startup Applications.

Compose Key
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

One of the coolest features in Ubuntu is the ability to type accented
characters very quickly. To do this, you'll need to turn on your Compose Key.
Still in the Shortcuts control pane from the previous step, click
:guilabel:`Typing`. Click the keyboard shortcut (probably "Disabled") on the
"Compose Key" row, and select "Right Alt".

Unity Tweaks
-------------------------------------------

As you may have noticed, the Unity Tweak Tool on Ubuntu Unity offers a lot of
hidden options. Here are a few to consider:

- You can change a lot about the panel, including displaying your name or the date,
  setting the clock to 12 or 24-hour time, and showing the volume and power
  icons.
- You can set Window Snapping to place a window in a given corner if you
  drag-and-drop it to said corner.
- You can set the Launcher to be on the left or bottom of the screen, and to
  autohide.

..  WARNING:: Do NOT set the Launcher position to "Bottom" **AND** set
    Auto-hide Reveal location to "Top left corner". It doesn't work.

-------------------------------------

This should be enough information to get you started! From here, you can keep
adjusting things to your liking. Continue to the next step when you're ready
to set up your work tools.
