Server Configuration: MPM Build Server
##############################################

This documentation outlines how our build server was built.

Initial Setup
==============================================

We are using a Linode 4 GB with an Ubuntu 20.04 disk instance deployed
from Marketplace. Be sure to add an SSH key, and then log in as root user.

Updates
----------------------

Before we continue, we need to check for any updates.

..  code-block:: bash

    apt update
    apt full-upgrade -y

Setting Hostname and Timezone
--------------------------------

We'll start by defining the name of our host.

..  code-block:: bash

    echo "mpmbuild" > /etc/hostname
    hostname -F /etc/hostname
    vim /etc/hosts

In that file, add the following lines below the first line, substituting the
actual IPv4 and IPv6 of the Linode in place of the NNN.NNN values:

```
NNN.NNN.NNN.NNN mpmbuild
NNNN:NNNN::NNNN:NNNN:NNNN:NNNN mpmbuild
```

Save and quit.

Now we set the timezone.

..  code-block:: bash

    dpkg-reconfigure tzdata

Use the arrow and ENTER keys to set your timezone. We'll be using US/Central.

Reboot the system.

`SOURCE: Getting Started with Linode (Linode) <https://www.linode.com/docs/getting-started>`_

Defining a Non-Root User
----------------------------

Next, we'll use the root account to set up the regular
user account.

..  code-block:: bash

    adduser mpm

Define the password for the new user, and other information if desired.
Then, we add the user to the ``sudo`` group.

..  code-block:: bash

    usermod -aG sudo mpm
    groups mpm

The second command will list all of the groups ``mpm`` is in. Ensure
it includes the ``sudo`` group.

SSH
------------------------------------------

..  note:: For security reasons, the SSH port we changed to has been
    scrubbed to ``123``.

The necessary packages for SSH were installed on installation. We need to
change a few settings and start the service.

..  code-block:: bash

    # Make a backup of the default SSH configuration.
    sudo cp /etc/ssh/sshd_config{,.bak}

    # Edit the SSH configuration.
    sudo vim /etc/ssh/sshd_config

Change ``Port 22`` to ``Port 123`` and ``PermitRootLogin yes`` to
``PermitRootLogin no``. Also add the line ``DebianBanner no`` (you can put it under
"PermitRootLogin"). The rest of the default settings should be fine for now.
Save and close the file.

Now we'll restart the service.

..  code-block:: bash

    # Restart the SSH service.
    sudo systemctl restart ssh

On the **remote machine** (the computer you're connecting *from*), run the
following command, where :code:`NNN.NNN.NNN.NNN` is the IP address of the
Linode you're connecting to.

..  code-block:: bash

    ssh-copy-id mpm@NNN.NNN.NNN.NNN -p 123

You can now connect to the non-root user account on the server via SSH.

`SOURCE: How to Use SSH To Connect To A Remote Server (Digital Ocean) <https://www.digitalocean.com/community/tutorials/how-to-use-ssh-to-connect-to-a-remote-server-in-ubuntu>`_

Set Up Longview
----------------------

It will be beneficial for us to use the free plan for Longview, a service
provided through Linode. Let's install that now.

On the Linode Cloud Manager web interface, go to Longview and add a new client.
After a moment, a box will pop up with a command to run in the server
terminal.

It will take a few minutes for Longview to start working, so just close the
window on the Linode Manager.

Install Docker
==================================

We start by installing Docker:

..  code-block:: bash

    sudo apt remove docker docker-engine docker.io
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    sudo apt update
    sudo apt install docker-ce docker-compose

Next, we set up Docker to be automatically started by systemd.

..  code-block:: bash

    sudo systemctl edit docker.service

Set the contents of that file to:

..  code-block:: text

    [Service]
    ExecStart=
    ExecStart=/usr/bin/dockerd -H fd:// -H unix:///var/run/docker.sock

Save and close, and then enable and restart Docker in systemd:

..  code-block:: bash

    sudo systemctl restart docker
    sudo systemctl enable docker

We need to add the user to the ``docker`` group. This is reasonably safe
here, as this instance is largely sandboxed.

..  code-block:: bash

    sudo groupadd docker
    sudo usermod -aG docker $USER
    newgrp docker

We also need to adjust the operating system to allow limiting the swap
memory of Docker containers:

..  code-block:: bash

    sudo nano /etc/default/grub

Edit the following line to include the following space-delimited options:

..  code-block:: text

    GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"

Be **absolutely certain** you have that line correct!

Save and close, and then run the following:

..  code-block:: bash

    sudo update-grub

Restart the computer.

`SOURCE: Install Docker Engine on Ubuntu <https://docs.docker.com/engine/install/ubuntu/>`_

`SOURCE: Post-Installation steps for Linux <https://docs.docker.com/engine/install/linux-postinstall/>`_
