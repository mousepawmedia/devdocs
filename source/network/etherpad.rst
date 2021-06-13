..  _etherpad:

Etherpad
##########################

Etherpad is a collaborative text editor with a number of useful
features.

Security Considerations
===========================

Etherpad is not behind any sort of formal security - anyone with the pad link
can edit the pad. However, a reasonable level of security can be achieved via
Nextcloud's integration.

When the pad is created through Nextcloud, the internal pad name is a long,
random string of letters and numbers, which achieves the same effective security
as a password. Meanwhile, the pad is represented as a custom-named file in
Nextcloud, which can then be shared with other collaborators through Nextcloud's
interface. (See :ref:`nextcloud_etherpad`).

..  _etherpad_opening:

Creating and Opening Pads
==============================

To create a new Etherpad (outside of Nextcloud), or to open an existing pad,
go to ``pad.mousepawmedia.com``. Enter the name of pad you wish to create
or open, and click :guilabel:`OK`.

..  _etherpad_deleting:

Deleting Pads
==========================

Pads can only be deleted by an administrator. Contact an admin, and request
that the pad be deleted. Be sure to specify the pad's name, and whether it was
created through Nextcloud.

..  _etherpad_usage:

Using Etherpad
==========================

Etherpad is geared towards plain-text editing, and as such, it has a very
simple interface.

..  _etherpad_usage_toolbar:

Toolbar
-------------------------

Let's explore the toolbar, from left to right. Most of these have keyboard
shortcuts. However over a button in the interface to see the name and keyboard
shortcut.

* :guilabel:`Bold`, :guilabel:`Italic`, :guilabel:`Underline`, and
  :guilabel:`Strikethrough` change the selected text.

* :guilabel:`Ordered List` starts an automatic numbered list, or converts
  each line of a selection into an item in a numbered list.

* :guilabel:`Unordered List` creates a bulleted list, or converts each line
  of a selection into an item in a numbered list.

* :guilabel:`Indent` and :guilabel:`Outdent` increase and decrease the
  indentation of the current line or selection. If part of a list, it will
  also adjust the bullet or numbering accordingly, for nested lists.

* :guilabel:`Undo` and :guilabel:`Redo` can be used to undo and redo one's
  own commands. *It does not affect other user's actions.*

* :guilabel:`Clear Authorship Colours` removes the authorship colors from the
  entire document. This change is visible to all authors. It will *not* shut
  off all future such highlighting.

Moving to the right side of the toolbar...

* :guilabel:`Print` allows you to print the pad.

* :guilabel:`Import/Export from/to different file formats` allows you to
  import or export from an Etherpad file, an HTML document, or a Plain text
  (``.txt``) file.

* :guilabel:`Timeslider` switches to the Timeslider view, which allows you to
  view the complete history of changes to the pad.
  (See :ref:`etherpad_usage_timeslider`).

* :guilabel:`Save Revision` marks the current version as a revision in the
  timeslider, making it easier to find later.

* :guilabel:`Settings` toggles the settings pane.
  (See :ref:`etherpad_usage_settings`).

* :guilabel:`Share and Embed this pad` displays the direct link to the pad,
  and the URL you can use to embed the pad. If you check :guilabel:`Read Only`,
  the link and embed will show a read-only version of the pad, to prevent
  changes. Anyone with the main name/link for the pad can still edit it.

* :guilabel:`Show the users on this pad` show all of the users currently
  editing the pad. You can also set your name and color here, which you should
  be in habit of doing as a courtesy!

..  _etherpad_usage_settings:

Settings
-------------------------

There are a number of settings you can change on the pad. These can be
modified from the :guilabel:`Settings` button on the toolbar.

* :guilabel:`Chat always on screen` switches between the collapsible chat
  box ("off") or ever-present chat panel ("on").

* :guilabel:`Show Chat and Users` shows the chat panel and the user list at
  all times.

* :guilabel:`Authorship colours` toggles the authorship highlighting. If this
  is on, text written by an author will be highlighted in their selected color.

* :guilabel:`Line numbers` toggles the line numbers on the left side of the
  editing pane.

* :guilabel:`Read content from right to left?` is generally only needed if you
  are writing in a right-to-left language.

* :guilabel:`Wrap lines` automatically wraps extra long lines, instead of
  showing a horizontal scrollbar.

* :guilabel:`enable Autocompletion` toggles the autocomplete feature. When this
  is on, a popup box will appear as you type, suggesting words that appear
  elsewhere in the document. Browse the options with the up and down arrow
  keys and press :kbd:`ENTER` to make your selection.

* :guilabel:`Font type` sets the font for yourself only. This list includes
  common font faces, monospace, and even OpenDyslexic.

* :guilabel:`Language` allows you to select the language of the interface.
  It is "British English" by default, but you can select "English" just below
  that option to switch to the American variation.

..  note:: If you ever make a mistake and select the wrong language, just know
    that the Language menu is always the last item on the Settings screen.

.. _etherpad_usage_timeslider:

Timeslider
---------------------------

The Timeslider allows you to view the complete history of the pad.

At the top of the Timeslider interface, as expected, is a slider. You can
grab the black bar marker and drag it to anywhere in the slider. Alternatively,
you can click anywhere on the slider to jump.

Stars mark major revisions, which are created using the :guilabel:`Save Revision`
button on the editing interface.

Pressing the :guilabel:`Play/Pause` button will animate the history from the
position you are at. If you're at the end of the timeslider, it will start
playback from the beginning. The :guilabel:`Previous` and :guilabel:`Next`
buttons let you step between versions.

Each version shows its number, date, and authors. At the far right are three
buttons:

* :guilabel:`Export` lets you export the current version.

* :guilabel:`Settings` lets you change settings, specifically the font.

* :guilabel:`Return to pad` takes you back to the editing interface.
