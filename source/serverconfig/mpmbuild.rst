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

    $ apt update
    $ apt full-upgrade -y

Setting Hostname and Timezone
--------------------------------

We'll start by defining the name of our host.

..  code-block:: bash

    $ echo "mpmbuild" > /etc/hostname
    $ hostname -F /etc/hostname
    $ vim /etc/hosts

In that file, add the following lines below the first line, substituting the
actual IPv4 and IPv6 of the Linode in place of the NNN.NNN values:

```
NNN.NNN.NNN.NNN mpmbuild
NNNN:NNNN::NNNN:NNNN:NNNN:NNNN mpmbuild
```

Save and quit.

Now we set the timezone.

..  code-block:: bash

    $ dpkg-reconfigure tzdata

Use the arrow and ENTER keys to set your timezone. We'll be using US/Central.

Reboot the system.

`SOURCE: Getting Started with Linode (Linode) <https://www.linode.com/docs/getting-started>`_

Defining a Non-Root User
----------------------------

Next, we'll use the root account to set up the regular
user account.

..  code-block:: bash

    $ adduser mpm

Define the password for the new user, and other information if desired.
Then, we add the user to the ``sudo`` group.

..  code-block:: bash

    $ usermod -aG sudo mpm
    $ groups mpm

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
    $ sudo cp /etc/ssh/sshd_config{,.bak}

    # Edit the SSH configuration.
    $ sudo vim /etc/ssh/sshd_config

Change ``Port 22`` to ``Port 123`` and ``PermitRootLogin yes`` to
``PermitRootLogin no``. Also add the line ``DebianBanner no`` (you can put it under
"PermitRootLogin"). The rest of the default settings should be fine for now.
Save and close the file.

Now we'll restart the service.

..  code-block:: bash

    # Restart the SSH service.
    $ sudo systemctl restart ssh

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

    $ sudo apt remove docker docker-engine docker.io
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    $ sudo apt update
    $ sudo apt install docker-ce docker-compose

Next, we set up Docker to be automatically started by systemd.

..  code-block:: bash

    $ sudo systemctl edit docker.service

Set the contents of that file to:

..  code-block:: text

    [Service]
    ExecStart=
    ExecStart=/usr/bin/dockerd -H fd:// -H unix:///var/run/docker.sock

Save and close, and then enable and restart Docker in systemd:

..  code-block:: bash

    $ sudo systemctl restart docker
    $ sudo systemctl enable docker

We need to add the user to the ``docker`` group. This is reasonably safe
here, as this instance is largely sandboxed.

..  code-block:: bash

    $ sudo groupadd docker
    $ sudo usermod -aG docker $USER
    $ newgrp docker

We also need to adjust the operating system to allow limiting the swap
memory of Docker containers:

..  code-block:: bash

    $ sudo nano /etc/default/grub

Edit the following line to include the following space-delimited options:

..  code-block:: text

    GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"

Be **absolutely certain** you have that line correct!

Save and close, and then run the following:

..  code-block:: bash

    $ sudo update-grub

Restart the computer.

`SOURCE: Install Docker Engine on Ubuntu <https://docs.docker.com/engine/install/ubuntu/>`_

`SOURCE: Post-Installation steps for Linux <https://docs.docker.com/engine/install/linux-postinstall/>`_







Install Docker Registry
==================================

Creating the Docker Registry
-----------------------------------

The Docker Registry hosts images for creating Docker containers. This speeds
up build times significantly.

I start by pointing Docker to the directory I created for the registry earlier.

..  code-block:: bash

    $ docker run -d -p 5000:5000 \
    --restart=always \
    --name registry \
    -v /opt/registry:/var/lib/registry \
    registry:2

..  warning:: Do not run Docker containers as ``--privileged``. Ever.

I can now set up Apache2 to host the registry.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/registry.conf

Set the contents of that file to:

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName registry.mousepawmedia.com
            ServerAdmin hawksnest@mousepawmedia.com

            Header add X-Forwarded-Proto "https"
            RequestHeader add X-Forwarded-Proto "https"

            SSLEngine on
            SSLCertificateFile  /etc/letsencrypt/live/registry.mousepawmedia.com/fullchain.pem
            SSLCertificateKeyFile /etc/letsencrypt/live/registry.mousepawmedia.com/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf

            ProxyRequests     off
            ProxyPreserveHost on

            # no proxy for /error/ (Apache HTTPd errors messages)
            ProxyPass /error/ !

            ProxyPass         /v2  http://127.0.0.1:5000/v2
            ProxyPassReverse  /v2  http://127.0.0.1:5000/v2

            <Location /v2>
                Order deny,allow
                Allow from all

                #AllowOverride AuthConfig
                AuthName "Registry Authentication"
                AuthType basic
                AuthBasicProvider ldap
                AuthLDAPURL "ldap://id.mousepawmedia.com:389/ou=Users,dc=id,dc=mousepawmedia,dc=com"
                Require valid-user
            </Location>

            ProxyPass         /v1  http://127.0.0.1:5000/v1
            ProxyPassReverse  /v1  http://127.0.0.1:5000/v1

            # Allow ping and users to run unauthenticated
            <Location /v1/_ping>
                Satisfy any
                Allow from all
            </Location>

            ProxyPass         /_ping  http://127.0.0.1:5000/_ping
            ProxyPassReverse  /_ping  http://127.0.0.1:5000/_ping

            # Allow ping and users to run authenticated
            <Location /_ping>
                Satisfy any
                Allow from all
            </Location>
        </VirtualHost>
    </IfModule>

Save and close. Now we enable the site:

..  code-block:: bash

    $ sudo a2ensite registry
    $ sudo a2enmod headers authnz_ldap proxy_http
    $ sudo systemctl restart apache2

We can test it out with the following command:

..  code-block:: bash

    $ docker login registry.mousepawmedia.com
    $ docker pull ubuntu:18.04
    $ docker tag ubuntu:18.04 registry.mousepawmedia.com/mpm-bionic
    $ docker push registry.mousepawmedia.com/mpm-bionic
    $ docker image remove ubuntu:18.04
    $ docker image remove registry.mousepawmedia.com/mpm-bionic
    $ docker pull registry.mousepawmedia.com/mpm-bionic

Login with valid LDAP credentials. If it succeeds, everything is correctly
configured.

We now tear down our initial test container, and switch to ``docker-compose``
for running our registry long-term instead:

..  code-block:: bash

    $ docker container stop registry
    $ docker container rm -v registry
    $ sudo mkdir -p /opt/docker/registry
    $ vim /opt/docker/registry/docker-compose.yml

Set the contents of that file to:

..  code-block:: text

    registry:
        restart: always
        image: registry:2
        ports:
            - 5000:5000
        volumes:
            - /opt/registry:/var/lib/registry
            - /opt/registry/config.yml:/etc/docker/registry/config.yml

Save and close. Then run...

..  code-block:: bash

    $ sudo vim /opt/registry/config.yml

Set the contents of that file to...

..  code-block:: text

    storage:
        s3:
            accesskey: ACCESSKEYHERE
            secretkey: SECRETKEYHERE
            regionendpoint: mpm-registry.us-east-1.linodeobjects.com/
            region: us-east-1
            bucket: mpm-registry
            encrypt: false
            secure: true

Save and close. Now run...

..  code-block:: bash

    $ sudo chown root:docker -R /opt/docker
    $ cd /opt/docker/registry
    $ sudo docker-compose up -d

The registry will now automatically restart with the ``docker.service``
managed by ``systemctl``.

..  note:: If you need to see the logs for the registry, run
    :code:`docker logs -f registry`. The name of the instance may be different
    for you; see `docker ps`.

SOURCE: `Securing a docker registry behind Apache <https://lathonez.com/2016/docker-registry-apache-letsencrypt/>`_

Setting Up Docker Login with Pass
-----------------------------------

For additional security, we want to use ``pass`` to store credentials for
Docker registry logins.

I'll start by logging out of the registry, and installing ``pass``:

..  code-block:: bash

    $ sudo docker logout registry.mousepawmedia.com
    $ sudo apt install pass

Before I go any further, I need a GPG key for the main user account.
I can create a new one with the following commands:

..  code-block:: bash

    gpg --gen-key

Fill out the form, using defaults for everything except the name and email.

After creating it, or if I already have one I want to use, I can access the
key by running ``gpg --list-keys``, and then looking for the name associated
with the key for this user account. (We MUST have the associated private key
accessible on this machine as well.)

For example, here's the key I want:

..  code-block:: text

    pub   2048R/B4B6AD7C 2020-04-14
    uid                  MousePaw Media (Hawksnest) <hawksnest@mousepawmedia.com>
    sub   2048R/0F2AE99F 2020-04-14

I copy the public key, which is the part after the forward-slash (``/``) on the
first line; in this case, that's ``B4B6AD7C``.

I pass that to the next command, in place of ``PUBLICKEYTOUSE``:

..  code-block:: bash

    $ pass init PUBLICKEYTOUSE
    $ pass git init

Now I can install the ``docker-credential-helpers`` package I'll need.
You should get the URL for the latest version from the
`docker-credential-helpers GitHub Releases page <https://github.com/docker/docker-credential-helpers/releases>`_

..  code-block:: bash

    cd /tmp
    wget https://github.com/docker/docker-credential-helpers/releases/download/v0.6.3/docker-credential-pass-v0.6.3-amd64.tar.gz
    tar -xf docker-credential-pass-v0.6.3-amd64.tar.gz
    chmod +x docker-credential-pass
    sudo mv docker-credential-pass /usr/local/bin/.

Test that installed correctly by running the following:]

..  code-block:: bash

    $ sudo docker-credential-pass version

That should print out the version of ``docker-credential-pass` that is
installed. If it works, you should also make sure it is communicating with
``pass`` via the following command:

..  code-block:: bash

    $ sudo docker-credential-pass list

If that returns ``{}`` or some other data (instead of an error or warning),
everything is correctly installed.

Finally, I'll tell Docker to use ``docker-credential-pass``:

..  code-block:: bash

    $ vim ~/.docker/config.json

Set the ``"credsStore"`` property to ``"pass"`` (include the quotes!), and
then save and close.

You should now be able to login with Docker, which you can test via:

..  code-block:: bash

    $ sudo docker login registry.mousepawmedia.com

Enter valid LDAP credentials. If it works, you'll see:

..  code-block:: text

    Login Succeeded
