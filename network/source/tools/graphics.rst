.. _graphics:

Graphics Design Tools
###################################

We use a carefully-selected collection of open source tools and applications
for our graphics design work at MousePaw Media.

..  WARNING:: These exact programs must be used for internal company work --
    no substitutions are permitted without written approval from the
    department lead.

These installation instructions are only written for Linux distributions based
on Ubuntu 18.04. If you're using a different Linux distribution, you may need
to adjust these instructions.

If you are working with **Design and Production** or **Mass Communication**,
we strongly recommend installing all of these tools, regardless of your
specific assignments.

Birdfont (v2.x)
=====================================

**Birdfont** is an open source font editor and creator. It allows you to design
fonts with vector graphics, and export them in the TTF, EOT, and SVG font
formats.

Birdfont can be installed via...

..  code-block:: bash

    $ sudo apt install birdfont

* Official Website: `<https://birdfont.org/>`_
* Tutorials: `<https://birdfont.org/doku/doku.php/tutorials>`_

Blender (v2.79)
=====================================

**Blender** is the leading open source 3D modeling studio, and is used
by professionals and hobbyists alike.

The latest version of Blender can always be installed from a PPA on Ubuntu.

..  code-block:: bash

    $ sudo add-apt-repository ppa:thomas-schiex/blender
    $ sudo apt install blender

* Official Website: `<https://www.blender.org/>`_
* Documentation: `<https://docs.blender.org/manual/en/dev/>`_
* Tutorials: `<https://www.blender.org/support/tutorials/>`_

Darktable (v2.4)
=====================================

**Darktable** is a RAW photo processor, generally considered on-par with
Adobe Lightroom®. It gives you an extensive set of professional photography
editing tools.

The latest version of Darkroom is available from the official PPA on Ubuntu.

..  code-block:: bash

    $ sudo add-apt-repository ppa:pmjdebruijn/darktable-release
    $ sudo apt install darktable

* Official Website: `<https://www.darktable.org/>`_
* Official Documentation: `<https://www.darktable.org/usermanual/en/>`_
* Tutorials: `<https://www.youtube.com/playlist?list=PLmvlUro_Up1NBX7VK8UUuyWo1B468zEA0>`_

GIMP (v2.10)
=====================================

The GNU Image Manipulation Program, or **GIMP**, is a feature-rich graphics
editing tool, largely analogous to Adobe Photoshop®.

..  code-block:: bash

    $ sudo apt remove gimp
    $ sudo add-apt-repository ppa:otto-kesselgulasch/gimp
    $ sudo apt install gimp

Upon first launch, you may notice that GIMP has three windows, like the
classic Adobe Photoshop®. We **strongly** recommend switching to single-window
mode. Click the :guilabel:`Windows` menu and select
:guilabel:`Single-Window Mode`.

* Official Website: `<https://www.gimp.org/>`_
* Official Documentation: `<https://docs.gimp.org/2.10/en/>`_
* Tutorials: `<https://www.gimp.org/tutorials/>`_

Gpick (v2.x)
=====================================

Gpick is a color picker and palette editor, which can import and export in
multiple formats, including the :code:`*.gpl` palette format that GIMP and
Inkscape use.

Gpick is best installed from the main Ubuntu package repositories.

..  code-block:: bash

    $ sudo apt install gpick

* Official Website: `<http://www.gpick.org/>`_

Inkscape (v0.92)
=====================================

**Inkscape** is a professional-quality vector graphics editing program,
analogous to Adobe Illustrator®. It can be used to create SVG (Scalable Vector
Graphics) files, and can export to most major graphics formats.

The latest version of Inkscape is always available from the official PPA...

..  code-block:: bash

    $ sudo add-apt-repository ppa:inkscape.dev/stable
    $ sudo apt install inkscape

* Official Website: `<https://inkscape.org/>`_
* Official Documentation: `<http://tavmjong.free.fr/INKSCAPE/MANUAL/html/>`_
* Tutorials: `<https://inkscape.org/en/learn/tutorials/>`_

Krita (v4.0.x)
=====================================

**Krita** is a vast and powerful raster drawing and painting application,
intended to be on-par with Adobe Photoshop®. It has an extensive collection
of brushes and tools for both realistic and hyper-realistic painting, and
sports excellent graphics tablet support.

The latest version of Krita is always available from the official PPA...

..  code-block:: bash

    $ sudo add-apt-repository ppa:kritalime/ppa
    $ sudo apt install krita

* Official Website: `<https://krita.org/>`_
* Official Documentation: `<https://docs.krita.org/en/user_manual.html#user-manual>`_
* Tutorials: `<https://docs.krita.org/en/>`_

MyPaint (v1.2.0)
=====================================

**MyPaint** is a painting and drawing application, best suited for situations
where you just want to start drawing! It supports graphics tablets and offers
a sizable collection of brushes.

MyPaint can just be installed from the core Ubuntu package repositories...

..  code-block:: bash

    $ sudo apt install mypaint

* Official Website: `<http://mypaint.org/>`_
* Official Documentation: `<https://github.com/mypaint/mypaint/wiki/v1.2-User-Manual>`_

Scribus (v1.4.x)
=====================================

Scribus is an open source desktop publishing and page layout program. It is
meant to functionally replace Adobe InDesign®, and supports many publishing
and design industry standards.

The latest version of Scribus can be installed from its official PPA...

..  code-block:: bash

    $ sudo add-apt-repository ppa:scribus/ppa
    $ sudo apt install scribus

* Official Website: `<https://www.scribus.net/>`_
* Official Documentation: `<https://wiki.scribus.net/canvas/Help:TOC>`_
* Tutorial: `<https://wiki.scribus.net/canvas/Get_Started_with_Scribus>`_