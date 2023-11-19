.. _wacom:

Wacom Tablets
###################################

If you are running Ubuntu 18.04 with the GNOME or Budgie desktop environments,
you have a Wacom control panel already. If you're on Ubuntu MATE 18.04, or
a number of other Ubuntu distros, you may not have a Wacom control panel at all.
In any case, some graphics tablet settings are hidden on all systems.

CEO/Lead Developer Jason C. McDonald wrote a few scripts for controlling
Wacom tablet settings. These are likely to be useful on *any* Ubuntu system.

Official documentation and information is available on each script's
GitHub repository.

* `WacomDisplayMap <https://github.com/CodeMouse92/WacomDisplayMap>`_
* `WacomRotate <https://github.com/CodeMouse92/WacomRotate>`_
* `WacomTouchToggle <https://github.com/CodeMouse92/WacomTouchToggle>`_

.. _wacom_install:

Installing Wacom Scripts
===================================

We will install all of the special Wacom scripts from their respective
GitHub repositories.

..  code-block:: bash

    sudo apt install pcregrep
    cd /usr/local/bin
    sudo wget https://github.com/CodeMouse92/WacomRotate/raw/master/wacomrotate
    sudo wget https://github.com/CodeMouse92/WacomTouchToggle/raw/master/wacomtouch
    sudo wget https://github.com/CodeMouse92/WacomDisplayMap/raw/master/wacommap
    sudo chmod +x wacom*

You can now run any of the scripts directly in the terminal.

.. _wacom_using:

Using Wacom Scripts
===================================

.. _wacom_using_touch:

Touch Toggle
-----------------------------------

If your Wacom tablet doubles as a touchpad, you'll often want to disable
this feature before you start drawing. That way, your hand won't throw off
your pen.

Turning touch off is simple. In a terminal, run...

..  code-block:: bash

    wacomtouch off

To turn it back on, run...

..  code-block:: bash

    wacomtouch on

.. _wacom_using_map:

Display Map
-----------------------------------

Ubuntu and Linux Mint offer control panels to map your tablet to a particular
monitor. On Ubuntu MATE and many other distros, however, you must do this
via the command line.

Simply run...

..  code-block:: bash

    wacommap

When prompted, select a display. ``LVDS`` is usually your laptop screen, while
``VGA`` or ``HDMI`` is an external monitor. Just enter the number for the
display you want to map to.

.. _wacom_using_rotate:

Rotate
-----------------------------------

If you want to use your tablet in any orientation other than its default, you
can rotate it via script.

Run one of the following commands in the terminal, depending on what you want...

..  code-block:: bash

    # Set to portrait mode.
    wacomrotate p

    # Set to left-hand landscape mode (opposite the default)
    wacomrotate lf

    # Set to upside-down portrait mode.
    wacomrotate pf

    # Set back to the default.
    wacomrotate l
