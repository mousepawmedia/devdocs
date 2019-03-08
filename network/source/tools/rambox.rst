.. _rambox:

Rambox
################################

It is possible to monitor Phabricator, as well as dozens of other chat services,
using **Rambox**. We'll be using the free version,
`Rambox Community Edition <https://rambox.pro/#ce`_, in this guide. If you
have Rambox Pro instead, the instructions may be slightly different.

.. _rambox_install:

Installation
================================

Download the latest version of Rambox Community Edition from the
`official GitHub Releases <https://github.com/ramboxapp/community-edition/releases>`_
page.

For Ubuntu or Debian Linux, you will want the file
:code:`Rambox-x.x.x-linux-amd64.deb` (where `x.x.x` is the version number).
Once you've downloaded the file, double-click on it to open it in your system's
package manager. Alternatively, you can run the following in the Terminal
(assuming you saved the file in :file:`Downloads/`), changing `x.x.x` to the
version number.

..  code-block:: bash

    $ sudo dpkg -i ~/Downloads/Rambox-x.x.x-linux-amd64.deb

For Windows, Mac, or other versions of Linux, download and run the appropriate
installer for your system.

.. _rambox_setup:

First-Time Setup
================================

.. _rambox_setup_settings:

Settings
--------------------------------

After you start Rambox for the first time, click the gear in the upper-right
corner of the main page (below the heart). I recommend the following settings:

* :guilabel:`Start automatically on system startup`: YES

* :guilabel:`Start minimized`: YES

* :guilabel:`Display behaviour`: Show in Taskbar and Tray Icon

* :guilabel:`When closing the main window`: Keep in tray

* :guilabel:`Show System Tray indicator on unread messages`: YES

* :guilabel:`Flash Taskbar on new message`: YES

Adjust the rest of the settings to your preference, and then click
:guilabel:`Save`.

.. _rambox_setup_phab:

Adding Phabricator Service
---------------------------------

We'll add Phabricator to Rambox, using the main page of Conpherence as the
default landing page to make the most of the chat service. Notifications
from the chatrooms *and* the rest of Phabricator will pop up in Rambox.

On the main tab of Rambox, where all the supported services are listed,
scroll down to the bottom and click :guilabel:`_Custom Service`.

Fill in the following details:

* :guilabel:`Name`: :code:`Phabricator`
* :guilabel:`URL`: :code:`https://phabricator.mousepawmedia.net/conpherence`
* :guilabel:`Logo`: :code:`https://mousepawmedia.net/img/foss/phabricator_icon.png`
* :guilabel:`Mute all sounds`: NO
* :guilabel:`Show notifications`: YES
* :guilabel:`Display tab unread counter`: YES
* :guilabel:`Include in global unread counter`: YES

Click :guilabel:`Add _CustomService`. You'll now see a tab for Phabricator
at the top of Rambox.

In the Phabricator tab, click on your profile picture towards the upper right,
and click :guilabel:`Settings`. On the left, select :guilabel:`Notifications`.
Select the option :code:`Web and Desktop`, and save. Then, click
:guilabel:`Enable Desktop Notifications` if the button is present.

Finally, click :guilabel:`Send Test Notification` in the upper-right corner
to test. Then, click :guilabel:`Save Preference`.

.. _rambox_setup_other:

Other Services
---------------------------------

Click on the Rambox logo at the left of the top toolbar, and add any other
services you want. You can always temporarily disable a service by deselecting
its checkbox on the list at the right.

Here's a few that might be of interest to you:

* **Discord** is a chat and voice service geared towards gamers. MousePaw Media
  has an official channel that we occasionally use for group coding.
* **Squirrelmail** is our webmail service. If you don't want to hook up
  an email client, you can use this.
* If you don't want to install HexChat, or always forget to start it up,
  you can use the **KiwiIRC** service to connect to IRC. (Alternatively,
  you can also use the bare-bones **FreeNode** service.)

.. _rambox_setup_ambient:

Ambient Sound Services
---------------------------------

Although it wasn't originally created for this purpose, I find Rambox
incredibly useful for keeping my favorite ambient sound generator services close
at hand. My personal favorites are **MyNoise.net** and **Noisli**.

The settings for each are below. You'll notice that I set the tabs to appear on
the *right* side of the toolbar, and to only show their icon. This helps me
differentiate them from my chat services.

Noisli
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Noisli is a free service that offers sounds like rain, thunder, wind, water,
waves, and (my personal favorite), coffee shop.

On the main tab of Rambox, where all the supported services are listed,
scroll down to the bottom and click :guilabel:`_Custom Service`.

Fill in the following details:

* :guilabel:`Name`: :code:`Noisli`
* :guilabel:`URL`: :code:`https://www.noisli.com/`
* :guilabel:`Logo`: :code:`https://www.noisli.com/assets/noisli-app-icon.png`
* :guilabel:`Align To Right`: YES
* :guilabel:`Show service name in Tab`: YES
* :guilabel:`Mute all sounds`: NO

Click :guilabel:`Add _CustomService`. You'll now see a tab for Noisli
at the top of Rambox.

myNoise.net
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

myNoise.net is a free service with a paid membership option. It has
*hundreds* of high quality sound generators, including many environmental
sounds recorded live on location. Each sound generator has a multitude of
sliders and controls to allow you to customize each to your heart's desire.

I do highly recommend buying a membership if you can afford it
(minimum $5 one-time for unlimited lifetime membership)! The premium features
are worth the investment, and it helps keep the site running.

..  NOTE:: Make sure you're on **mynoise.net**. The dot-com version is an
    unrelated domain-squatting scam.

On the main tab of Rambox, where all the supported services are listed,
scroll down to the bottom and click :guilabel:`_Custom Service`.

Fill in the following details:

* :guilabel:`Name`: :code:`myNoise.net`
* :guilabel:`URL`: :code:`https://mynoise.net/noiseMachines.php`
* :guilabel:`Logo`: :code:`https://mynoise.net/Pix/logo_256.png`
* :guilabel:`Align To Right`: YES
* :guilabel:`Show service name in Tab`: YES
* :guilabel:`Mute all sounds`: NO

Click :guilabel:`Add _CustomService`. You'll now see a tab for myNoise.net
at the top of Rambox.
