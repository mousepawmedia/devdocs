Server Configuration: MPM Collaboration Server
##################################################

This documentation outlines how the Linode "Webster" server was built.

Initial Setup
==============================================

We are using a Linode 4 GB with an Ubuntu 20.04 disk instance deployed
from Marketplace. Be sure to add an SSH key, and then log in as root user.

Updates
----------------------

Before we continue, we need to check for any updates.

..  code-block:: bash

    $ apt update
    $ apt full-upgrade

Setting Hostname and Timezone
--------------------------------

We'll start by defining the name of our host.

..  code-block:: bash

    $ echo "mpmlool" > /etc/hostname
    $ hostname -F /etc/hostname
    $ vim /etc/hosts

In that file, add the following lines below the first line, substituting the
actual IPv4 and IPv6 of the Linode in place of the NNN.NNN values:

```
NNN.NNN.NNN.NNN mpmlool
NNNN:NNNN::NNNN:NNNN:NNNN:NNNN mpmlool
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

Setup LAMP
==========================

Apache2
--------------------------

We'll start by setting up Apache2.

..  code-block:: bash

    $ sudo apt install apache2
    $ sudo vim /etc/apache2/apache2.conf

Next, we'll edit the configuration file to turn off ``KeepAlive``, as that
uses up extra memory (and we don't have that much to spare). We'll also set
the ``ServerName`` (you may need to add this entry).

..  code-block:: apache

    ServerName 127.0.0.1
    KeepAlive Off

Scroll down to the section with all the directories, and add this entry.
(Be mindful to use tabs, not spaces, to be consistent with the rest of
the file.)

..  code-block:: apache

    <DirectoryMatch /\.git/>
        Options FollowSymLinks
        AllowOverride None
        Require all denied
    </DirectoryMatch>

Save and close.

Next, we'll change the settings for the ``mpm_prefork`` module.

..  code-block:: bash

    $ sudo vim /etc/apache2/mods-available/mpm_prefork.conf

Set the file to the following...

..  code-block:: apache

    <IfModule mpm_prefork_module>
            StartServers            2
            MinSpareServers         5
            MaxSpareServers         10
            MaxRequestWorkers       39
            MaxConnectionsPerChild  3000
    </IfModule>

Save and close. Now we'll enable the prefork module and restart Apache2.

..  code-block:: bash

    $ sudo a2dismod mpm_event
    $ sudo a2enmod mpm_prefork
    $ sudo systemctl restart apache2

Next, we will add our user to the ``www-data`` group, which will be
helpful for permissions.

..  code-block:: bash

    $ sudo usermod -aG www-data mpm

Browse to the web server using the IP or whatever address is most convenient,
and ensure the Apache2 default page is appearing.

Scripts
--------------------------

We need to save a number of scripts for regular use.

..  code-block:: bash

    $ sudo mkdir -p /opt/scripts/sys
    $ sudo mkdir -p /opt/scripts/root
    $ sudo chown root:root /opt/scripts/root
    $ sudo chmod 700 /opt/scripts/root

Now we add the system scripts to the path for the main user.

..  code-block:: bash

    $ vim ~/.bashrc

Add the following to that file:

..  code-block:: bash

    PATH=$PATH:/opt/scripts/sys

Save and close. Log in as root and repeat, only with the following instead:

..  code-block:: bash

    PATH=$PATH:/opt/scripts/sys:/opt/scripts/root

Save and close that too. You can immediately update the path for your current
login session with :code:`source ~/.bashrc`.

Let's Encrypt Certbot
===============================================

We need to install the Let's Encrypt Certbot for generating certificates.

..  code-block:: bash

    $ sudo snap install core; sudo snap refresh core
    $ sudo snap install --classic certbot
    $ sudo ln -s /snap/bin/certbot /usr/bin/certbot
    $ sudo certbot register

Follow the instructions to register with Let's Encrypt.

We'll actually generate certificates in a later step.

`SOURCE: Certbot (Certbot) <https://certbot.eff.org/lets-encrypt/ubuntufocal-apache>`_

Scheduling Auto-Renewal
-----------------------------------

Now we need to schedule the autorenewal task.

..  code-block:: bash

    $ sudo crontab -e

Add the following line to the end:

..  code-block:: text

    41 5 * * * /usr/bin/certbot renew

This will run the renewal script once a day at 5:41am. (Let's Encrypt asks
that a random time be used by each user, to spread out server load.)

Server Hardening
===========================================

Let's improve our system security before continuing.

SSH Security
-------------------------

We need to lock down SSH for further security.

..  code-block:: bash

    $ sudo vim /etc/ssh/sshd_config

Edit the file so the following lines have the given settings:

..  code-block:: text

    PermitRootLogin no
    PasswordAuthentication no
    AuthorizedKeysFile      .ssh/authorized_keys

Save and close the file, and then run...

..  code-block:: bash

    $ sudo systemctl restart sshd

Firewall Settings
---------------------

Now we need to open the firewall to allow SSH and HTML to pass through,
and enable it. Be sure to change ``123`` to your SSL port from previously.

..  code-block:: bash

    $ sudo ufw allow 123
    $ sudo ufw allow 80
    $ sudo ufw allow 443
    $ sudo ufw enable

Secure Shared Memory
--------------------------

..  code-block:: bash

    $ sudo vim /etc/fstab

At the bottom of the file, add the lines:

..  code-block:: text

    # Secure shared memory
    tmpfs /run/shm tmpfs defaults,noexec,nosuid 0 0

Save and close the file. The changes will take effect on next reboot.


Lock Down ``sudo`` Privilege
--------------------------------

We'll limit ``sudo`` privileges to only users in the ``admin`` group.

..  code-block:: bash

    $ sudo groupadd admin
    $ sudo usermod -a -G admin <YOUR ADMIN USERNAME>
    $ sudo dpkg-statoverride --update --add root admin 4750 /bin/su

Harden Network with ``sysctl`` Settings
------------------------------------------------------

..  code-block:: bash

    $ sudo vi /etc/sysctl.conf

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

    $ sudo sysctl -p

Harden Apache2
---------------------------------------------

Edit the Apache2 security configuration file...

..  code-block:: bash

    $ sudo vim /etc/apache2/conf-available/security.conf

Change or add the following lines:

..  code-block:: text

    ServerTokens Prod
    ServerSignature Off
    TraceEnable Off
    FileETag None

Restart the Apache2 server and make sure it still works.

..  code-block:: bash

    $ sudo systemctl restart apache2

Setup ModSecurity
---------------------------------------------------

First, install the necessary dependencies. We'll also need to create a
symbolic link to work around a bug on 64-bit systems. Finally, we'll install
the package itself.

..  code-block:: bash

    $ sudo apt install libxml2 libxml2-dev libxml2-utils libaprutil1 libaprutil1-dev
    $ sudo ln -s /usr/lib/x86_64-linux-gnu/libxml2.so.2 /usr/lib/libxml2.so.2
    $ sudo apt install libapache2-mod-security2

Now we'll copy the default configuration.

..  code-block:: bash

    $ sudo mv /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf

Now we download the latest OWASP security rules.

..  code-block:: bash

    $ cd /etc/modsecurity
    $ sudo wget https://github.com/coreruleset/coreruleset/archive/v3.3.0.tar.gz
    $ sudo tar -xvf v3.3.0.tar.gz
    $ sudo mv coreruleset-3.3.0 owasp-modsecurity-crs
    $ cd owasp-modsecurity-crs
    $ sudo mv crs-setup.conf.example crs-setup.conf
    $ cd rules
    $ sudo mv REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf
    $ sudo mv RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf.example RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf

You may need to edit :file:`/etc/modsecurity/owasp-modsecurity-crs/crs-setup.conf`
to match your server's situation. For example, we enabled Project Honeypot.

Edit the configuration for the ModSecurity Apache module...

..  code-block:: bash

    $ sudo vim /etc/apache2/mods-available/security2.conf

Change the ``IncludeOptional`` entries to match the following:

..  code-block:: apache

    IncludeOptional /etc/modsecurity/*.conf
    IncludeOptional /etc/modsecurity/owasp-modsecurity-crs/crs-setup.conf

    IncludeOptional /etc/modsecurity/owasp-modsecurity-crs/plugins/*-before.conf
    IncludeOptional /etc/modsecurity/owasp-modsecurity-crs/rules/*.conf
    IncludeOptional /etc/modsecurity/owasp-modsecurity-crs/plugins/*-after.conf

Enable the modules and restart Apache2, ensuring that it still works.

..  code-block:: bash

    $ sudo a2enmod headers security2
    $ sudo systemctl restart apache2

Finally, to make sure it works, go to ``http://<serveraddress>/?param="><script>alert(1);</script>``.
Check ``/var/log/apache2/error.log`` for an error report from ``mod_security``.
If one is there, the configuration worked!

Setup Fail2Ban
-----------------------------------------------

Fail2Ban locks out IP addresses that repeatedly attempt invalid or malicious
actions.

..  code-block:: bash

    $ sudo apt install fail2ban
    $ sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
    $ sudo vim /etc/fail2ban/jail.local

To turn on various "jails", scroll down to the ``# JAILS`` section. Place
``enabled = true`` under each jail name you want turned on. This is the list
of jails we enabled for this server:

- sshd
- sshd-ddos
- apache-auth
- apache-badbots
- apache-noscript
- apache-overflows
- apache-nohome
- apache-botsearch
- apache-fakegooglebot
- apache-modsecurity
- apache-shellshock
- recidive

I also added ``sshd-ddos`` by including this entry:

..  code-block:: text

    [sshd-ddos]
    mode = ddos
    enabled = true
    port = ssh
    logpath = %(sshd_log)s
    filter = sshd

Be sure you look through the jails and enable any additional jails that will
be appropriate to your server's configuration and applications.

For the ``[recidive]`` jail to work correctly, a couple of settings need to
be changed in Fail2Ban's configuration:

..  code-block:: bash

    $ sudo cp /etc/fail2ban/fail2ban.conf /etc/fail2ban/fail2ban.local
    $ sudo vim /etc/fail2ban/fail2ban.local

Change the following values:

..  code-block:: text

    # NEVER SET TO DEBUG!!! [recidive] jail is enabled
    loglevel = INFO

    dbpurgeage = 648000

Save and close. Run the following command to ensure there are no errors:

..  code-block:: bash

    $ sudo systemctl stop fail2ban
    $ sudo fail2ban-client -x start

Finally, restart the fail2ban process.

..  code-block:: bash

    $ sudo systemctl restart fail2ban

Setup PSAD
------------------------------------------

..  code-block:: bash

    $ sudo apt install -y psad
    $ sudo iptables -A INPUT -j LOG
    $ sudo iptables -A FORWARD -j LOG
    $ sudo ip6tables -A INPUT -j LOG
    $ sudo ip6tables -A FORWARD -j LOG
    $ sudo vim /etc/psad/psad.conf

Change the following values:

..  code-block:: text

    EMAIL_ADDRESS mpm@localhost;
    HOSTNAME mpmlool;
    ALERTING_METHODS noemail;
    EMAIL_THROTTLE 100;
    ALERT_ALL N;
    ENABLE_AUTO_IDS_EMAIL N;
    ENABLE_DNS_LOOKUPS N;
    ENABLE_WHOIS_LOOKUPS N;

Save and close, and then reload like this:

..  code-block:: bash

    $ sudo psad -R; sudo psad --sig-update; sudo psad -H; sudo psad --Status

When you run that last command, it may whine about not finding a pidfile.
It appears we can ignore that error.

We also need to tweak Fail2Ban so that it doesn't start up before ``psad`` does.
Otherwise, ``psad`` won't be able to log correctly.

..  code-block:: bash

    $ sudo vim /lib/systemd/system/fail2ban.service

In that file, add ``ufw.service`` and ``psad.service`` to the ``After=`` directive,
so it looks something like this:

..  code-block:: text

    After=network.target iptables.service firewalld.service ufw.service psad.service

Save and close, and then reload the daemons for systemctl and restart fail2ban.

..  code-block:: bash

    $ sudo systemctl daemon-reload
    $ sudo systemctl restart fail2ban

Now we need to adjust the UFW settings.

..  code-block:: bash

    $ sudo ufw logging high
    $ sudo vim /etc/ufw/before.rules

Add the following lines before the final commit message:

..  code-block:: text

    -A INPUT -j LOG
    -A FORWARD -j LOG

Save and close. Repeat this with ``before6.rules``. Then, restart ufw and
reload PSAD.

`SOURCE: PSAD Is Giving a Firewall Setup Warning (Ubuntu Forums) <https://ubuntuforums.org/showthread.php?t=2047977>`_

..  code-block:: bash

    $ sudo systemctl restart ufw
    $ sudo psad --fw-analyze

Restart the computer, and ensure PSAD isn't sending any system emails
complaining about the firewall configuration. (Check system email by
running ``$ mail``).

Rootkit Checks
--------------------------------------------

We use two different rootkit checkers.

..  code-block:: bash

    $ sudo apt install rkhunter chkrootkit
    $ sudo vim /opt/scripts/root/rootkitscan

Set the contents of that file to the following:

..  code-block:: bash

    #!/bin/bash
    chkrootkit
    rkhunter --update
    rkhunter --propupd
    rkhunter --check --cronjob -l
    echo "Rootkit Check Done!"


Miscellaneous
----------------------------------------------

These are a few other useful programs.

..  code-block:: bash

    $ sudo apt install nmap logwatch libdate-manip-perl apparmor apparmor-profiles tiger clamav

    # Ensure apparmor is working.
    $ sudo apparmor_status

To use logwatch, run...

..  code-block:: bash

    $ sudo logwatch | less

To scan for vulnerabilites with Tiger, run...

..  code-block:: bash

    $ sudo tiger
    $ sudo less /var/log/tiger/security.report.*

Adding Sites
============================

For each site, we need to configure Apache2 and get an SSL certificate.

First, we need to enable the SSL module for Apache2. This usually only needs
to be done the first time.

..  code-block:: bash

    $ sudo a2enmod ssl
    $ sudo systemctl restart apache2

We start by generating a certificate for the domain being added.

The ``000-default`` Apache site is what we'll use for initially generating on
a domain name. After generating the cert, we disable that site again so the
other sites will work.

..  code-block:: bash

    $ sudo a2ensite 000-default
    $ sudo systemctl reload apache2
    $ sudo certbot certonly --apache -d lool.mousepawmedia.com

In the output for the certbot command, take note of the paths where the
certificate and chain were saved. You'll need that in the next step.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/lool.conf

Set the contents of that file to the following, substituting the
domain name in place for :code:`ServerName`, and fixing the paths for
the :code:`SSLCertificateFile` and :code:`SSLCertificateKeyFile`.
Also set the :code:`DocumentRoot` to the desired directory.

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName lool.mousepawmedia.com

            ServerAdmin webmaster@mousepawmedia.com
            DocumentRoot /opt/lool

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            SSLCertificateFile /etc/letsencrypt/live/lool.mousepawmedia.com/fullchain.pem
            SSLCertificateKeyFile /etc/letsencrypt/live/lool.mousepawmedia.com/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf
            Header always set Strict-Transport-Security "max-age=31536000"
            Header always set Content-Security-Policy upgrade-insecure-requests

            <FilesMatch "\.(cgi|shtml|phtml|php)$">
                SSLOptions +StdEnvVars
            </FilesMatch>
            <Directory /usr/lib/cgi-bin>
                SSLOptions +StdEnvVars
            </Directory>

            BrowserMatch "MSIE [2-6]" \
                nokeepalive ssl-unclean-shutdown \
                downgrade-1.0 force-response-1.0
            # MSIE 7 and newer should be able to use keepalive
            BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown
        </VirtualHost>
    </IfModule>

Save and close. Now we'll create the directory we specified
in :code:`DocumentRoot`.

..  code-block:: bash

    $ cd /opt
    $ sudo mkdir lool
    $ sudo chown www-data:www-data lool
    $ sudo chmod 775 lool

We need to tell Apache2 to read this directory.

..  code-block:: bash

    $ sudo vim /etc/apache2/apache2.conf

Scroll down to the section with all the directories, and add these entries:

..  code-block:: apache

    <Directory /opt/lool/>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

Save and close.

Consider creating an ``index.html`` in that folder, with the text "Hello, world!",
just to make sure the site works.

Now we disable the default site, enable the new site, and restart Apache2.

..  code-block:: bash

    $ sudo a2dissite 000-default
    $ sudo a2ensite lool
    $ sudo systemctl restart apache2

Ensure the new domain works over http.

Port Forward 80 to 443
----------------------------------

With that set up, we want to redirect port 80 to port 443.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/000-redirect.conf

Set the contents of that file to...

..  code-block:: apache

    <VirtualHost *:80>
        RewriteEngine On
        RewriteCond %{HTTPS} off
        RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI}
    </VirtualHost>

Save and close. Load the site, make sure ``mod_rewrite`` is enabled, and then
restart Apache2.

..  code-block:: bash

    $ sudo a2ensite 000-redirect
    $ sudo a2enmod rewrite
    $ sudo systemctl restart apache2

Navigating to ``http://<serveraddress>`` should now redirect properly to
Navigate to ``https://<serveraddress>``. The same will apply for any subdirectory
thereof, such as ``http://<serveraddress>/docs``.

..  note:: Apache2 sites are loaded in alphabetical order. Addresses and ports
    are first come, first served, so the first site that defines on a port
    gets all addresses on that port, unless something else snatches away
    a specific address.

Install Collabora Office
==================================

Docker
----------------------------------

We start by installing Docker:

..  code-block:: bash

    $ sudo apt-get remove docker docker-engine docker.io
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

..  warning:: DO NOT add ``mpm`` to the ``docker`` group. You want to
    really strictly limit control of Docker.

We also need to adjust the operating system to allow limiting the swap
memory of Docker containers:

..  code-block:: bash

    $ sudo nano /etc/default/grub

Edit the following line to match the following:

..  code-block:: text

    GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"

Be **absolutely certain** you have that line correct!

Save and close, and then run the following:

..  code-block:: bash

    $ sudo update-grub

Restart the computer.

Collabora Office Online
--------------------------------

Next, we'll pull in the Docker container for Collabora Office online.

..  code-block:: bash

    $ sudo docker pull collabora/code

This download will take a while, so sit back and wait.

Next, we'll deploy the docker image. Make sure you substitute your value
in on the ``password`` option.

..  code-block:: bash

    $ sudo docker run -t -d -p 127.0.0.1:9980:9980 -e 'domain=cloud\\.mousepawmedia\\.com|cloud\\.ajcharlesonpublishing\\.com|cloud\\.codemouse92\\.com|cloud\\.bughunters\\.cafe' -e 'username=admin' -e 'password=CollaboraPassword' --restart always --cap-add MKNOD collabora/code

..  warning:: Do not run Docker containers as ``--privileged``. Ever.

Next, we will set up Apache to proxy to Collabora Office.

..  code-block:: bash

    $ sudo a2enmod proxy proxy_wstunnel proxy_http ssl
    $ sudo systemctl restart apache2
    $ sudo vim /etc/apache2/sites-available/lool.conf

Set the contents of that file to the following...

..  code-block:: apache

    <VirtualHost *:443>
        ServerName lool.mousepawmedia.com:443

        SSLEngine on
        SSLCertificateFile /etc/letsencrypt/live/lool.mousepawmedia.com/fullchain.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/lool.mousepawmedia.com/privkey.pem
        Include /etc/letsencrypt/options-ssl-apache.conf

        # Encoded slashes need to be allowed
        AllowEncodedSlashes NoDecode

        # Container uses a unique non-signed certificate
        SSLProxyEngine On
        SSLProxyVerify None
        SSLProxyCheckPeerCN Off
        SSLProxyCheckPeerName Off

        # keep the host
        ProxyPreserveHost On

        # static html, js, images, etc. served from loolwsd
        # loleaflet is the client part of LibreOffice Online
        ProxyPass           /loleaflet https://127.0.0.1:9980/loleaflet retry=0
        ProxyPassReverse    /loleaflet https://127.0.0.1:9980/loleaflet

        # WOPI discovery URL
        ProxyPass           /hosting/discovery https://127.0.0.1:9980/hosting/discovery retry=0
        ProxyPassReverse    /hosting/discovery https://127.0.0.1:9980/hosting/discovery

        # Capabilities
        ProxyPass           /hosting/capabilities https://127.0.0.1:9980/hosting/capabilities retry=0
        ProxyPassReverse    /hosting/capabilities https://127.0.0.1:9980/hosting/capabilities


        # Main websocket
        ProxyPassMatch "/lool/(.*)/ws$" wss://127.0.0.1:9980/lool/$1/ws nocanon

        # Admin Console websocket
        ProxyPass   /lool/adminws wss://127.0.0.1:9980/lool/adminws

        # Download as, Fullscreen presentation and Image upload operations
        ProxyPass           /lool https://127.0.0.1:9980/lool
        ProxyPassReverse    /lool https://127.0.0.1:9980/lool
    </VirtualHost>

Save and close.

..  important:: The above Apache2 configuration is for `CODE 2.0 updates 2 <https://www.collaboraoffice.com/community-en/code-2-0-updates-2/>`_
    and onward. Using the old configuration will break things.

Then, enable the site and restart Apache2.

..  code-block:: bash

    $ sudo a2ensite lool
    $ sudo systemctl restart apache2

You can see stats and admin options at ``https://lool.<serveraddress>/loleaflet/dist/admin/admin.html``.

`SOURCE: Setting up Apache2 reverse proxy - Collabora <https://www.collaboraoffice.com/code/apache-reverse-proxy/>`_

Preventing Lockouts
--------------------------------

Last, we need to modify fail2ban so it won't lock users out when using
CollaboraOffice.

..  code-block:: bash

    $ sudo mkdir /var/log/lool
    $ sudo vim /etc/fail2ban/filter.d/lool.conf

Set the contents of that file to:

..  code-block:: text

    [Definition]
    failregex={"reqId":".*","remoteAddr":".*","app":"core","message":"Login failed: '.*' \(Remote IP: '<host>'\)","level":2,"time":".*"}
    ignoreregex =

Save and close. Then, run...

..  code-block:: bash

    $ sudo vim /etc/fail2ban/jail.local

Add the following to the bottom of that file:

..  code-block:: text

    [lool]
    enabled = true # set to false to disable
    filter  = lool
    port    =  http,https # Change this to https if you aren't using http
    logpath = /var/log/lool/lool.log
    ignoreip = 10.0.2.1/24 # Change/Delete this if you want to Ignore one or more IP's

Save and close. Then, restart fail2ban.

..  code-block:: bash

    $ sudo systemctl restart fail2ban

`SOURCE: Setup Fail2Ban with Owncloud (TechKnight) <https://techknight.eu/2015/07/25/setup-fail2ban-with-owncloud-8-1-0/>`_

Connecting Nextcloud
--------------------------------

You can integrate with any Nextcloud instance, assuming that instance runs on
a domain given permissions with the ``-e`` on the command you used to start
the Collabora Office container.

On the Nextcloud instance you want to connect to Collabora, click the menu,
and go to :guilabel:`Apps --> Office & text`. Download and enable the
"Collabora Online" addon.

Then, go to :guilabel:`Settings` and under the :guilabel:`Administration`
section, go to :guilabel:`Collabora Online`.

Select :guilabel:`Use your own server` and set the URL as
:code:`https://lool.mousepawmedia.com/`. Do *not* disable certificate
verification. Click :guilabel:`Save`.

After saving, you should then see a green checkmark and a message that
says "Collabora Online server is reachable."

`SOURCE: Getting started in 3 steps (Nextcloud) <https://nextcloud.com/collaboraonline/>`_

Cleanup
---------------------------

Periodically you will get a warning that the server is out of disk space.
When this happens, run the following commands:

..  code-block:: bash

    $ sudo docker ps
    # Find the processid from the preceeding.
    $ sudo docker stop processid
    $ sudo docker rm processid
    $ sudo docker system prune -a --force
    $ find /tmp -ctime +10 -exec rm -rf {} +
    $ sudo docker pull collabora/code
    $ sudo docker run -t -d -p 127.0.0.1:9980:9980 -e 'domain=cloud\\.mousepawmedia\\.com|cloud\\.ajcharlesonpublishing\\.com|cloud\\.codemouse92\\.com|cloud\\.bughunters\\.cafe' -e 'username=admin' -e 'password=CollaboraPassword' --restart always --cap-add MKNOD collabora/code
