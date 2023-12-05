..  _git:

Git
###############################

All of our code is stored on our own private Git repositories. For those who
don't know, Git is a :abbr:`VCS (version control software)`, which means it
keeps track of every change that is made. Using Git enables us to collaborate
on the code and merge all of our changes together cleanly.

..  note:: There is a difference between Git, GitLab, and GitHub.
    GitHub and GitLab are online services that offer free and paid Git
    repositories. We run our own private instance of GitLab. We also have
    "mirrors" of our code on GitHub, meaning the code is copied directly
    over from our own repositories, but we do not use GitHub itself for
    development work.

Our private GitLab instance manages all our Git repositories, and provides a
number of additional tools for code review, internal documentation, building,
and deployment.

.. _git_install:

Installing Git
=============================

Installing on Ubuntu
------------------------------

On Ubuntu, you can simply install Git like any other package. We'll
install it along with its dependencies and related tools.

..  code-block:: bash

    sudo apt install git

Installing on Mac
-----------------------------------

On Mac, Git is automatically installed as part of
``Command Line Tools for Xcode``. If you prefer an alternative means of
installation, see `Git: Getting Started Installing Git <https://git-scm.com/book/en/v2/Getting-Started-Installing-Git>`_

Installing on Windows
-----------------------------------

On Windows, you can use the Windows Subsystem for Linux and follow
the instructions for Linux.

Otherwise, to install directly on Windows, follow the instructions at
`Git: Getting Started Installing Git <https://git-scm.com/book/en/v2/Getting-Started-Installing-Git>`_

.. _git_config:

Configuring Git
===============================

Register yourself with Git, so you'll be credited for commits.

..  note:: If you only want the given name and/or email for the current
    repository, omit the ``--global`` flag.

Run the following commands, **substituting in your own information.**
Staff should use their company email here; otherwise use the email
associated with your GitHub account.

..  code-block:: bash

    git config --global user.name "Sam Smith"
    git config --global user.email sam@example.com

.. _git_importrepos:

Import Repositories
===============================

All of our projects are preconfigured to work on Linux, macOS, and Windows
Subsystem for Linux. You only need to pull them down using Git.

Decide on a place to put your repositories. I recommend creating a
:file:`repos` directory in your Home folder. Wherever you want your
repositories, create and navigate to the directory in question.
On Linux or Mac, run...

..  code-block:: bash

    mkdir ~/repos
    cd ~/repos

This is the directory where you will clone (download) the repositories you want.

Creating an SSH Key
-------------------------------

Before you can interact with one of our repositories, you must add an SSH key
to your MousePaw Media GitLab account. This will allow you to authenticate,
associating any commits or other changes to the repository with your account.

This documentation will walk you through the basics below. For more detailed
help, see the GitLab documentation
`Use SSH keys to communicate with GitLab <https://docs.gitlab.com/ee/user/ssh.html>`_.

Creating a Keypair (Linux/Mac)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You may want to first check if you already have an SSH key pair created.
This would be in the `.ssh` subdirectory of your home folder. If you have
a key pair there, you want to upload the *public key*, which is normally
`id_rsa.pub`.

If you need to create a new SSH key pair, we can do that now. We start by
installing OpenSSH Client, so we can log into other systems with SSH.

On Ubuntu, you can install OpenSSH via...

..  code-block:: bash

    sudo apt install openssh-client

If you're on Mac, the necessary software is already installed by default.

Next, we generate a new keypair. Be sure to enter the same email you have
associated with your MousePaw Media GitLab account, in place of `me@example.com`:

..  code-block:: bash

    ssh-keygen -t rsa -b 4096 -C 'me@example.com'

You'll see a message that the key pair is being generated. When prompted, press
:kbd:`Enter` to use the default path for the key.

Finally, enter a password used for unlocking the SSH key. You'll find the
new keypair at :file:`~/.ssh/id_rsa.pub`.

..  warning:: Your public key is the part you share. **NEVER EVER EVER share
    your private key** (the part ending in `.rsa`)!

Creating a Keypair (Windows)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you're using Windows Subsystem for Linux, follow the instructions above.
OpenSSH is installed by default.

Alternatively, you can create an SSH keypair using PuTTYGen or
Git Bash. We do not officially support either method, but you can find out
more from the links below:

- `OpenSSH for Windows 11 <https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_overview>`_
- `Using PuTTYGen on Windows <https://www.ssh.com/academy/ssh/putty/windows/puttygen>`_
- `Generating a new SSH Key (Git for Windows) <https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#platform-windows>`_

Configuring Git
----------------------------

If your key pair is named `id_rsa` and `id_rsa.pub`, then Git will use it
automatically. However, if your key is named anything else, or if you have
multiple keys, you will have to tell Git which key to use.

Add the following entry to the `.ssh/config` file in your home directory:

..  code-block:: text

    Host gitlab.mousepawmedia.com
        Hostname gitlab.mousepawmedia.com
        IdentityFile ~/.ssh/your_private_key
        IdentitiesOnly yes

Adding Your Public Key to GitLab
------------------------------------

Open your public key in a plain text editor, or just output it in your terminal.
On Linux, macOS, or Windows Subsystem for Linux, you can do this via...

..  code-block:: bash

    cat ~/.ssh/id_rsa.pub

On MousePaw Media's GitLab, click your profile picture (top of left sidebar)
and select :guilabel:`Preferences`. On the left, select :guilabel:`SSH Keys`.

Click :guilabel:`Add new key`, paste the public key in the text entry box
labeled :guilabel:`Key`. If desired, give the key a :guilabel:`Title`. Ensure
:guilabel:`Usage type` is set to :guilabel:`Authentication & Signing`.

If desired, set or remove the :guilabel:`Expiration date`. This is an added
security measure, but you will have to remember to add a new key after that
date before you can access the repositories again.

Finally, click :guilabel:`Add key`.

Cloning the Repository
---------------------------

On the MousePaw Media GitLab homepage, click :guilabel:`Projects` on the left
pane. Find the repository you want to clone locally and click on it.

On the repository page, click the blue :guilabel:`Clone` button, and click the
clipboard icon next to the clone URL to copy it.

In your terminal, make sure you're in the directory where you want to
check out your repositories. On Linux, macOS, or Windows Subsystem for Linux,
you'd probably do this by running the following:

..  code-block:: bash

    cd ~/repos

Then, clone the repository with the :samp:`git clone {URL}` command.
For example, to check out the IOSqueak repository with SSH, use...

..  code-block:: bash

    git clone git@gitlab.mousepawmedia.com:platform/iosqueak.git

------------------------

-*That's it!** Repeat the `Cloning the Repository`_ section as needed to
import each repository you need.
