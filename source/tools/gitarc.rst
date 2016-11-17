Git and Arcanist
###############################

All of our code is stored on our own private Git repositories. For those who
don't know, Git is a :abbr:`VCS (version control software)`, which means it
keeps track of every change that is made. Using Git enables us to collaborate
on the code and merge all of our changes together cleanly.

..  NOTE:: There is a difference between Git and GitHub. GitHub is an online
    service that offers free and paid Git repositories. We have "mirrors" of our
    code on GitHub, meaning the code is copied directly over from our own
    repositories, but we do not use GitHub itself for development work.

Phabricator manages all our Git repositories, and that gives us a lot of
additional features - Differential being key among those. In order to upload
directly to Phabricator, we use a program called Arcanist.

Installing Git and Arcanist
=============================

If you're on a Debian-based Linux OS (such as Ubuntu), you can follow these
instructions to install Arcanist. If you're on another Linux OS, these
instructions should still work, although you may need to adjust commands
a little to make it work.

First, we need to install our dependencies.

..  code-block:: bash

    $ sudo apt install git php php-curl php-cli php-xml cppcheck

Enter your password if prompted. It will want to install a few other packages.
Tell it "yes" (`Y`) and let it run. It might take a little while.

Still in your terminal, create the folder for Arcanist to live in, and then pull
in Arcanist and its support library, Libphutil.

..  code-block:: bash

    $ mkdir ~/.arcanist
    $ cd ~/.arcanist
    $ git clone https://github.com/phacility/libphutil.git
    $ git clone https://github.com/phacility/arcanist.git

Now we need to tell our system where to look for Arcanist. The easiest way
to do this is to add Arcanist's :file:`/bin` directory to our environment
path.

..  code-block:: bash

    $ sudo gedit /etc/environment

Just before the last ", insert the following, replacing `USERNAME` with your
Ubuntu username.::

    :/home/USERNAME/.arcanist/arcanist/bin/

Note the colon at the beginning, which separates each entry. Your new path will
probably look something like this::

    PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/home/USERNAME/.arcanist/arcanist/bin/"

Save and close gedit. Then, restart your computer. To test it out, run...

..  code-block:: bash

    $ arc help

..  WARNING:: If you installed it wrong, you'll see a suggestion to install the
    package "arc". DON'T DO IT! That is a completely different program.

Arcanist may ask you to install additional PHP packages via apt. If you set
everything up right, you'll see arc's help list.

Import Repositories
===============================

All of our projects are preconfigured to work on an Ubuntu system. You only
need to pull them down using Git.

Decide on a place to put your repositories. I recommend creating a
:file:`repos` directory in your Home folder. Wherever you want your
repositories, create and navigate to the directory in question.

..  code-block:: bash

    $ mkdir ~/repos
    $ cd ~/repos

Now we need to check out a repository. You have two options for this...

1) Use an SSH key (recommended).

2) Use your Phabricator username and a VCS password.

Using an SSH Key
----------------------------

We need a public and private SSH key pair for this method. If you already
have one set up, find your public key, which probably has a file path like
:file:`~/.ssh/{id_rsa}.pub`. If you have an SSH key already, skip to
`Adding Your Public Key`.

Creating a Keypair
^^^^^^^^^^^^^^^^^^^^^^^^

If you need to create a new SSH key pair, we can do that now. We start by
installing OpenSSH Client, so we can log into other systems with SSH.

..  code-block:: bash

    sudo apt install openssh-client

Next, we generate a new keypair. Be sure to enter your GitHub or company
email address in place of `me@example.com`.

    $ ssh-keygen -t rsa -b 4096 -C "me@example.com"

You'll see a message that the key pair is being generated. When prompted, press
:kbd:`Enter` to use the default path for the key.

Finally, enter a password used for unlocking the SSH key. You'll find the
new keypair at :file:`~/.ssh/{id_rsa}.pub`.

..  WARNING:: Your public key is the part you share. **NEVER EVER EVER share
    your private key** (the part ending in `.rsa`)!

Adding Your Public Key
^^^^^^^^^^^^^^^^^^^^^^^^^

Open your public key in a plain text editor like Gedit.

..  code-block:: bash

    $ gedit `~/.ssh/id_rsa.pub

Copy the entire contents of the file. Be sure to **close without modifying
the file!**

On Phabricator, click the Wrench icon in the upper right corner, and click
:guilabel:`Personal Account Settings`. From the menu pane on the left,
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

On Phabricator, click the Wrench icon in the upper right corner, and click
:guilabel:`Personal Account Settings`. From the menu pane on the left,
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
check out your repositories...

..  code-block:: bash

    $ cd ~/repos

Then, clone the repository with the :samp:`git clone {URL}` command.
For example, to check out the PawLIB repository with SSH, use...

..  code-block:: bash

    $ git clone ssh://git@phabricator.mousepawmedia.net:2222/diffusion/P/pawlib-git.git

Switching Between SSH and VCS
----------------------------------

If you've cloned a repository using one protocol, and decide you need to use
the other, you can switch them out fairly easily without having to clone
the repository all over again.

Navigate to the root of the repository you want to switch out, and run...

..  code-block:: bash

    $ gedit .git/config

Look for the `url =` section. Swap that URL out for the one that matches
the method you want to move. (Again, you can find those URLs on the Phabricator
Diffusion page for the repository in question.)

After switching out the URLs, save and close the file. That's it!

Sign In With Arcanist
=================================

In your terminal, navigate into the root of one of the repositories you
checked out, using :samp:`cd {reponame}`. Once in your project directory, run
the following...

..  code-block:: bash

    $ arc install-certificate

Go to `this link <https://hawksnest.ddns.net:8446/conduit/login/>` and sign
in if necessary. You'll receive a validation code. Copy and paste
(:kbd:`Ctrl+Shift+V`) the validation code into the terminal when prompted.

------------------------

**That's it!** Repeat the `Cloning the Repository`_ section as needed to
import each repository you need.
