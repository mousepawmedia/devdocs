.. MousePaw Media Development Documentation master file, created by
   sphinx-quickstart on Mon May 30 15:25:19 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

MousePaw Media Development Documentation
##########################################

About the Development Docs
=============================
Development at MousePaw Media takes place on a carefully designed technology
stack, engineered for efficiency and ease-of-use. To ensure that all developers
are familiar with these technologies and methodologies, we maintain this
documentation.

Our Development Documentation is not a replacement for any official
documentation. Our focus here is to describe our particular instances and usages
of our stack's technologies.

Guides
---------------------------

..  toctree::
    :maxdepth: 1

    guides/contrib
    guides/environment
    guides/building
    guides/revision
    guides/taskfinder
    guides/taskcreate
    guides/reviewing
    guides/livecollab
    guides/staff

Network
---------------------------

..  toctree::
    :maxdepth: 2

    network/phabricator
    network/programmingworkflow
    network/etherpad
    network/nextcloud
    network/harbormaster_jenkins
    tools/irc

Development Tools
---------------------------

..  toctree::
    :maxdepth: 2

    tools/gitarc
    tools/cpp
    tools/python
    tools/sphinx
    tools/devtools
    tools/docker

IDEs and Editors
---------------------------

We officially support four major code editors for our projects. Each language
setup tutorial will independently discuss these options.

If you can't decide on one, try :ref:`vscode`!

..  toctree::
    :maxdepth: 2

    tools/atom
    tools/geany
    tools/vscode

Design Tools
---------------------------

We use a number of additional tools for our graphics design and audio/video
work.

..  toctree::
    :maxdepth: 2

    tools/graphics
    tools/avtools
    tools/fonts
    tools/wacom

Ubuntu Installation
--------------------------

..  toctree::
    :maxdepth: 2

    setup_linux/about
    setup_linux/install
    setup_linux/first_steps

Other
--------------------------

..  toctree::
    :maxdepth: 2

    admin/admin
    serverconfig/config

Indices and tables
==================

..  toctree::
    :maxdepth: 1

    glossary

* :ref:`genindex`
* :ref:`search`

Feedback
===========================
This documentation is written and maintained by the MousePaw Media Standards
Board. Feedback is welcome via email (developers@mousepawmedia.com).

You can also find us on Matrix in `#lobby:chat.mousepawmedia.com`, or on
the `#mousepawmedia channel` on Libera.Chat IRC.

Pull requests to this repository are not accepted. If you wish to propose a
change, email your patch to us at the address above.

For more information about contributing to MousePaw Media
projects, see `mousepawmedia.com/developers <https://www.mousepawmedia.com/developers>`_.
