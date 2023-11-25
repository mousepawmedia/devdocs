Ubuntu Setup: Installing
################################

Making Some Decisions
==============================

..  note:: You should read the :doc:`/setup_linux/about` section before continuing.

Single Boot or Dual Boot?
-------------------------------

If you have a PC, your first decision is whether you want to keep Microsoft
Windows™. Millions of people work exclusively on Linux, including a few people
at MousePaw Media. However, if you rely on Windows-only or Mac-only software,
such as Microsoft Office™ or software from Adobe or Autodesk, you may want to
keep Microsoft Windows™ installed.

..  note:: You should consider whether you are actually dependent on the
    Windows-only or Mac-only software you use, or if a Linux alternative will
    do just as well. See :doc:`about`

If you want Linux to be your only operating system on the computer,
the installation process is a *lot* easier.

Otherwise, if you want to have both Windows and Ubuntu Linux on your machine,
you can install them side-by-side. To do this, you must have at least
200 GB of free space on your hard drive.

..  warning:: Whether you're single-booting or dual-booting, backup all of your
    files to external removable media**, such as an external hard drive or a
    flash drive! If something goes wrong (which it can), you'll need that backup.

..  note:: If you're a little intimidated by all of this, consider seeking out
    a tech-savvy friend, coworker, or relation to help you, *especially*
    if you're dual-booting.

If you're on a Mac, you could alternatively install Linux via Parallels,
although we've found this doesn't work as well as dual-boot.

Choosing a Distribution
--------------------------------

Below are links to several great Ubuntu-based operating systems, including
Ubuntu itself. They all run the same software and work the same at their
heart, so just pick one you like.

If you have absolutely no idea what to go for, just go with Ubuntu itself.
I'm writing these instructions on Ubuntu anyway!

If you're not sure which version to select, we recommend one of these three,
especially Pop!_OS:

- `Pop!_OS <https://pop.system76.com/>`_
- `Ubuntu <https://www.ubuntu.com/desktop>`_
- `Ubuntu MATE <https://ubuntu-mate.org/>`_

These are other options in the Ubuntu family:

- `elementaryOS <https://elementary.io/>`_
- `Kubuntu <http://www.kubuntu.org/>`_
- `Linux Mint <https://linuxmint.com/>`_ ← (There are actually three versions
  of Linux Mint. Cinnamon is the one that looks most like Windows.)
- `Lubuntu <http://lubuntu.net/>`_
- `Ubuntu Budgie <https://ubuntubudgie.org/>`_
- `Ubuntu Studio <http://ubuntustudio.org/>`_
- `Xubuntu <http://xubuntu.org/>`_

..  note:: Although we primarily recommend Pop!_OS, we'll be using the term
    "Ubuntu" throughout to avoid confusion. Pop!_OS is based on Ubuntu, and
    can be used in the same way.

Alterative Distributions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  sidebar:: Can I Use FreeBSD?

    FreeBSD is another UNIX operating system, which does *not* use the Linux
    kernel. Because of the technical similarity between Linux and
    FreeBSD, we *do* permit use of FreeBSD for official development work at
    MousePaw Media. However, as with any non-Debian operating system, you are
    solely responsible for your own technical support, and may need to build
    some required packages from source.

If you're a more experienced Linux user, there are hundreds of other varieties
of Linux that can be used.

However, **these are not officially supported by MousePaw Media for
development**, and we offer no in-house technical support for them. In some
cases, you'll need to compile some required software packages from source.

If you're up for the challenge, and are willing to be your own technical
support, you are welcome to use any variety of Linux you like for development
work at MousePaw Media.

Here's a few popular, non-Ubuntu varieties of Linux.

- `Arch Linux <https://www.archlinux.org/>`_
- `Debian <https://www.debian.org/>`_
- `CentOS <https://www.centos.org/>`_
- `Fedora <https://getfedora.org/>`_
- `OpenSUSE <https://www.opensuse.org/>`_
- `Solus <https://solus-project.com/>`_

Choosing a Version
----------------------------------

A new version of Ubuntu releases every six months. While it is always free
to upgrade, it isn't always easy, and sometimes you have to reinstall the
entire operating system in the process.

Every two years, Ubuntu releases a **Long-Term Support [LTS] version**,
which is supported with updates for five years. If you'd prefer stability
over cutting-edge new features, I'd recommend this option.

However, if you're tech-savvy and love experimenting with the latest features,
you might want to use the newest release.

..  important:: Our instructions and build environment are all based on the
    LTS release. If you use a newer version, you will be responsible for
    adjusting instructions for your release yourself.

Preparing for Installation
==============================

Downloading and Creating Installation Media
------------------------------------------------

Once you've selected your distro, go to the official download page on their
website. While you can usually download the ISO (disk image) file directly,
you should seriously consider using the Torrent. This option reduces the load
on the server, and it can be stopped and resumed whenever!

You can download torrents with `qBitTorrent <https://www.qbittorrent.org/>`_
(Win/Mac/Linux) or `Transmission <https://transmissionbt.com>`_ (Mac/Linux).

Once you've downloaded the ISO, burn it onto a blank DVD (if your computer
has a DVD drive), or make a bootable Flash drive following
`these instructions for Windows <https://www.ubuntu.com/download/desktop/create-a-usb-stick-on-windows>`_
or `these instructions for macOS <https://ubuntu.com/tutorials/tutorial-create-a-usb-stick-on-macos>`_.

Preparing For Disaster
---------------------------

We'd all like to believe that this process is flawless, but nothing with
computers is. You should have a backup plan in case something goes haywire.

1) **Make sure you have a backup of all your files!** Put this backup on a
removable device, such as an external hard drive or a flash drive (or set of
flash drives).

..  note:: If you're ditching Windows forever, congrats! You can skip
    steps 2-3.

2) **Find your Microsoft Windows™ recovery disk or flash drive.** If you want
to dual-boot, you'll want to have this handy in case something goes wrong
and Windows gets nuked.

..  sidebar:: Windows 10 Recovery

    Official Windows 10 install images can be
    `downloaded from microsoft.com <https://www.microsoft.com/en-us/software-download/windows10>`_.
    The Windows 10 key is generally installed on your computer's BIOS. You can,
    however, generally retrieve it using `the instructions from Microsoft <https://support.microsoft.com/en-us/help/10749/windows-product-key>`.

3) **Write down your Microsoft Windows™ license key.** For Windows 7 or 8, you
can recover this key using `Magical Jellybean KeyFinder <http://www.magicaljellybean.com/keyfinder/>`_.
You may want to write down any other software license keys that tool recovers,
as well.

4) **Write down a list of all your Windows software.** Make sure you have
install disks, registration info and/or keys, etc. You should probably do
this, even if you *are* ditching Windows. You may want to set up a VirtualBox
later!

Preparing for Dual-Boot on Windows
------------------------------------------

..  note:: If you're not dual-booting with Windows, you can skip this step.

In Windows, open up "Disk Management". On the table, find the disk and
partition (disk section) that has at least 200GB of free space. It is
probably marked `Active (System, Healthy, Primary Partition)`, although
you can certainly install Linux on a different hard drive or partition from
Windows.

Right-click that partition on the chart and click `Shrink Volume...`.
Under `Enter the amount of space to shrink in MB:`, enter the amount of
space (in MB) you want to set aside for Linux. There are 1000 MB in 1 GB, so
(200 GB = 200000 MB) and (250 GB = 250000 MB).

Preparing for Dual-Boot on Mac
------------------------------------------

..  TODO:: Write me.

Installing Linux
=============================

Turn Off Secure Boot
------------------------------

If you have a PC running Windows 8 or later, you may need to turn off Secure Boot
before you can install Linux.

..  note:: It is not possible to install Linux on Microsoft-branded computers,
    such as the Surface Pro.

To do this, go to the Power menu (where you shut down from), hold down SHIFT,
and click `Restart`. After a few moments, a menu screen will appear. Select
`Troubleshooting` → `Advanced Options` → `UEFI Firmware Settings`.

This will bring up the UEFI Settings control panel, which is independent of
the operating system. Every brand of computer has its own such control panel,
and they all tend to look a little different.

..  warning:: Be very careful in here.

Search through the options for "Secure Boot" and disable it.
**If you're dual-booting, do NOT turn off UEFI altogether!** Also, search
for "Fast Boot" (if the option is present) and disable that.

Change Boot Order
-----------------------------

..  note:: If you are on a PC running Windows 7 or earlier, you probably have
    BIOS instead of UEFI. To get to that, restart your computer. As the
    computer brand flashes on your screen at the beginning of startup, look
    for the key to press for "Setup". If you miss the chance to press it, just
    turn off the computer by the power button and try again.

Either on your UEFI or BIOS control panel, look for the option to set "boot
order," which denotes the order of devices to boot from. Set your Ubuntu
installation media (the DVD drive or bootable flash drive) as the first device
in that list.

Now press the key listed as "Save Changes and Exit". Your computer should
boot to the installation medium.

Testing Linux
-------------------------------

You will be given the option to "Try" the Linux operating system you selected.
This is called the "Live" version. **You should always run this first,** to
make sure it will work on your computer.

..  note:: Give it some time - it is actually loading the entire operating
    system from the DVD or flash drive into RAM, so it will be very slow.
    The final installed operating system will be much faster.

When the "Live" version of the operating system has booted, feel free to test
it out. At minimum, make sure you have a working internet connection, as you'll
need that for the installation to finish. If you can't get the internet working,
this may suggest that your computer's internet hardware is not compatible
with Linux.

When you're happy, start the "Install" program.

Installing Linux
--------------------------

The first screen will ensure you are connected to the internet and have enough
hard drive space for the installation. Check `Download updates while installing`
and `Install this third-party software`. Click `Continue`.

..  warning:: Be **very** careful what you select on the next screen!

For Single-Boot
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  sidebar:: Future-Proofing (Advanced)

    Personally, I find it helpful to have two system partitions, and a shared
    `/home` partition. This way, if there is an error in installing a new
    version of Ubuntu, I still have my old version untouched. To do this, select
    ``Something else`` for partitioning, create two 30-50 GB partitions, and
    allocate the rest for the `/home` partition.

If you want to **permanently remove Windows** and install Linux, select
"Erase disk and install Ubuntu". This is usually the best option for a
completely new install.

Alternatively, you can click `Something else` and set up the partitions
yourself. I personally recommend having a 50 GB `/` partition, and using the
rest as a separate `/home` partition. You can find more information about
setting up partitions on
`this page <https://help.ubuntu.com/community/DiskSpace>`_.

..  note:: As of Ubuntu 18.04, it is no longer necessary to set up a separate
    ``swap`` partition.

For Dual-Boot
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you're dual-booting, be very careful. Look for the section marked
`free space`, and click the `+` button to create a new partition.

We'll first set aside 50 GB for our system, so set the partition size to be
`50000` MB, `Primary` and `Beginning of this space`. Set `Use as` to `ext4`,
and `Mount point` to `/`. Confirm.

Next, we'll create the swap space, which is used as a sort of extension to
our RAM memory. Click `free space` again and click `+`. Set the partition
size equivalent to the amount of RAM you have. Select `End of this space`
and set `Use as` to `swap area`. Confirm.

Finally, we'll use the rest of the free space for our `/home` partition. Select
`free space` again and click `+`. Leave the size at the default, and leave
`Primary` and `Beginning of this space` selected. Set `Use as` to `ext4` and
`Mount point` to `/home`. Confirm.

..  important:: On the table, ensure that the checkmark under the `Format`
    column is only checked on those three partitions you just made! **DO NOT
    FORMAT ANY OTHER PARTITIONS!**

Click `Install Now`, and then read and confirm the dialog boxes.

During the Install
-------------------------

Your installation has started! While we wait, let's set a few options.

If you have an internet connection (you should), you can enter your city
in the box below the map. Then, click the option in the popup list. (If you
have too much trouble with this, just click your time zone on the map and
call it good.)

Next, select your language and keyboard layout. Chances are, you can leave the
defaults.

Finally, create your login credentials. Enter your full name in the top box.
Then, take this opportunity to think of a good name for your computer - you'll
see that name every time you open the Terminal. This is also the name that
will appear on local networks when you connect, so it's helpful to have a
unique and identifiable name.

Some computer names I've seen include `tardis` (that one's mine),
`bagofholding`, `enigma`, `cortex`, and `sunshine`. Just pick something that
makes you happy.

Third, pick a username. This is usually your first name, but it can be anything,
so long as it is composed only of lowercase letters and numbers, and the first
character is a lowercase letter.

Finally, choose a password. If you ever lost this password, you could reset
it with a little effort, but you really should pick one that is easy to
remember. At the same time, you should choose a password that is hard to guess.
(See "A Word About Passwords" below.)

Once that's done, just wait for the install to finish. There are some
interesting slides that will tell you more about Ubuntu Linux while you wait,
but don't plan on staring at the screen the whole time. The install can take
anywhere from 1-6 hours, depending on your internet connection speed.

A Word About Passwords
---------------------------------

Passwords don't have to be hard to remember. First, here are a few rules:

- **NEVER** use your name, or the name of a relative, friend, or pet.
- Don't use any form of the phrases "password", "secret", "letmein", or
  "iforgot" in the password. These are surprisingly common, and as such,
  they're the first thing a cracker tries.
- Use a mix of upper and lowercase letters, at least one number or symbol
  (ideally at least one of each). This doesn't mean things have to be in
  crazy or illogical positions. That said...
- Length is the *real* key to a good password!
- Real words *are* allowed! Passwords are cracked one character at a time,
  so the dictionary and the rules of grammar actually don't help the bad guys.

There are two easy (and fun) ways to make a good password that follows these
rules:

1) Think of your favorite song lyric, movie line, or poem. That whole thing
without spaces is your password! Seriously.
`Leanonmewhenyou'renotstrongI'llbeafriendI'llhelpyoucarryon!` is actually a
solid password. It's long, and has a mix of uppercase and lowercase letters and
symbols. Yet, it's easy to remember. You won't ever forget it!

2) Pick 3-4 random words out of the dictionary, preferably ones of moderate
length. You can choose ones that make you laugh, as they'll be easier to
remember. Then, mix in the month and year you created this password, and at
least one symbol. `01/17:ZealousJellyfishWrangler!` is a **very** strong
password, and after you've typed it a couple of times, it's hard to forget.

You can use `GRC Haystack <https://www.grc.com/haystack.htm>`_ and
`How Secure Is My Password? <https://howsecureismypassword.net/>`_ to test out
your password. Those sites are safe to enter your password on - they won't
store anything.

After The Install
-----------------------------

Once the installation is finished, it will prompt you to restart your computer.
Click the `Restart Now` button. When prompted, remove your installation media
(the DVD or USB) and press ENTER.

Your computer will now restart. If it hangs in the process, go ahead and turn
it off via the power button.

When you start the computer up, you may need to tap a key to view the Boot Menu
and select an operating system to boot to. This key is usually F11 or F12, but
you can find it when you first turn on the computer and see the manufacturer
logo.

If there is only the option to boot to the hard drive or an external device,
boot to the hard drive. You should be presented with an option to start
your Linux operating system or Windows (usually "Windows Boot Manager").

Once you've booted into Linux, continue to the next section of this tutorial.
