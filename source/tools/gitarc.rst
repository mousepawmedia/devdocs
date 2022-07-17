..  _gitarc:

Git and Arcanist
###############################

All of our code is stored on our own private Git repositories. For those who
don't know, Git is a :abbr:`VCS (version control software)`, which means it
keeps track of every change that is made. Using Git enables us to collaborate
on the code and merge all of our changes together cleanly.

..  note:: There is a difference between Git and GitHub. GitHub is an online
    service that offers free and paid Git repositories. We have "mirrors" of our
    code on GitHub, meaning the code is copied directly over from our own
    repositories, but we do not use GitHub itself for development work.

Phabricator manages all our Git repositories, and that gives us a lot of
additional features - Differential being key among those. In order to upload
directly to Phabricator, we use a program called Arcanist.

.. _gitarc_install:

Installing Git and Arcanist
=============================

Installing on Ubuntu
------------------------------

On Ubuntu, you can simply install Arcanist like any other package. We'll
install it along with its dependencies and related tools.

..  code-block:: bash

    $ sudo apt install git php php-{curl,cli,xml} cppcheck arcanist clang-format arcanist-clang-format-linter

Then, we just test it out.

..  code-block:: bash

    $ arc help

If the help list shows up, that's it! You're ready to go.

..  warning:: If you run into any errors with this, run `sudo apt remove arcanist arcanist-clang-format-linter`
    and install according to "Installing on Linux, Alternate" below. 

Installing on Linux, Alternate
--------------------------------

..  warning:: If the above worked, do NOT follow this version of the
    instructions. Just skip to next section, ``Configuring Git``.

If you're on a Debian-based Linux OS, you can follow these instructions to
install Arcanist. If you're on another Linux OS, these instructions should
still work, although you may need to adjust commands a little to make it work.

Arcanist requires PHP 7; as of July 2022, it doesn't work with PHP 8.
If you're on Ubuntu 22.04 or later, you will need to run `sudo add-apt-repository ppa:ondrej/php -y`
so you can install PHP 7.4 explicitly.

First, we need to install our dependencies.

..  code-block:: bash

    $ sudo apt install git php7.4 php7.4-{curl,cli,xml} cppcheck clang-format

Enter your password if prompted. It will want to install a few other packages.
Tell it "yes" (`Y`) and let it run. It might take a little while.

Still in your terminal, create the folder for Arcanist to live in, and then pull
in Arcanist.

..  code-block:: bash

    $ mkdir ~/.arcanist
    $ cd ~/.arcanist
    $ git clone https://github.com/phacility/arcanist.git

Now we need to tell our system where to look for Arcanist. The easiest way
to do this is to add Arcanist's :file:`/bin` directory to our environment
path.

..  code-block:: bash

    $ sudo pluma /etc/environment

Just before the last ", insert the following, replacing `USERNAME` with your
Ubuntu username.::

    :/home/USERNAME/.arcanist/arcanist/bin/

Note the colon at the beginning, which separates each entry. Your new path will
probably look something like this::

    PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/home/USERNAME/.arcanist/arcanist/bin/"

Save and close pluma. Then, restart your computer. To test it out, run...

..  code-block:: bash

    $ arc help

..  warning:: If you installed it wrong, you'll see a suggestion to install the
    package "arc". DON'T DO IT! That is a completely different program.

Arcanist may ask you to install additional PHP packages via apt. If you set
everything up right, you'll see arc's help list.

We use the clang-format-linter to configure our C++ code. 
For non-Debian-based Linux systems, you will need to follow the steps below:

Locate where Arcanist is installed on your system via :code:`whereis arcanist`. 
This will provide you with a path like `/usr/share/arcanist` or `/home/user/.arcanist/arcanist/bin/arc`. 
Navigate to the folder containing `arcanist` (e.g. `/usr/share` or `/home/user/.arcanist`) and
install `arcanist-clang-format-linter` like this:

..  code-block:: bash

    cd /usr/share   # or whatever path given by 'whereis arc'
    sudo git clone https://github.com/pwithnall/morefas-phabricator.git clang-format-linter

Installing on Mac
-----------------------------------

On Mac, Git is automatically installed as part of
``Command Line Tools for Xcode``. If you prefer an alternative means of
installation, see `Git: Getting Started Installing Git <https://git-scm.com/book/en/v2/Getting-Started-Installing-Git>`_

You can install Arcanist following these instructions:
`Arcanist User Guide: Mac OS X <https://secure.phabricator.com/book/phabricator/article/arcanist_mac_os_x/>`_

We use the clang-format-linter to configure our C++ code. 
For macOS systems, you will need to follow the steps below:

Install clang-format first using homebrew with the following code:

..  code-block:: bash
    brew install clang-format

Locate where Arcanist is installed on your system via :code:`whereis arcanist`. 
This will provide you with a path like `/usr/share/arcanist`. 
Navigate to the folder containing Arcanist (e.g. `/usr/share`) and install `arcanist-clang-format-linter` like this:

..  code-block:: bash

    cd /usr/share   # or whatever path given by 'where arcanist'
    sudo git clone https://github.com/pwithnall/morefas-phabricator.git clang-format-linter

Installing on Windows
-----------------------------------

On Windows, you can use the Windows Subsystem for Linux, or MinGW, and follow
the instructions for Linux.

Otherwise, to install directly on Windows, follow these instructions:
* `Git: Getting Started Installing Git <https://git-scm.com/book/en/v2/Getting-Started-Installing-Git>`_
* `Arcanist User Guide: Windows <https://secure.phabricator.com/book/phabricator/article/arcanist_windows/>`_
* `Clang-format-linter guide <https://llvm.org/builds/>`_

To install clang-format-linter follow the directions below:

Locate where Arcanist is installed on your system via:

..  code-block:: cmd
    
    cd\ 
    dir <arcanist> /AD /s

This will provide you with a path like `C:\Program Files (x86)`. 
Navigate to the folder containing Arcanist (e.g. `C:\Program Files (x86)`) and install `arcanist-clang-format-linter` like this:

..  code-block:: cmd

    cd c:\Program Files (x86)` # or whatever path given by 'dir <arcanist> /AD /s'
    git clone https://github.com/pwithnall/morefas-phabricator.git clang-format-linter

.. _gitarc_gitconfig:

Configuring Git
===============================

Register yourself with Git, so you'll be credited for commits.

..  note:: If you only want the given name and/or email for the current
    repository, omit the ``--global`` flag.

Run the following commands, **substituting in your own information.**
Staff should use their company email here; otherwise use the email
associated with your GitHub account.

..  code-block:: bash

    $ git config --global user.name "Sam Smith"
    $ git config --global user.email sam@example.com

.. _gitarc_importrepos:

Import Repositories
===============================

All of our projects are preconfigured to work on Linux and Mac systems.
You only need to pull them down using Git.

Decide on a place to put your repositories. I recommend creating a
:file:`repos` directory in your Home folder. Wherever you want your
repositories, create and navigate to the directory in question.
On Linux or Mac, run...

..  code-block:: bash

    $ mkdir ~/repos
    $ cd ~/repos

Now we need to check out a repository. You have two options for this...

1) Use an SSH key (recommended).

2) Use your Phabricator username and a VCS password.

Using an SSH Key
-------------------------------

We need a public and private SSH key pair for this method. If you already
have one set up, find your public key, which probably has a file path like
:file:`~/.ssh/{id_rsa}.pub`. If you have an SSH key already, skip to
`Adding Your Public Key`.

Creating a Keypair (Linux/Mac)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you need to create a new SSH key pair, we can do that now. We start by
installing OpenSSH Client, so we can log into other systems with SSH.

On Ubuntu, you can install OpenSSH via...

..  code-block:: bash

    $ sudo apt install openssh-client

If you're on Mac, the necessary software is already installed by default.

Next, we generate a new keypair. Be sure to enter your GitHub or company
email address in place of `me@example.com`.

..  code-block:: bash

    $ ssh-keygen -t rsa -b 4096 -C 'me@example.com'

You'll see a message that the key pair is being generated. When prompted, press
:kbd:`Enter` to use the default path for the key.

Finally, enter a password used for unlocking the SSH key. You'll find the
new keypair at :file:`~/.ssh/{id_rsa}.pub`.

..  warning:: Your public key is the part you share. **NEVER EVER EVER share
    your private key** (the part ending in `.rsa`)!

Creating a Keypair (Windows)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you're using Windows, you can create an SSH keypair using PuTTYGen or
Git Bash. Two methods are described below:

* `Using PuTTYGen on Windows <https://www.ssh.com/ssh/putty/windows/puttygen>`_
* `Generating a new SSH Key (Git Bash) <https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#platform-windows>`_

Adding Your Public Key
^^^^^^^^^^^^^^^^^^^^^^^^^

Open your public key in a plain text editor, or just output it in your terminal.
On Linux or Mac, you can do this via...

..  code-block:: bash

    $ cat ~/.ssh/id_rsa.pub

On Phabricator, click your profile picture towards the right of the top menubar,
and click :guilabel:`Settings`. From the menu pane on the left,
select :guilabel:`SSH Public Keys`.

Next, click :guilabel:`SSH Key Actions` (upper-right) and
:guilabel:`Upload Public Key`.

Paste your public key under :guilabel:`Public Key`. Then, enter a
:guilabel:`Name` for the key. The name only needs to be something helpful
for you to remember what key this is.

Finally, click :guilabel:`Upload Public Key`.

Using a VCS Password
---------------------------

This method is a bit easier to set up, but considerably more annoying to use.
During a typical `git pull` or `git push`, you may have to enter the VCS
password as many as *three times*!

On Phabricator, click your profile picture towards the right of the top menubar,
and click :guilabel:`Settings`. From the menu pane on the left,
select :guilabel:`VCS Password`.

Enter a new VCS password under :guilabel:`New VCS Password` and confirm it
under :guilabel:`Confirm VCS Password`. Then, click :guilabel:`Change Password`.

Cloning the Repository
---------------------------

On Phabricator, navigate to the `Diffusion` app, and select for the repository
you want to check out. On the repository page, look in the :guilabel:`Details`
box for the `Clone` commands. If you're using the VCS password, select the
`https://` URL. Otherwise, if you're using the SSH key, select the `ssh://`
URL.

In your terminal, make sure you're in the directory where you want to
check out your repositories. On Linux or Mac, run...

..  code-block:: bash

    $ cd ~/repos

Then, clone the repository with the :samp:`git clone {URL}` command.
For example, to check out the PawLIB repository with SSH, use...

..  code-block:: bash

    $ git clone ssh://git@phab.mousepawmedia.com:2222/diffusion/P/pawlib-git.git

Switching Between SSH and VCS
----------------------------------

If you've cloned a repository using one protocol, and decide you need to use
the other, you can switch them out fairly easily without having to clone
the repository all over again.

In the root of the repository you want to switch out, edit the file
:file`.git/config`.

Look for the `url =` section. Swap that URL out for the one that matches
the method you want to move. (Again, you can find those URLs on the Phabricator
Diffusion page for the repository in question.)

After switching out the URLs, save and close the file. That's it!

.. _gitarc_signin:

Sign In With Arcanist
=================================

In your terminal, navigate into the root of one of the repositories you
checked out, using :samp:`cd {reponame}`. Once in your project directory, run
the following...

..  code-block:: bash

    $ arc install-certificate

Go to `this link <https://phab.mousepawmedia.com/conduit/login/>`_ and sign
in if necessary. You'll receive a validation code. Copy and paste
(:kbd:`Ctrl+Shift+V`) the validation code into the terminal when prompted.

If you did all this correctly, you will see the message "API Token installed".

------------------------

**That's it!** Repeat the `Cloning the Repository`_ section as needed to
import each repository you need.
