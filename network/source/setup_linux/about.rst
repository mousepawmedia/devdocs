Ubuntu Setup: About Linux
################################

About the Linux Operating System
================================

What Is Linux?
-------------------------------

Linux is a FOSS (free and open source) **operating system**. It fills the
same role on the computer as Microsoft Windows™ or macOS™: operating the
computer's hardware, providing the main user interface, and running all
other software.

Linux is a dominant operating system in the technology, science, and academic
fields. The majority of web servers, many world governments, and a number of
major scientific institutes (including NASA and CERN) rely on some form or
another of Linux. Even Chromebooks and Android smartphones are technically
Linux devices.

What Is Debian?
-----------------------------

In reality, "Linux" only refers to the **kernel**, the heart of the operating
system ["OS"]. The rest of the OS is composed of hundreds of other pieces of
software. There are actually over a thousand different "distributions" (or
"distros") of Linux, most specially designed for a particular use or purpose.

"Debian" is one of the oldest and most stable Linux distros. It is well known
for working "out of the box" with very little modification, and for supporting
the vast majority of Linux software. In fact, the most popular Linux
distro, **Ubuntu**, is based on Debian, and several other distros have been
created based on Ubuntu.

What Distro Should I Choose?
----------------------------------

The best way to think of Linux is as a box of LEGOs - you decide what you want,
and snap the pieces together. Some computer nerds like to start with just the
Linux kernel itself and add each piece manually, but the rest of us like to
have a starting place. That's one of the reasons we use Ubuntu-based distros.

The most common distro at MousePaw Media is **Ubuntu** itself. It's unique,
friendly, and very easy-to-use. It takes about 15 minutes to get used to
Ubuntu, no matter what your computer experience level.

The second most common distro is **Linux Mint**, which looks a lot like
Windows 7 in its layout, but is still based on Ubuntu. It's highly
customizable, so you can make it look and act the way you want.

Other options include...

- **elementaryOS**: Perfect for Apple lovers.
- **Ubuntu MATE**: Offers a simple and customizable interface.
- **Kubuntu**: Packed with cool graphical effects and customization.
- **Lubuntu**: A lightweight option for slower machines.
- **Xubuntu**: A lighter, Apple-like system.

We'll get to the process of selecting and downloading an operating system
in :doc:`install`.

What Is Free and Open Source?
================================

Open Source vs Free Software
--------------------------------

**Open Source** refers in part to a program's source code being publicly
visible. However, the definition does not stop here: Open Source is a distinct
philosophy of software development. The Open Source Initiative [OSI] maintains
the `official definition <https://opensource.org/osd>`_, which emphasizes
**software freedom** - the right of *anyone* to freely view, modify, and
distribute the source code.

By contrast, the philosophy of **Free Software** ("free" as in "free speech",
not necessarily as in "free beer") emphasizes software freedom to an extreme.
Free Software posits that non-free ("proprietary") software, being any
software which does not adhere to the standards of software freedom, is
inherently morally wrong and should not be used. To this end, their main
license, the GNU Public License [GPL], requires that any source code that
uses GPL code to be licensed under the GPL as well, effectively preventing
proprietary use. Unfortunately, this also prevents developers from using GPL
code in many projects licensed under other open source licenses.

The Open Source Initiative [OSI] and the Free Software Foundation [FSF] have
long been at odds over this stance. While the OSI seeks to build bridges with
proprietary software companies, the FSF seeks to widen the chasm.

Thankfully, most of the politics only affects the software developers
themselves. Software developed under either philosophy, known collectively
as **Free and Open Source Software** [FOSS], makes up virtually the entire
Linux universe.

How Is This Possible?
--------------------------------

FOSS software is developed through volunteer efforts of developers around
the world. Usually, these are projects we start to solve a problem we
personally encountered, or just because we want to make something cool for
ourselves.

Free, As In Cost?
-------------------------------

It is important to note that, while most FOSS software costs nothing,
some software does have a price, especially business oriented software and
some games. However, you can rest assured there are no surprise costs. If you
can download and install the software without first paying for it, it's free.

All the same, you should consider donating to software projects that you use
a lot. Regardless of license, it costs money to make software, and most
developers donate their time.

Isn't "Open Source" Insecure?
---------------------------------

A common myth is that "open source" software is unsafe, because the bad
guys can read the source code. In reality, FOSS software is generally
*safer* than proprietary software, because there are more developers
watching for bugs and security issues. The bad guys don't need to read the
source to find holes (just look at all the viruses for Windows),
but the good guys do need to read the source to solve the problem.

Frequently Asked Questions
===================================

Can I run Windows software?
----------------------------------

Yes and no. Some Windows software can be run on Linux using a program called
`WINE <https://www.winehq.org/>`_, but results vary greatly. Their official
website has a `database <https://appdb.winehq.org/>`_ listing all the software
that's been tested with WINE, and reports on how well it runs.

Another option is to run a `VirtualBox <https://www.virtualbox.org>`_ of
Microsoft Windows™ inside of Linux. This means that you have a full
installation of Microsoft Windows™ that you can use via a special software
application on Linux. You do need to bring your own Microsoft Windows™
installation disc and license key. However, You can find free (and legal)
downloads of Windows 98 and prior through many abandonware catalogs.

Is there a Linux replacement for..?
---------------------------------------

Quite possibly! While Linux alternatives to popular Windows software look
different, they are often similar or identical in features. Here are a few
examples. You can find more at `alternativeto.net <http://alternativeto.net/>`_.

+--------------------+---------------------+
| Instead of...      | Try...              |
+====================+=====================+
| Microsoft Office   | LibreOffice/        |
|                    | WPS Office*         |
+--------------------+---------------------+
| iTunes             | Banshee/            |
|                    | Rhythmbox           |
+--------------------+---------------------+
| Adobe Photoshop    | GIMP                |
|                    | Krita               |
+--------------------+---------------------+
| Adobe Lightroom    | Darktable/          |
|                    | RawTherapee         |
+--------------------+---------------------+
| Adobe Illustrator  | Inkscape            |
+--------------------+---------------------+
| Adobe Animate      | Synfig              |
+--------------------+---------------------+
| Adobe Audition     | Audacity            |
+--------------------+---------------------+
| 3DS Max or Maya    | Blender             |
+--------------------+---------------------+
| Any video editor   | Kdenlive/           |
|                    | Openshot            |
+--------------------+---------------------+
| (Any DAW)          | LMMS/               |
|                    | Ardour              |
+--------------------+---------------------+
| AutoCAD            | FreeCAD/            |
|                    | BrisCAD**           |
+--------------------+---------------------+
| Finale             | MuseScore/          |
|                    | Lilypond            |
+--------------------+---------------------+

(\*Proprietary freeware.)

(\*\*Proprietary, not free.)

I have an iDevice. Will it still work with my PC?
---------------------------------------------------------

Unfortunately, Apple *blocked* music sync with Linux on all 4th Gen and later
iDevices. There are ways around this, however. I have an iPod Touch that runs
Google Music (a free service), that my Ubuntu machine automatically syncs with.

Will my <device> work?
----------------------------

Fitbit and Livescribe are two devices I'm frequently asked about. Although there
is demand from the community, these companies have shown no interest in
supporting Linux. There are some open-source efforts to get these devices
working with Linux, but it'll be a while.

On the other hand, Wacom tablets, many cameras, and most printers (just to
name a few things) work BEAUTIFULLY with Linux!

Ultimately, you should just do a web search to see if your device is compatible.

Does Linux support speech recognition?
------------------------------------------------

While there are a number of projects attempting to add this functionality to
Linux, there is no ready-to-use speech recognition software that works with
Linux. Unfortunately, Dragon Naturally Speaking has no plans to work on Linux
either.
