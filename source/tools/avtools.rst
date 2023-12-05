.. _avtools:

Audio/Video Tools
###################################

We use a carefully-selected collection of open source tools and applications
for our audio/video production work at MousePaw Media.

..  warning:: These exact programs must be used for internal company work --
    no substitutions are permitted without written approval from the
    department lead.

These installation instructions are only written for Linux distributions based
on Ubuntu 22.04. If you're using a different Linux distribution, you may need
to adjust these instructions.

If you are working with **Design and Production** or **Mass Communication**,
we strongly recommend installing all of these tools, except for those under
the "Optional" section below, regardless of your specific assignments.

.. _avtools_codecs:

Codecs
=============================

MousePaw Media uses entirely open source audio and video formats (codecs).
However, from time to time, it may become necessary to import or export
from proprietary codecs, such as ``.MP3``.

To install support for these codecs, you should run the following...

..  code-block:: bash

    sudo apt install ubuntu-restricted-extras

When prompted, accept the EULA for the Microsoft Fonts (or reject it, if you
so choose). To do so, use your arrow keys to highlight an option, and press
:kbd:`ENTER` to select.

.. _avtools_audacity:

Audacity (v2.3 or later)
=============================

-*Audacity** is a robust open source audio editor, on par with tools like
Adobe Audition®.

It can be installed in one of several ways (select ONE):

It's available from the main repositories on Debian-based systems:

..  code-block:: bash

    sudo apt install audacity

It's available via Snapcraft:

..  code-block:: bash

    sudo snap install audacity

Or, install via Flatpak from Flathub:

..  code-block:: bash

    flatpak install flathub org.audacityteam.Audacity

- Official Website: `<https://www.audacityteam.org/>`_
- Documentation: `<https://manual.audacityteam.org/>`_
- Tutorials: `<https://wiki.audacityteam.org/wiki/Category:Tutorial>`_

.. _avtools_handbrake:

Handbrake (v1.3 or later)
=============================

-*Handbrake** converts video between formats, as well as adjusting
resolution, aspect ratio, and many more advanced options.

It can be installed in one of several ways (select ONE):

It's available from the main repositories on Debian-based systems:

..  code-block:: bash

    sudo apt install handbrake

It's available via Snapcraft:

..  code-block:: bash

    sudo snap install handbrake-jz

Or, install via Flatpak from Flathub:

..  code-block:: bash

    flatpak install flathub fr.handbrake.ghb

- Official Website: `<https://handbrake.fr/>`_
- Documentation: `<https://handbrake.fr/docs/>`_

.. _avtools_kdenlive:

Kdenlive (v19.12 or later)
=============================

-*Kdenlive** is an open source, non-linear video editor. It has support for
multiple codecs, and provides many useful effects and tools for professional
video editing.

It can be installed in one of several ways (select ONE):

It's available from the main repositories on Debian-based systems:

..  code-block:: bash

    sudo apt install kdenlive

It's available via Snapcraft:

..  code-block:: bash

    sudo snap install kdenlive

Or, install via Flatpak from Flathub:

..  code-block:: bash

    flatpak install flathub org.kde.kdenlive

- Official Website: `<https://kdenlive.org/>`_
- Documentation: `<https://userbase.kde.org/Kdenlive/Manual>`_
- Tutorials: `<https://kdenlive.org/en/toolbox/>`_

.. _avtools_musescore:

MuseScore (v3.2 or later)
=============================

-*MuseScore** is a free and open source sheet music authoring program, with
support for MIDI input and playback. It is a near drop-in replacement for
Finale® software, sporting full support of all notation for sheet music,
including guitar tablatures, lyrics, linked parts, and multiple instruments.

All our original music is written in MuseScore.

It can be installed in one of several ways (select ONE):

It's available from the main repositories on Debian-based systems:

..  code-block:: bash

    sudo apt install musescore3

It's available via Snapcraft:

..  code-block:: bash

    sudo snap install musescore

Or, install via Flatpak from Flathub:

..  code-block:: bash

    flatpak install flathub org.musescore.MuseScore

.. _avtools_op:

Optional Software
=============================

If you're going to be doing heavy-duty music or audio production, and need
something more powerful than Audacity, check out these applications.

..  note:: If you have no need for these tools, just skip this section.

.. _avtools_op_ardour:

Ardour (v5) and JACK
-----------------------------

-*Ardour** is a full-fledged Digital Audio Workstation (DAW), similar to
programs like Avid Pro Tools® or Apple Logic Pro®. It is free on Ubuntu, and one
of the most inexpensive options for Windows and Mac.

Ardour relies on **JACK Audio Connection Kit [JACK]**, a powerful, low-latency
sound server. We'll be setting that up as well.

..  code-block:: bash

    sudo apt install ardour5 qjackctl pulseaudio-module-jack

That command will install all three packages, plus their dependencies. When you
reach the "Configuring jackd2" screen, we recommend selecting ``<Yes>``.
(To select an option, highlight your choice with your arrow keys and press
:kbd:`ENTER`.)

Now we need to change some limits, so Ardour can run.

..  code-block:: bash

    sudo adduser $USER audio
    sudo nano /etc/security/limits.conf

Ensure the following line is in the file. If you don't see it, add it towards
the bottom of the file, just above the ``# END OF FILE`` line.

..  code-block:: bash

    @audio          -       memlock         unlimited

Save and quit by pressing :kbd:`Ctrl+O`, :kbd:`ENTER`, and :kbd:`Ctrl+X`.

You can now control JACK from **Qjackctl**. You shouldn't need to change any
of its settings in ``Setup...``; the defaults work on Ubuntu 20.04. You can
route audio between your different inputs and outputs using the ``Connect``
option, and then you can start JACK with ``Start``.

..  note:: Your normal system sound will stop while JACK is running, as it will
    take over sound playback from PulseAudio, the system default sound server.

-*Ardour**
- Official Website: `<https://ardour.org/>`_
- Documentation: `<http://manual.ardour.org/toc/>`_
- Tutorials: `<http://write.flossmanuals.net/ardour/introduction/>`_

-*JACK**
- Official Website: `<http://www.jackaudio.org/>`_

-*QJackCtl**
- Official Website: `<https://qjackctl.sourceforge.io/>`_

.. _avtools_audiorecorder:

Audio Recorder (v3.1 or later)
===================================

-*Audio Recorder** is a simple tool for quickly recording audio directly from
the audio output or microphone.

We can install this from the official PPA.

..  code-block:: bash

    sudo add-apt-repository ppa:audio-recorder/ppa
    sudo apt install audio-recorder

- Official Website: `<https://launchpad.net/~audio-recorder>`_

.. _avtools_op_dvdstyler:

DVDStyler (3.1 or later)
------------------------------

-*DVDStyler** allows you to create production-quality DVDs with custom,
hand-crafted menus.

We will install DVDStyle fro the unofficial PPA.

..  code-block:: bash

    sudo add-apt-repository ppa:ubuntuhandbook1/dvdstyler
    sudo apt install dvdstyler

- Official Website: `<https://www.dvdstyler.org/en/>`_
- Documentation: `<https://www.dvdstyler.org/en/70>`_
- Tutorials: `<https://www.dvdstyler.org/en/documents>`_

.. _avtools_op_hydrogen:

Hydrogen (v1.0 or later)
-----------------------------

-*Hydrogen** is a drum machine with some very high quality samples. When used
properly, there is little to no discernable difference between a Hydrogen
drumtrack and a live drumtrack.

It can be installed in one of two ways (select ONE):

It's available from the main repositories on Debian-based systems, which
you can install along with all of its extra drumkits:

..  code-block:: bash

    sudo apt install hydrogen hydrogen-drumkits hydrogen-drumkits-effects

Or, install via Flatpak from Flathub:

..  code-block:: bash

    flatpak install flathub org.hydrogenmusic.Hydrogen

If you have installed Ardour and JACK, you may need to change Hydrogen to use
PulseAudio under ordinary circumstances. To do this, start Hydrogen. Go to
:menuselection:`Tools --> Preferences`. Select :guilabel:`Audio System`
and set it to ``PulseAudio`` for ordinary use.

..  note:: You can also select ``Jack`` if you want to route the output
    directly into Ardour, although this is rarely useful.

- Official Website: `<http://hydrogen-music.org/>`_
- Documentation: `<http://hydrogen.sourceforge.net/content/tutorial/manual_en.html>`_

.. _avtools_op_lmms:

LMMS (v1.2 or later)
-----------------------------

-*LMMS** is a synthesizer and digital audio workstation, similar in many ways
to Apple GarageBand®.

It can be installed in one of two ways (select ONE):

It's available from the main repositories on Debian-based systems:

..  code-block:: bash

    sudo apt install lmms

Or, install via Flatpak from Flathub:

..  code-block:: bash

    flatpak install flathub io.lmms.LMMS

In many cases, LMMS works best with JACK. (See :ref:`avtools_op_ardour`)

- Official Website: `<https://lmms.io/>`_
- Documentation & Tutorials: `<https://lmms.io/documentation/>`_

.. _avtools_op_obs:

Open Broadcaster Software (25.x or later)
--------------------------------------------

-*Open Broadcaster Software [OBS]** is considered one of the best streaming
and screen recording programs in existence. It has full support for webcams,
microphones, screen capture, and media playback. You can use it to record
to a file, or to stream live to most major streaming services.

It can be installed in one of several ways (select ONE):

It's available from the main repositories on Debian-based systems:

..  code-block:: bash

    sudo apt install obs-studio

It's available via Snapcraft:

..  code-block:: bash

    sudo snap install obs-studio

Or, install via Flatpak from Flathub:

..  code-block:: bash

    flatpak install flathub com.obsproject.Studio

- Official Website: `<https://obsproject.com/>`_
- Documentation & Tutorials: `<https://obsproject.com/wiki/>`_
