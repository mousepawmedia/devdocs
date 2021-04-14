..  _nextcloud:

Nextcloud
##########################

..  _nextcloud_login:

Login
==========================

Nextcloud is only accessible by MousePaw Media staff. To log in, navigate to
`<https://nextcloud.mousepawmedia.net>`_ and enter your company LDAP
credentials.

If you have difficulty logging in, contact the IT department.

..  _nextcloud_quota:

Nextcloud Quota
============================

To keep our file space usage under control, each user has been issued a
**quota**. Most users have a 1GB quota.

What Is a Quota?
-----------------------------

If you're not familiar with this concept, every file has a *file size*. A file's
size is a measurement of how much computer memory it takes up. File size is
usually measured in :abbr:`KB (kilobytes; 1024 bytes)`,
:abbr:`MB (megabytes; 1024 KB)`, and :abbr:`GB (gigabytes; 1024 MB)`.

Your quota is the total amount of computer memory you can use on the server.
For example, if you've uploaded ten 5 MB files, you've used a total of 50 MB
from your quota.

..  _nextcloud_quota_managing:

Managing Your Quota
--------------------------

You can view your quota and usage by clicking your username in the upper-right
corner and selecting :guilabel:`Personal`. The used and available quota is
displayed at the top.

Most documents, spreadsheets, presentation and text files are fairly small
files, unless they have a lot of media embedded in them.

Photos, audio, and video files are much larger (*especially* video), so you
should be mindful of their size when uploading. Higher resolution photos,
uncompressed audio, and high resolution video take up more space.

Some media files should be stored as high resolution, especially those being used
for production. Media used only for internal reference, on the other hand, can
usually be stored in a compressed format (such as .ogg instead of .wav) or
a lower resolution (such as 800 x 450 instead of 7680 x 4320).

..  _nextcloud_quota_raising:

Raising Your Quota
------------------------

If you need more space, you can ask an administrator to raise your quota.
Before you do, however, we recommend following these steps:

1)  Search through your files for anything you no longer need, and thus can
    remove.

2)  Ensure your media files are optimized for storage, depending on their
    purpose. (see :ref:`nextcloud_quota_managing`)

3)  Think carefully about how much extra space you need. Are you uploading
    something necessary? Can you change any habits to make better use of
    your quota space?

If you are certain you need more space, contact an administrator and request
that your quota be raised to a particular size (preferably in GB). Please
be sure to explain why you need the extra space.

..  _nextcloud_basic:

Basic Structure
===========================

Nextcloud offers a few different Apps, which you can switch between from the
top bar.

* :guilabel:`Files` allows you to browse through your files and folders.
  (See :ref:`nextcloud_files`).

* :guilabel:`Activity` lists all of the recent activity on Nextcloud.

* :guilabel:`Gallery` lets you view and browse all the photos on Nextcloud.

* :guilabel:`Circles` allows you to create custom groups for sharing.

* :guilabel:`Notes` is a place to quickly store notes for yourself.
  (Not collaborative.)

* :guilabel:`Announcements` shows the latest news.

* :guilabel:`Bookmarks` allows you to store internet bookmarks and
  access them from any device. (Not collaborative.)

* :guilabel:`Tasks` is your personal task list. (Not collaborative.)

..  note:: You should still be using Phabricator Maniphest for project tasks,
    so your teammates can see them. The Nextcloud Tasks app is only for personal
    work tasks, such as "Read 'Dreaming in Code'" or "Clean desk".

Clicking the gear on the right side of the top bar brings up another menu.
This gives you access to your profile (:guilabel:`Personal`), Nextcloud's
official documentation (:guilabel:`Help`), and the ability to sign out of your
account (:guilabel:`Log out`).

Most of these applications are self-explanatory. Thus, for brevity, we will
only be covering Files and Collabora Office here. If you need other help,
contact IT.

..  _nextcloud_files:

Files
============================

The Files application allows you to browse, arrange, add, and remove files
from Nextcloud. The interface is divided into three panes:

* The left-most pane allows you to filter your files by various criteria.
  Usually, you'll use :guilabel:`All files` when browsing and managing files.

* The center pane allows you to browse files and directories, and perform
  many file management tasks.

* The right pane appears when you select many options from the center pane.

..  _nextcloud_files_browsing:

Browsing Files
-------------------------------

To open a folder or file, click its *name* in the center pane.

At the top of the center pane is a "breadcrumb trail" which displays your
location in the folder structure. Clicking the Home icon on the
left end of the breadcrumb trail takes you to the base directory.

..  _nextcloud_files_uploading:

Adding and Uploading
--------------------------------

The :guilabel:`+` button towards the top of the center pane allows you to
create or upload a new item at the current location.

You can upload nearly any kind of file to Nextcloud.

* Image files (.jpg, .png, .gif, .svg, etc.) can be viewed directly via the
  Gallery app.

* Document files (``*.odt``, ``*.doc``, ``*.docx``) can be viewed and edited
  via the Office (Document) app.

* Spreadsheet files (``*.ods``, ``*.xls``, ``*.xlsx``) can be viewed and
  edited via the Office (Spreadsheet) app.

* Presentation files (``*.odp``, ``*.ppt``, ``*.pptx``) can be viewed and
  edited via the Office (Presentation) app.

* PDF files can be viewed directly via the built-in PDF viewer.

* Text files (``*.txt``) can be viewed and edited directly via the built-in
  Text editor.

* Many video files can be viewed directly via the built-in Video player.

..  important:: You should keep an eye on your file space quota.
    See :ref:`nextcloud_quota`.

..  _nextcloud_files_managing:

Managing Files
--------------------------------

On each file/folder's row on the center pane, you'll see the following
controls (moving from left to right)...

* The Star button marks a file as favorite.

* Clicking the item icon toggles its selection. Selecting multiple items
  allows you to perform various file actions on them in bulk.

* Click the item name opens it.

* The Share button opens the sharing tab in the right pane.
  (See :ref:`nextcloud_files_sharing`).

* The Menu button (the three dots) opens the actions menu.

  * :guilabel:`Details` shows information and history about the item in the
    right pane. (See :ref:`nextcloud_files_details`).

  * :guilabel:`Rename` allows you to rename the item.

  * :guilabel:`Download` downloads the current version of the item to your
    computer. If you download a folder, it will download all of the contents
    in a .zip file.

  * :guilabel:`Delete` deletes the file. (See :ref:`nextcloud_files_restoring`).

* The file size is displayed in KB, MB, or GB.

* The date the file was last modified is displayed last on the row.

..  _nextcloud_files_sharing:

Sharing Files
------------------------------

You can share any item with other users from the :guilabel:`Sharing` tab
in the right pane. If you don't see it, you can open it by tapping the
Sharing icon on an item row in the center pane.

..  note:: When sharing a lot of related files, it is usually better to
    put them all in one folder, and then share the folder itself.

To share an item, start typing a username (i.e. "jcmcdonald") or group name
(i.e. "Content Developers") in the :guilabel:`Share with users or groups...`
box. Tap the name when it appears in the autocomplete list.

..  note:: You *must* search users by their username. You presently cannot
    search by their real name.

After selecting a user or group, they will appear in the sharing list below
the search box. You can control sharing permissions from here.

* :guilabel:`Can share` means the user/group can share the item with other
  users/groups.

* :guilabel:`Can edit` means the user/group is able to edit the item.

* The trash can icon un-shares the item with the user/group.

..  important:: For Design+Production or Content Development, it is usually
    most convenient to place uploaded work into a folder and share it with
    your entire department, using the department's group name.

..  _nextcloud_files_details:

File Details and Versions
-----------------------------------

The right pane of te Files app allows you to control details about each folder
and file. If you don't see this pane, click the Menu button (the three dots)
on the item's row in the center pane, and select :guilabel:`Details`.

At the top, you can see the file name, size, and date modified. You can
set tags by clicking the :guilabel:`Tags` button. Type in the
:guilabel:`Collaborative tags` box to search existing tags. You can also type
a new tag name and press :kbd:`Enter` to create one.

The :guilabel:`Activities` tab shows the history of the file.

..  note:: Any action attributed to "remote user" is due to a bug in the
    Office app. You can view more revision information in that app.

The :guilabel:`Comments` section allows you to discuss the item with other
users.

The :guilabel:`Sharing` section allows you to share the file with other users
and groups (see :ref:`nextcloud_files_sharing`).

Lastly, :guilabel:`Versions` allows you to see and restore different versions
of an item! You can download or restore any version displayed on the list. If
you choose to restore an older version, you can still restore the newer version.

..  _nextcloud_files_restoring:

Restoring Files
------------------------------

If you accidentally delete a file, you can restore it. Select
:guilabel:`Deleted files` from the lower-left corner of the Files app.

In the list, find the item you want to restore, and click the
:guilabel:`Restore` button towards the right of the item's row.

..  _nextcloud_office:

Office
============================

Nextcloud has the latest version of Collabora Office built in. This allows us
to collaboratively edit documents right from the web browser.

We are specifically running :abbr:`CODE (Collabora Office Development Edition)`.
This is basically a special version of LibreOffice, designed and optimized for
web browsers. For performance reasons, many extraneous editing features have
been removed. However, it is capable of displaying documents the same as if you
opened them in LibreOffice on your computer.

..  _nextcloud_office_limitations:

Limitations
------------------------------

Missing Features
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For performance reasons, many of LibreOffice's editing features have been
omitted from Collabora Office.

While Collabora Office can display all OpenDocument format documents,
spreadsheets, and presentations (among other document types and formats) almost
identically to how LibreOffice displays them, it cannot necessary *create* or
*edit* the more complex formatting.

Here are a few common examples:

* Missing fonts may be substituted, without actually changing what font is
  called for in the document.

* Complex formatting on documents is retained and usually displayed, but
  it cannot be created or modified.

* Headers and footers are hidden on documents, although they still exist
  and are displayed in LibreOffice itself.

What this often means is that, while Collabora Office is fantastic for
on-the-fly and collaborative editing, you will still need to use LibreOffice
for formatting.

This is especially true of Presentations. The text and images can themselves
be edited on Collabora Office, but themes, appearances, and effects will need
to be edited on LibreOffice.

Performance
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Collabora Office is a full application being served over the internet.
Understandably, then, it needs a fairly good internet connection! You should
take this into consideration when using it.

If your internet connection is slow or unreliable, you may experience lags in
typing, applying changes, or seeing the collaborative changes of others. In the
worst case, you might not be able to make any changes because they're getting
lost between your computer and the server.

..  note:: If you *see* your changes on the document, you can rest assured
    that they are indeed there.

If you are experiencing such issues, you may need to wait on using Collabora
Office until you have a more reliable internet connection.

..  _nextcloud_office_opening:

Creating and Opening Files
------------------------------

Collabora fully integrates with the Files application. Clicking the name of any
office-format document (``*.odt``, ``*.docx``, ``*.ods``, ``*.odp``, etc.)
will open it in Collabora Office.

To create a new file, click the :guilabel:`+` button at the top center of the
Files app, and select :guilabel:`New Document`, :guilabel:`New Spreadsheet`,
or :guilabel:`New Presentation`.

..  _nextcloud_office_interface:

The Editing Interface
------------------------------

When you open a file for editing, you'll see the menubar and toolbar
along the top. You'll see different menus, buttons, and options depending
on what kind of file you're editing.

..  _nextcloud_office_interface_menus:

Menus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  _nextcloud_office_interface_menus_file:

File Menu
""""""""""""""""""""""""""""""""

* :guilabel:`Save` will publish your changes immediately to Nextcloud's
  copy of the file. Changes will sometimes autosave, but you should habitually
  save anyway.

* :guilabel:`Print` allows you to print the document from the web browser.

* :guilabel:`See Revision History` allows you to view and switch between
  different saved versions of the file. Click a version to preview it, and
  click the restore icon to switch to that version.

* :guilabel:`Download As...` allows you to download the file in PDF,
  OpenDocument format, or Microsoft Office format (old or new).

..  _nextcloud_office_interface_menus_edit:

Edit Menu
""""""""""""""""""""""""""""""""

* :guilabel:`Repair` shows a list of all changes made in this session. This is
  useful if you want to roll back a lot of changes at once.

* :guilabel:`Undo` and :guilabel:`Redo` let you roll back or restore changes
  one-by-one.

* :guilabel:`Cut`, :guilabel:`Copy`, and :guilabel:`Paste` work exactly as you
  would expect with the file itself. However, for technical reasons, it is
  only possible to cut/copy/paste plain, unformatted text between Collabora
  Office and other programs.

..  note:: If you use PrivacyBadger, you may need to disable it on this site
    before you can use Cut/Copy/Paste.

* :guilabel:`Select all` selects everything in the document.

* :guilabel:`Track Changes` allows you to...

  * :guilabel:`Record` changes made to the document.

  * :guilabel:`Show` changes tracked.

  * Navigate to the :guilabel:`Previous` or :guilabel:`Next` tracked change.

Tracked changes appear as comments on the right side of the document. Each can
be accepted or rejected there.

..  _nextcloud_office_interface_menus_view:

View Menu
""""""""""""""""""""""""""""""""

* :guilabel:`Full screen` toggles the full screen mode for Collabora Office,
  which is fantastic for distraction free editing. You can also tap :kbd:`Esc`
  to exit full screen mode.

* :guilabel:`Zoom in`, :guilabel:`Zoom out`, and :guilabel:`Reset zoom` let you
  control the zoom on the document. You can also use the zoom controls on the
  lower-right corner of the interface.

..  note:: Your web browser's default zoom controls will only scale the
    interface, not the document.

* :guilabel:`Formatting Marks` toggles the visibility of various otherwise
  invisible formatting marks, like space and paragraph break markers.

..  _nextcloud_office_interface_menus_insert:

Insert Menu
""""""""""""""""""""""""""""""""

* :guilabel:`Image` embeds an image from your computer in the document.
  Be aware that this will increase the document's file size.

* :guilabel:`Comment` adds a comment at the current cursor position or
  selection. Comments appear on the right side of documents, or floating by
  cells in spreadsheets. They are useful for editing discussions and for
  marking suggested revisions.

* :guilabel:`Footnote` adds a new footnote to the document, with the reference
  inserted at the current cursor position. Footnotes appear at the bottom of
  each page.

* :guilabel:`Endnote` adds a new endnote to the document, with the reference
  inserted at the current cursor position. Endnotes appear at the end of the
  document.

* :guilabel:`Page break` inserts a new page break, while
  :guilabel:`Column break` inserts a new column break. (Obviously.)

* :guilabel:`Row` and :guilabel:`Column` insert a new row or column
  before the current position in the spreadsheet.

* :guilabel:`Special character...` opens a panel which allows you to browse
  for and insert special characters.

* :guilabel:`Formatting mark` allows you to insert one of several special
  formatting marks.

..  _nextcloud_office_interface_menus_format:

Format Menu
""""""""""""""""""""""""""""""""

* The :guilabel:`Text` menu allows you to set various text properties.
  A decent subset of the most common options are provided in Collabora Office.

* :guilabel:`Text orientation` is mainly for controlling the direction of
  text flow (left-to-right, or right-to-left). This is usually only useful
  if you're working in another language.

* :guilabel:`Spacing` offers a subset of paragraph properties. Line spacing,
  paragraph spacing, and indentations are controlled from here.

* :guilabel:`Align` controls text alignment. In addition to horizontal alignment
  (left, center, right, justified), you can control vertical alignment within
  table cells.

* :guilabel:`Lists` supplies controls and options for bulleted and numbered
  lists, specifically indentation and levels. (Bullet and numbering formatting
  is not directly editable.)

* :guilabel:`Clear direct formatting` resets the selection to the default
  formatting.

* :guilabel:`Page` allows you to change the page size.

..  _nextcloud_office_interface_menus_tables:

Tables Menu
""""""""""""""""""""""""""""""""

You can use this menu to modify and delete *existing* tables in documents.
New tables are created from the Table button on the toolbar.

* :guilabel:`Insert` lets you add rows and columns to a table.

* :guilabel:`Delete` lets you delete rows, columns, and entire tables.

* :guilabel:`Select` aids in selecting portions (or the entirety) of a table.

* :guilabel:`Merge cells` merges the selected cells. Unfortunately, no option
  is provided for splitting cells, so you will need to delete merged cells and
  create new rows/columns instead.

..  _nextcloud_office_interface_menus_cells:

Cells Menu
""""""""""""""""""""""""""""""""

When editing spreadsheets, this menu provides controls for managing cells.

* :guilabel:`Insert row` and :guilabel:`Insert column` insert a new row or
  column *before* the current position.

* :guilabel:`Delete row` and :guilabel:`Delete column` delete the current
  row or column.

..  _nextcloud_office_interface_menus_slide:

Slide Menu
""""""""""""""""""""""""""""""""

When editing presentations, this menu provides controls for managing slides.

* :guilabel:`New slide` creates a new slide after the current slide.

* :guilabel:`Duplicate slide` creates a new copy of the current slide.

* :guilabel:`Delete slide` deletes the current slide.

* :guilabel:`Fullscreen presentation` previews the current presentation.
  You can navigate it using the arrow keys, and close out of the preview by
  pressing :kbd:`Esc`.

..  _nextcloud_office_interface_menus_help:

Help Menu
""""""""""""""""""""""""""""""""

* :guilabel:`Keyboard shortcuts` displays a master list of keyboard shortcuts
  for Collabora Office. Some features are hidden behind keyboard shortcuts,
  so it is worth reviewing this. :kbd:`Esc` closes the list.

* :guilabel:`About` displays the current version of LibreOffice Online that
  this version of Collabora Office is based on.

..  _nextcloud_office_interface_toolbar:

Toolbar
^^^^^^^^^^^^^^^^^^^^^^^^^^

Different buttons appear on the toolbar, depending on what kind of file you're
editing. The button's name will appear when you hover over the button.

* **Save** saves the current document.
  (See :ref:`nextcloud_office_interface_menus_file`).

* **Undo** rolls back the last change.
  (See :ref:`nextcloud_office_interface_menus_edit`).

* **Redo** restores the last rolled back change.
  (See :ref:`nextcloud_office_interface_menus_edit`).

* **Document repair** brings up the document repair window.
  (See :ref:`nextcloud_office_interface_menus_edit`).

* The **Style Menu** allows you to select a style for your current document
  selection.

* The **Slide Menu** allows you to select a slide type for your current slide.

* The **Font Menu** offers many common open-source fonts.

* The **Size Menu** allows you to select a text size.

* **Bold**, **Italic**, **Underline**, and **Strikethrough** are shortcuts
  for the four most common text formatting options.
  (See :ref:`nextcloud_office_interface_menus_format`).

* **Font color** and **Highlighting** set the foreground and background
  colors for your text.

* **Align left**, **Center horizontally**, **Align right**, and **Justified**
  set the text's alignment.
  (See :ref:`nextcloud_office_interface_menus_format`).

* **Wrap text** wraps the text within the cell.

* **Format as Currency**, **...as Percent**, **...as Number**, or **...as Date**
  will format the numbers in the current cell as indicated.

* **Add Decimal Place** and **Delete Decimal Place** allow you to control
  the number of decimal places displayed in a number in the current cell.

* **Sort Ascending** and **Sort Descending** will sort the selected cells.

* **Bullets on/off** and **Numbering on/off** toggle the two kinds of lists
  available to you.
  (See :ref:`nextcloud_office_interface_menus_format`).

* **Increase Indent** and **Decrease Indent** let you control the indentation
  of a paragraph (not just its first line).
  (See :ref:`nextcloud_office_interface_menus_format`).

* **Insert Table** inserts a new table.

* **Insert comment** inserts a comment at the current cursor position, cell, or
  selection.
  (See :ref:`nextcloud_office_interface_menus_insert`).

* **Insert graphic** inserts an image from your computer.
  (See :ref:`nextcloud_office_interface_menus_insert`).

* **Special Character** inserts a special character, which you select from
  a panel. (See :ref:`nextcloud_office_interface_menus_insert`).

..  _nextcloud_office_interface_statusbar:

Status Bar
^^^^^^^^^^^^^^^^^^^^^^^^^^

A few more tools and controls are available on the status bar at the bottom
of the interface.

* You can search within the document using the :guilabel:`Search` box and the
  corresponding Previous and Next buttons.

* On Documents, the current word and character count, as well as the current
  page number. are displayed.

* On Spreadsheets, the current sheet number and count are displayed.

* On Spreadsheets, the number of selected rows and columns is displayed.

* Insert and Overwrite typing modes can be toggled with your computer's
  :kbd:`Insert` key.

* On Spreadsheets, you can perform quick math on the selected cells by tapping
  the down arrow near the center of the status bar.

* When the document does NOT have unsaved changes, **Document Saved** will
  appear on the status bar.

* The user list displays the number of users currently viewing the document.
  Click this list to expand it, and then click a username to jump to that
  user's position in the document.

* You can move between pages using the Previous Page and Next Page buttons.

* If available, Zoom controls appear on the right-most side of the status bar.

..  _nextcloud_office_interface_spreadsheet:

Spreadsheet Controls
^^^^^^^^^^^^^^^^^^^^^^^^^^

When editing Spreadsheets, a few additional controls are available.

The second upper toolbar is where you type cell contents. The Sum and Function
buttons can help with creating the two most common kinds of formulas.

..  note:: To my knowledge, all LibreOffice spreadsheet formulas and functions
    work, but you will have to manually type most of them.

The second to bottom toolbar visible when editing Spreadsheets allows you to
navigate and control sheets. Use the arrows or tabs to move between sheets.

Right-click on a sheet tab to add, rename, or delete sheets.

..  _nextcloud_office_interface_presentation:

Presentation Controls
^^^^^^^^^^^^^^^^^^^^^^^^^^

When editing Presentations, a list of the current slides is displayed on the
left side. You can navigate by clicking on a slide.

The Insert, Duplicate, and Delete Slide buttons are available at the bottom of
this pane, in addition to the Fullscreen presentation button.
(See :ref:`nextcloud_office_interface_menus_slide`).

..  _nextcloud_etherpad:

Etherpad
===========================

Nextcloud also is integrated with Etherpad (see :ref:`etherpad_usage`), a
collaborative text editor.

From the Files application, you can create a new Etherpad by clicking the
:guilabel:`+` icon at the top center and selecting :guilabel:`Pad`.

The interface for Etherpad is the same whether accessed through Nextcloud
or directly at `pad.mousepawmedia.net`. See :ref:`etherpad_usage` for usage
instructions.

..  _nextcloud_client:

Installing Nextcloud Client
============================

You may want to install the Nextcloud client, which allows you to sync your
files to and from your computer or device.

..  _nextcloud_client_install:

Installing
----------------------------

To install the Nextcloud client on an Debian-based system, including Ubuntu,
run...

..  code-block:: bash

    $ sudo add-apt-repository ppa:nextcloud-devs/client
    $ sudo apt update
    $ sudo apt install nextcloud-client

To install the Nextcloud client on Windows (7-10) or macOS (10.10+)
`download the installer <https://nextcloud.com/install/#install-clients>`_
from the official website. Run the installer like you usually would.

If you're running another version of Linux, native packages are available for
all major distributions.
`The complete list is available here <https://help.nextcloud.com/t/linux-packages-status/10216>`_.
Alternatively, you can
`download the AppImage <https://nextcloud.com/install/#install-clients>`_
from the official website.

..  _nextcloud_client_setup:

Client Setup
----------------------------

Once that installs, start the :guilabel:`Nextcloud desktop sync client`
application. The setup wizard will appear the first time.

Set the server address to :code:`https://nextcloud.mousepawmedia.net` and
click :guilabel:`Next`. Enter your company login (LDAP) credentials and
click :guilabel:`Next` again.

On the next screen, we'll choose to select specific folders to sync. Click
:guilabel:`Choose what to sync` and UNCHECK :guilabel:`MLP`. (That's a large
collection of videos you can just watch on Nextcloud online.) The other folders
should be fine to sync, so leave the rest checked. Click :guilabel:`OK`.

The default sync folder is recommended, but you can change it if you like.

Finally, click :guilabel:`Connect...`, and then :guilabel:`Finish`.
If you're on Ubuntu, you'll now see the Nextcloud icon on your top bar. It will
alert you when changes are made to files, and will automatically sync to the
server.

You can now interact directly with files on the Nextcloud server by adding,
editing, and removing them within your :file:`Nextcloud` directory in your
home folder!

Next, let's ensure the Nextcloud client starts when our computer does.
Right-click the Nextcloud icon in your indicator area, and click
:guilabel:`Settings...`. Select the :guilabel:`General` tab and ensure
that :guilabel:`Launch on System Startup` is checked.

Feel free to tweak the other settings to your preference, and then click
:guilabel:`Close` when you're done.
