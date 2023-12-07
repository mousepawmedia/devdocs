..  _docker:

Docker
#################################

You can use Docker to build and run code in a clean environment, especially
one with different packages. This is especially helpful if your system is not
able to run our standard build environment.

Visual Studio Code has excellent integration for Docker. We recommend installing
the **Docker** extension from Microsoft.

..  _docker_installing:

Installing Docker Engine
=================================

For Linux, we recommend Docker Engine over Docker Desktop:

- `Install Docker Engine on Ubuntu <https://docs.docker.com/engine/install/ubuntu/>`_
- `Install Docker Engine on Debian <https://docs.docker.com/engine/install/debian/>`_

For Windows, we strongly advise installing Docker Engine within Windows
Subsystem for Linux, as the user experience is much smoother in our experience.

..  note:: Linux and WSL users should be sure to follow the guides for
    ``Manage Docker as a non-root user`` and
    ``Configure Docker to start on boot`` outlined on the
    `Post-installation steps for Linux | Docker Documentation <https://docs.docker.com/install/linux/linux-postinstall/>`_.

For macOS, you'll need to install Docker Desktop instead:

- `Install Docker Desktop on macOS <https://docs.docker.com/desktop/install/mac-install/>`_

..  _docker_login:

Docker Registry Login
=================================

We host Docker images on our own private registry run by our GitLab instance.

This section describes how to set this up for Linux, specifically Ubuntu,
although the instructions should be the same across most distros.

For Windows or macOS, or for more options and information in general, see
`docker login | Docker Documentation <https://docs.docker.com/engine/reference/commandline/login>`_

..  _docker_login_install:

Installing and Configuring Pass
---------------------------------

First, install ``pass`` on your system. This should be available in your
distribution's package manager. On Debian-based systems, run:

..  code-block:: bash

    sudo apt install pass

Download and install the latest version of ``docker-credential-pass``.
This is usually downloaded as a precompiled binary and installed in place.
The latest version can be found on the
`docker-credential-helpers GitHub Releases page <https://github.com/docker/docker-credential-helpers/releases>`_

The latest installation instructions (as of writing) for most 64-bit
systems is as follows:

..  code-block:: bash

    cd /tmp
    wget https://github.com/docker/docker-credential-helpers/releases/download/v0.8.0/docker-credential-pass-v0.8.0.linux-amd64
    mv docker-credential-pass-v0.8.0.linux-amd64 docker-credential-pass
    chmod +x docker-credential-pass
    sudo install docker-credential-pass /usr/local/bin

Test that this installed correctly by running the following:

..  code-block:: bash

    gpg --list-keys

This will show *all* public keys known to your computer, including any
belonging to other people that you have stored for verification purposes.
Scroll through to find your name and email, and then grab your key.
(It's the long value towards the top with all the uppercase numbers and
letters).

For example, here's mine::

    pub   rsa4096 2022-03-02 [SC]
        FF4678D9D3A42F78D2885F8EE038FEDD4C3CF53D
    uid           [ultimate] Jason C. McDonald (MousePaw Media) <jcmcdonald@mousepawmedia.com>
    uid           [ultimate] [jpeg image of size 3881]
    sub   rsa4096 2022-03-02 [E]


So, ``FF4678D9D3A42F78D2885F8EE038FEDD4C3CF53D`` would be my public key.
Note, I cannot actually sign anything with that key unless I also have the
private key on my system! (Of course, I do.)

..  WARNING: Always keep your private keys secret, and maintain secure backups
    of them in case anything happens to your computer. You cannot recover
    a lost private key, and if you don't have it, you can't access anything
    encrypted with said key.

If this step worked, skip down to :ref:`docker_login_install_gpg_init`.

..  _docker_login_install_gpg_new:

New GPG Key
""""""""""""""""""""""""""""""

If you don't have a GPG key, you can generate a new one easily:

..  code-block:: bash

    gpg --gen-key

Press :kbd:`Enter` to select the default for the first three options, and
then confirm your choices by pressing :kbd:`y` and :kbd:`Enter`. Now it will
prompt you for your real name, email address, and an optional comment.

If you choose, you can also set a passphrase at this time, although it is not
required. If you do set a passphrase, be careful not to lose it!
There is no way to reset it or recover a password for a GPG key.

At this time, you may see that the computer is trying to "gain entropy". Just
switch to another window or terminal and do other things for a bit. As you use
the computer, it'll create that entropy it needs.

Once you're done, you'll see that your key was created. Grab the key from the
Look for the line marked ``pub``, and get the key from *after* the first
forward-slash (``/``).

..  _docker_login_install_gpg_init:

Initialzing Pass
"""""""""""""""""""""""""""""

Initialize ``pass`` with the following command, substituting your GPG key
in place of ``MYGPGKEYHERE``:

..  code-block:: bash

    pass init MYGPGKEYHERE

If all goes well, then the following command should work:

..  code-block:: bash

    docker-credential-pass list

If ``pass`` is not configured, you'll receive a warning like "pass store is
uninitialized.".

You'll first need to generate a Personal Access Token on your MousePaw Media
GitLab account.

In a browser, log into the MousePaw Media GitLab. Click your profile picture
at the top of the left pane and select :guilabel:`Preferences`. On the left,
select :guilabel:`Access Tokens`. Click :guilabel:`Add new token`.

Give your token a name, and select the scopes :guilabel:`read_registry` and
:guilabel:`write_registry`. Then click :guilabel:`Create personal access token`.

A green box will appear at the top of the page with the heading
:guilabel:`Your new personal access token`. Click the clipboard icon to the
left of the text field to copy the access token.

Once you've copied this personal access key, open your terminal. Enter the
following:

..  code-block:: bash

    docker login gitlab.mousepawmedia.com

At the prompts, enter your username and your personal access token.
If login is successful, you will see "Login Succeeded".

You can now work with MousePaw Media's private Docker image registry.
