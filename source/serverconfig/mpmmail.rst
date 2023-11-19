Server Configuration: MPM Email Server
##############################################

This documentation outlines how the Linode "mpmmail" server was built.

Initial Setup
==============================================

We are using a Linode 8 GB with an Ubuntu 20.04 disk instance deployed
from Marketplace. Be sure to add an SSH key, and then log in as root user.

Updates
----------------------

Before we continue, we need to check for any updates.

..  code-block:: bash

    apt update
    apt full-upgrade

Setting Hostname and Timezone
--------------------------------

We'll start by defining the name of our host.

..  code-block:: bash

    echo "mpmmail" > /etc/hostname
    hostname -F /etc/hostname
    vim /etc/hosts

In that file, add the following lines below the first line, substituting the
actual IPv4 and IPv6 of the Linode in place of the NNN.NNN values:

```
NNN.NNN.NNN.NNN mpmmail
NNNN:NNNN::NNNN:NNNN:NNNN:NNNN mpmmail
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

Server Hardening
===========================================

Let's improve our system security before continuing.

SSH Security
-------------------------

We need to lock down SSH for further security.

..  code-block:: bash

    sudo vim /etc/ssh/sshd_config

Edit the file so the following lines have the given settings:

..  code-block:: text

    PermitRootLogin no
    PasswordAuthentication no
    AuthorizedKeysFile      .ssh/authorized_keys

Save and close the file, and then run...

..  code-block:: bash

    sudo systemctl restart sshd

Firewall Settings
---------------------

Now we need to open the firewall to allow SSH and HTML to pass through,
and enable it. Be sure to change ``123`` to your SSL port from previously.

..  code-block:: bash

    sudo ufw allow 333,25,465,587,143,993,110,995,4190,80,443/tcp
    sudo ufw enable

Secure Shared Memory
--------------------------

..  code-block:: bash

    sudo vim /etc/fstab

At the bottom of the file, add the lines:

..  code-block:: text

    # Secure shared memory
    tmpfs /run/shm tmpfs defaults,noexec,nosuid 0 0

Save and close the file. The changes will take effect on next reboot.


Lock Down ``sudo`` Privilege
--------------------------------

We'll limit ``sudo`` privileges to only users in the ``admin`` group.

..  code-block:: bash

    sudo groupadd admin
    sudo usermod -a -G admin <YOUR ADMIN USERNAME>
    sudo dpkg-statoverride --update --add root admin 4750 /bin/su

Harden Network with ``sysctl`` Settings
------------------------------------------------------

..  code-block:: bash

    sudo vi /etc/sysctl.conf

Edit the file, uncommenting or adding the following lines:

..  code-block:: text

    # IP Spoofing protection
    net.ipv4.conf.all.rp_filter = 1
    net.ipv4.conf.default.rp_filter = 1

    # Ignore ICMP broadcast requests
    net.ipv4.icmp_echo_ignore_broadcasts = 1

    # Disable source packet routing
    net.ipv4.conf.all.accept_source_route = 0
    net.ipv6.conf.all.accept_source_route = 0
    net.ipv4.conf.default.accept_source_route = 0
    net.ipv6.conf.default.accept_source_route = 0

    # Ignore send redirects
    net.ipv4.conf.all.send_redirects = 0
    net.ipv4.conf.default.send_redirects = 0

    # Block SYN attacks
    net.ipv4.tcp_syncookies = 1
    net.ipv4.tcp_max_syn_backlog = 2048
    net.ipv4.tcp_synack_retries = 2
    net.ipv4.tcp_syn_retries = 5

    # Log Martians
    net.ipv4.conf.all.log_martians = 1
    net.ipv4.icmp_ignore_bogus_error_responses = 1

    # Ignore ICMP redirects
    net.ipv4.conf.all.accept_redirects = 0
    net.ipv6.conf.all.accept_redirects = 0
    net.ipv4.conf.default.accept_redirects = 0
    net.ipv6.conf.default.accept_redirects = 0

    # Ignore Directed pings
    net.ipv4.icmp_echo_ignore_all = 1

Finally, reload ``sysctl``. If there are any errors, fix the associated lines.

..  code-block:: bash

    sudo sysctl -p

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

`SOURCE: Install Docker Engine on Ubuntu <https://docs.docker.com/engine/install/ubuntu/>`_

`SOURCE: Post-Installation steps for Linux <https://docs.docker.com/engine/install/linux-postinstall/>`_

Install Mailcow
==================================

..  code-block:: bash

    sudo su
    umask
    # verify the above is 0022
    cd /opt
    git clone https://github.com/mailcow/mailcow-dockerized
    cd mailcow-dockerized
    ./generate_config.sh

Follow the prompts, specifying a FQDN for the mail admin panel. In this case,
we are using ``mail.mousepawmedia.com``.

Now run...

..  code-block:: bash

    vim mailcow.conf

Change the following lines, as well as any other settings you want to modify.

..  code-block:: text

    SKIP_CLAMD=y
    SKIP_SOLR=y

    ADDITIONAL_SAN=mail.*


Save and close, and then run...

..  code-block:: bash

    vim /opt/mailcow-dockerized/data/conf/nginx/redirect.conf

Set the contents of that file to:

..  code-block:: text

    server {
    root /web;
    listen 80 default_server;
    listen [::]:80 default_server;
    include /etc/nginx/conf.d/server_name.active;
    if ( $request_uri ~* "%0A|%0D" ) { return 403; }
    location ^~ /.well-known/acme-challenge/ {
        allow all;
        default_type "text/plain";
    }
    location / {
        return 301 https://$host$uri$is_args$args;
    }
    }

Save and close, and then run...

..  code-block:: bash

    docker-compose pull
    docker-compose up -d

Now go to ``mail.mousepawmedia.com``. It'll take a couple of minutes
to set up Mailcow for the first time, and then you can configure it.
The default login is ``admin`` and ``moohoo``. Be sure to change this
password immediately.

..  NOTE: When restarting the service, run `docker-compose down`, and then
    `docker-compose up -d`.

Configure DNS
==================================

You will need to go to the Network tab on the Linode for the mail server and
set the PTR record (Reverse DNS) to point to the mail server FQDN.

On the DNS control panel, on each domain that you want to have email to,
you'll need to add the following records:

* A/AAAA: ``mail`` subdomain pointing to the mail server IP.

* CNAME: ``autodiscover`` and ``autoconfig`` pointing to the mail server FQDN
  (``mail.mousepawmedia.com``).

* TXT: ``v=spf1 mx ~all``

* MX: ``imap.DOMAIN.TLD`` (where ``DOMAIN.TLD`` is your domain in question).

`SOURCE: mailcow: dockerized documentation (mailcow) <https://mailcow.github.io/mailcow-dockerized-docs/>`_

`SOURCE: Tutorial: How to Install and Configure Mailcow (Bennet Richter) <https://www.bennetrichter.de/en/tutorials/mailcow-dockerized/>`_
