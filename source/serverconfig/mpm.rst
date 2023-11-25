Server Configuration: MPM Server
##############################################

This documentation outlines how the Linode "mpm" server was built.

Initial Setup
==============================================

We are using a Linode 8 GB with an Ubuntu 22.04 disk instance deployed
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

    echo "mpm" > /etc/hostname
    hostname -F /etc/hostname
    vim /etc/hosts

In that file, add the following lines below the first line, substituting the
actual IPv4 and IPv6 of the Linode in place of the NNN.NNN values:

```
NNN.NNN.NNN.NNN mpm
NNNN:NNNN::NNNN:NNNN:NNNN:NNNN mpm
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

The necessary packages for SSH were installed on installation. We need to
change a few settings and start the service.

..  code-block:: bash

    # Make a backup of the default SSH configuration.
    sudo cp /etc/ssh/sshd_config{,.bak}

    # Edit the SSH configuration.
    sudo vim /etc/ssh/sshd_config

Change ``PermitRootLogin yes`` to ``PermitRootLogin no``.
Also add the line ``DebianBanner no`` (you can put it under
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

    ssh-copy-id mpm@NNN.NNN.NNN.NNN

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

Scripts
--------------------------

We need to save a number of scripts for regular use.

..  code-block:: bash

    sudo mkdir -p /opt/scripts/sys
    sudo mkdir -p /opt/scripts/root
    sudo chown root:root /opt/scripts/root
    sudo chmod 700 /opt/scripts/root

Now we add the system scripts to the path for the main user.

..  code-block:: bash

    vim ~/.bashrc

Add the following to that file:

..  code-block:: bash

    PATH=$PATH:/opt/scripts/sys

Save and close. Log in as root and repeat, only with the following instead:

..  code-block:: bash

    PATH=$PATH:/opt/scripts/sys:/opt/scripts/root

Save and close that too. You can immediately update the path for your current
login session with :code:`source ~/.bashrc`.

Apache2
======================================

We'll now set up Apache2.

..  code-block:: bash

    sudo apt install apache2
    sudo vim /etc/apache2/apache2.conf

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

    sudo vim /etc/apache2/mods-available/mpm_prefork.conf

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

    sudo a2dismod mpm_event
    sudo a2enmod mpm_prefork
    sudo systemctl restart apache2

Next, we will add our user to the ``www-data`` group, which will be
helpful for permissions.

..  code-block:: bash

    sudo usermod -aG www-data mpm

Browse to the web server using the IP or whatever address is most convenient,
and ensure the Apache2 default page is appearing.

Docker
=======================================

Some of the applications we'll install require Docker. Install according to the
`official Docker installation instructions <https://docs.docker.com/engine/install/ubuntu/>`_,
recreated below:

..  code-block:: bash

    sudo apt install ca-certificates curl gnupg
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg
    echo \
    "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt update
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

Databases
===========================================

PostgreSQL
--------------------------------------------

Many of our applications use PostgreSQL, so we'll install that:

..  code-block:: bash

    sudo apt install postgresql-14 postgresql-contrib

We need to allow connections from Docker containers to PostgreSQL. Edit the
following file:

..  code-block:: bash

    sudo vim /etc/postgresql/14/main/postgresql.conf

Find and modify the following line:

..  code-block:: text

    listen_addresses = 'localhost,172.17.0.1'

Save and close, and then edit the following file:

..  code-block:: bash

    sudo vim /etc/postgresql/14/main/pg_hba.conf

Add the following lines to the end:

..  code-block:: text

    host    all             all             172.17.0.0/16           scram-sha-256
    host    all             all             0.0.0.0/0               md5

Save and close, and then restart PostgreSQL.

..  code-block:: bash

    sudo systemctl restart postgresql
    sudo ufw allow 5432

MySQL
-------------------------------------------

Now we will set up MySQL.

..  code-block:: bash

    sudo apt install mysql-server
    sudo mysql_secure_installation

Validate Password is optional; you should specify the root password
and answer ``Y`` to the following:

* Remove anonymous users?
* Disallow root login remotely?
* Remove test database and access to it?
* Reload privilege tables now?

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
and enable it.

..  code-block:: bash

    sudo ufw allow 22
    sudo ufw allow 80
    sudo ufw allow 443
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

Installing Apache2
======================================

We'll now set up Apache2.

..  code-block:: bash

    sudo apt install apache2
    sudo vim /etc/apache2/apache2.conf

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

    sudo vim /etc/apache2/mods-available/mpm_prefork.conf

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

    sudo a2dismod mpm_event
    sudo a2enmod mpm_prefork
    sudo systemctl restart apache2

Next, we will add our user to the ``www-data`` group, which will be
helpful for permissions.

..  code-block:: bash

    sudo usermod -aG www-data mpm

Browse to the web server using the IP or whatever address is most convenient,
and ensure the Apache2 default page is appearing.
    net.ipv4.conf.default.accept_source_route = 0
    net.ipv6.conf.default.accept_source_route = 0

    # Do not send ICMP redirects (we are not a router)
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

    # Do not accept ICMP redirects (prevent MITM attacks)
    net.ipv4.conf.all.accept_redirects = 0
    net.ipv6.conf.all.accept_redirects = 0
    net.ipv4.conf.default.accept_redirects = 0
    net.ipv6.conf.default.accept_redirects = 0

    # Ignore Directed pings
    net.ipv4.icmp_echo_ignore_all = 1

Finally, reload ``sysctl``. If there are any errors, fix the associated lines.

..  code-block:: bash

    sudo sysctl -p

Harden Apache2
---------------------------------------------

Edit the Apache2 security configuration file...

..  code-block:: bash

    sudo vim /etc/apache2/conf-available/security.conf

Change or add the following lines:

..  code-block:: text

    ServerTokens Prod
    ServerSignature Off
    TraceEnable Off
    FileETag None

Restart the Apache2 server and make sure it still works.

..  code-block:: bash

    sudo systemctl restart apache2

Setup ModSecurity
---------------------------------------------------

First, install the necessary dependencies. We'll also need to create a
symbolic link to work around a bug on 64-bit systems. Finally, we'll install
the package itself.

..  code-block:: bash

    sudo apt install libxml2 libxml2-dev libxml2-utils libaprutil1 libaprutil1-dev apt-transport-https lsb-release ca-certificates curl
    sudo ln -s /usr/lib/x86_64-linux-gnu/libxml2.so.2 /usr/lib/libxml2.so.2
    sudo wget -qO - https://modsecurity.digitalwave.hu/archive.key | sudo apt-key add -
    sudo sh -c 'echo "deb http://modsecurity.digitalwave.hu/ubuntu/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/dwmodsec.list'
    sudo sh -c 'echo "deb http://modsecurity.digitalwave.hu/ubuntu/ $(lsb_release -sc)-backports main" >> /etc/apt/sources.list.d/dwmodsec.list'
    cat << EOF | sudo tee -a /etc/apt/preferences.d/99modsecurity
    Package: *libapache2-mod-security2*
    Pin: origin modsecurity.digitalwave.hu
    Pin-Priority: 900

    Package: *modsecurity-crs*
    Pin: origin modsecurity.digitalwave.hu
    Pin-Priority: 900

    Package: *libmodsecurity*
    Pin: origin modsecurity.digitalwave.hu
    Pin-Priority: 900
    EOF
    sudo apt install libapache2-mod-security2

Now we'll copy the default configuration.

..  code-block:: bash

    sudo mv /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf

Now we download the latest OWASP security rules.

..  code-block:: bash

    cd /etc/modsecurity
    sudo wget https://github.com/coreruleset/coreruleset/archive/v3.3.5.tar.gz
    sudo tar -xvf v3.3.5.tar.gz
    sudo mv coreruleset-3.3.5 owasp-modsecurity-crs
    cd owasp-modsecurity-crs
    sudo mv crs-setup.conf.example crs-setup.conf
    cd rules
    sudo mv REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf
    sudo mv RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf.example RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf

You may need to edit :file:`/etc/modsecurity/owasp-modsecurity-crs/crs-setup.conf`
to match your server's situation. For example, we enabled Project Honeypot.

Edit the configuration for the ModSecurity Apache module...

..  code-block:: bash

    sudo vim /etc/apache2/mods-available/security2.conf

Change the ``IncludeOptional`` entries to match the following:

..  code-block:: apache

    IncludeOptional /etc/modsecurity/*.conf
    IncludeOptional /etc/modsecurity/owasp-modsecurity-crs/crs-setup.conf

    IncludeOptional /etc/modsecurity/owasp-modsecurity-crs/plugins/*-before.conf
    IncludeOptional /etc/modsecurity/owasp-modsecurity-crs/rules/*.conf
    IncludeOptional /etc/modsecurity/owasp-modsecurity-crs/plugins/*-after.conf

Enable the modules and restart Apache2, ensuring that it still works.

..  code-block:: bash

    sudo a2enmod headers security2
    sudo systemctl restart apache2

Finally, to make sure it works, go to ``http://<serveraddress>/?param="><script>alert(1);</script>``.
Check ``/var/log/apache2/error.log`` for an error report from ``mod_security``.
If one is there, the configuration worked!

Setup Fail2Ban
-----------------------------------------------

Fail2Ban locks out IP addresses that repeatedly attempt invalid or malicious
actions.

..  code-block:: bash

    sudo apt install fail2ban
    sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
    sudo vim /etc/fail2ban/jail.local

To turn on various "jails", scroll down to the ``# JAILS`` section. Place
``enabled = true`` under each jail name you want turned on. This is the list
of jails we enabled for this server:

- sshd
- apache-auth
- apache-badbots
- apache-noscript
- apache-overflows
- apache-nohome
- apache-botsearch
- apache-fakegooglebot
- apache-modsecurity
- apache-shellshock
- php-url-fopen
- mysqld-auth
- recidive
- gitlab
- phpmyadmin-syslog

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

    sudo cp /etc/fail2ban/fail2ban.conf /etc/fail2ban/fail2ban.local
    sudo vim /etc/fail2ban/fail2ban.local

Change the following values:

..  code-block:: text

    # NEVER SET TO DEBUG!!! [recidive] jail is enabled
    loglevel = INFO

    dbpurgeage = 648000

Save and close. Run the following command to ensure there are no errors:

..  code-block:: bash

    sudo systemctl stop fail2ban
    sudo fail2ban-client -x start

Finally, restart the fail2ban process.

..  code-block:: bash

    sudo systemctl restart fail2ban

Setup PSAD
------------------------------------------

..  code-block:: bash

    sudo apt install psad
    sudo iptables -A INPUT -j LOG
    sudo iptables -A FORWARD -j LOG
    sudo ip6tables -A INPUT -j LOG
    sudo ip6tables -A FORWARD -j LOG
    sudo vim /etc/psad/psad.conf

Change the following values:

..  code-block:: text

    EMAIL_ADDRESS mpm@localhost;
    HOSTNAME mpm;
    ALERTING_METHODS noemail;
    EMAIL_THROTTLE 100;
    ALERT_ALL N;
    ENABLE_AUTO_IDS_EMAIL N;
    EMABLE_DNS_LOOKUPS N;
    ENABLE_WHOIS_LOOKUPS N;

Save and close, and then reload like this:

..  code-block:: bash

    sudo psad -R; sudo psad --sig-update; sudo psad -H; sudo psad --Status

When you run that last command, it may whine about not finding a pidfile.
It appears we can ignore that error.

We also need to tweak Fail2Ban so that it doesn't start up before ``psad`` does.
Otherwise, ``psad`` won't be able to log correctly.

..  code-block:: bash

    sudo vim /lib/systemd/system/fail2ban.service

In that file, add ``ufw.service`` and ``psad.service`` to the ``After=`` directive,
so it looks something like this:

..  code-block:: text

    After=network.target iptables.service firewalld.service ufw.service psad.service

Save and close, and then reload the daemons for systemctl and restart fail2ban.

..  code-block:: bash

    sudo systemctl daemon-reload
    sudo systemctl restart fail2ban

Now we need to adjust the UFW settings.

..  code-block:: bash

    sudo ufw logging high
    sudo vim /etc/ufw/before.rules

Add the following lines before the final commit message:

..  code-block:: text

    -A INPUT -j LOG
    -A FORWARD -j LOG

Save and close. Repeat this with ``before6.rules``. Then, restart ufw and
reload PSAD.

`SOURCE: PSAD Is Giving a Firewall Setup Warning (Ubuntu Forums) <https://ubuntuforums.org/showthread.php?t=2047977>`_

..  code-block:: bash

    sudo systemctl restart ufw
    sudo psad --fw-analyze

Restart the computer, and ensure PSAD isn't sending any system emails
complaining about the firewall configuration. (Check system email by
running ``$ mail``).

Rootkit Checks
--------------------------------------------

We use two different rootkit checkers.

..  code-block:: bash

    sudo apt install rkhunter chkrootkit
    sudo vim /opt/scripts/root/rootkitscan

Set the contents of that file to the following:

..  code-block:: bash

    #!/bin/bash
    chkrootkit
    rkhunter --update
    rkhunter --propupd
    rkhunter --check --cronjob -l
    echo "Rootkit Check Done!"

Save and close, and then run...

..  code-block:: bash

    sudo chmod +x /opt/scripts/root/rootkitscan

Let's Encrypt Certbot
===============================================

We need to install the Let's Encrypt Certbot for generating certificates.

..  code-block:: bash

    sudo snap install core; sudo snap refresh core
    sudo snap install --classic certbot
    sudo ln -s /snap/bin/certbot /usr/bin/certbot
    sudo certbot register

Follow the instructions to register with Let's Encrypt.

We'll actually generate certificates in a later step.

`SOURCE: Certbot (Certbot) <https://certbot.eff.org/lets-encrypt/ubuntufocal-apache>`_

Scheduling Auto-Renewal
-----------------------------------

Now we need to schedule the autorenewal task.

..  code-block:: bash

    sudo crontab -e

Add the following line to the end:

..  code-block:: text

    41 5 * * * /usr/bin/certbot renew

This will run the renewal script once a day at 5:41am. (Let's Encrypt asks
that a random time be used by each user, to spread out server load.)

GitLab Omnibus
====================================

We will install GitLab using the GitLab Omnibus package for Ubuntu.

DNS Setup
-------------------------------------

Start by setting up your DNS, adding the following A/AAAA records, and associating
them with this Linode instance's IP addresses.

* gitlab
* pages
* registry

SSL Certificates
---------------------------------

Run the following to create the certificates for GitLab, changing the certificate URLs as appropriate:

..  code-block:: bash

    sudo a2ensite 000-default
    sudo systemctl restart apache2
    sudo certbot certonly --apache -d gitlab.mousepawmedia.com -d pages.mousepawmedia.com -d registry.mousepawmedia.com

Take note of the certificate paths, such as `/etc/letsencrypt/live/gitlab.mousepawmedia.com/fullchain.pem`.

Apache2 Configuration
---------------------------------

Edit the following file to configure GitLab to work with Apache2.

..  code-block:: bash

    sudo vim /etc/apache2/sites-available/gitlab-omnibus.conf

The following Apache configuration is file based on the Apache 2.4 configuration
for GitLab Omnibus provided by the
`Gitlab recipes repository <https://gitlab.com/gitlab-org/gitlab-recipes/-/blob/master/web-server/apache/gitlab-omnibus-ssl-apache24.conf>`_.

..  code-block:: apache

    <VirtualHost gitlab.mousepawmedia.com:80>
        ServerName gitlab.mousepawmedia.com
        ServerSignature Off

        RewriteEngine on
        RewriteCond %{HTTPS} !=on
        RewriteRule .* https://%{SERVER_NAME}%{REQUEST_URI} [NE,R,L]
    </VirtualHost>

    <VirtualHost gitlab.mousepawmedia.com:443>
        SSLEngine on
        #strong encryption ciphers only
        #see ciphers(1) http://www.openssl.org/docs/apps/ciphers.html
        SSLProtocol all -SSLv2
        SSLHonorCipherOrder on
        SSLCipherSuite "ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:ECDH+3DES:DH+3DES:RSA+AESGCM:RSA+AES:RSA+3DES:!aNULL:!MD5:!DSS"
        Header add Strict-Transport-Security: "max-age=15768000;includeSubdomains"
        SSLCompression Off
        SSLCertificateFile /etc/letsencrypt/live/gitlab.mousepawmedia.com/fullchain.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/gitlab.mousepawmedia.com/privkey.pem

        ServerName gitlab.mousepawmedia.com
        ServerSignature Off

        ProxyPreserveHost On

        # Ensure that encoded slashes are not decoded but left in their encoded state.
        # http://doc.gitlab.com/ce/api/projects.html#get-single-project
        AllowEncodedSlashes NoDecode

        <Location />
            # New authorization commands for apache 2.4 and up
            # http://httpd.apache.org/docs/2.4/upgrading.html#access
            Require all granted

            #Allow forwarding to gitlab-workhorse
            ProxyPassReverse http://127.0.0.1:8181
            ProxyPassReverse http://YOUR_SERVER_FQDN/
        </Location>

        # Apache equivalent of nginx try files
        # http://serverfault.com/questions/290784/what-is-apaches-equivalent-of-nginxs-try-files
        # http://stackoverflow.com/questions/10954516/apache2-proxypass-for-rails-app-gitlab
        RewriteEngine on

        # Enable WebSocket reverse Proxy
        # Needs proxy_wstunnel enabled
        RewriteCond %{HTTP:Upgrade} websocket [NC]
        RewriteCond %{HTTP:Connection} upgrade [NC]
        RewriteRule ^/?(.*) "ws://127.0.0.1:8181/$1" [P,L]

        RewriteCond %{REQUEST_URI} ^/api/v\d+/.* [OR]
        RewriteCond %{REQUEST_URI} .*-/branches/.* [OR]
        RewriteCond %{REQUEST_URI} .*-/refs/.*/logs_tree/.* [OR]

        RewriteCond %{REQUEST_URI} .*-/tree/.*
        RewriteRule .* http://127.0.0.1:8181%{REQUEST_URI} [P,QSA,NE]

        #Forward all requests to gitlab-workhorse except existing files like error documents
        RewriteCond %{DOCUMENT_ROOT}/%{REQUEST_FILENAME} !-f [OR]
        RewriteCond %{REQUEST_URI} ^/uploads/.*

        # Remove "NE" flag to allow special characters (spaces) in path
        RewriteRule .* http://127.0.0.1:8181%{REQUEST_URI} [P,QSA]

        RequestHeader set X_FORWARDED_PROTO 'https'
        RequestHeader set X-Forwarded-Ssl on

        # needed for downloading attachments
        DocumentRoot /opt/gitlab/embedded/service/gitlab-rails/public

        #Set up apache error documents, if back end goes down (i.e. 503 error) then a maintenance/deploy page is thrown up.
        ErrorDocument 404 /404.html
        ErrorDocument 422 /422.html
        ErrorDocument 500 /500.html
        ErrorDocument 502 /502.html
        ErrorDocument 503 /503.html

        LogFormat "%{X-Forwarded-For}i %l %u %t \"%r\" %>s %b" common_forwarded

        ErrorLog /var/log/apache2/gitlab.mousepawmedia.com_error.log
        CustomLog /var/log/apache2/gitlab.mousepawmedia.com_forwarded.log common_forwarded
        CustomLog /var/log/apache2/gitlab.mousepawmedia.com_access.log combined env=!dontlog
        CustomLog /var/log/apache2/gitlab.mousepawmedia.com.log combined

    </VirtualHost>

    # GitLab Container Registry Section
    # https://gitlab.com/gitlab-org/gitlab-recipes/issues/50

    # Prereq: port 4567 needs to be open in firewalls and SELinux.
    # See common:tasks/Gitlab.yml for relevant tasks.

    Listen 4567
    NameVirtualHost *:4567

    <VirtualHost *:4567>
        ServerName registry.mousepawmedia.com

        ServerSignature Off

        SSLEngine On
        SSLProtocol ALL -SSLv2 -SSLv3
        SSLHonorCipherOrder On
        SSLCipherSuite ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:ECDH+3DES:DH+3DES:RSA+AESGCM:RSA+AES:RSA+3DES:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!3DES:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA
        Header add Strict-Transport-Security: "max-age=15768000;includeSubdomains"

        SSLCertificateFile /etc/letsencrypt/live/gitlab.mousepawmedia.com/fullchain.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/gitlab.mousepawmedia.com/privkey.pem

        Header set Host "registry.mousepawmedia.com:4567"

        RequestHeader set X-Forwarded-Proto "https"

        ProxyRequests Off
        ProxyPreserveHost On
        TimeOut 900

        # Timeout didn't resolve issue with Docker container push, so
        # added RequestReadTimeout 2019-07-30
        RequestReadTimeout header=20-40,MinRate=500 body=20,MinRate=500

        <Location />
            Order deny,allow
            Allow from all

            ProxyPass http://localhost:5000/ timeout=900
            ProxyPassReverse http://localhost:5000/
        </Location>

        ErrorLog /var/log/apache2/registry.mousepawmedia.com_error.log
        LogLevel warn
        CustomLog /var/log/apache2/registry.mousepawmedia.com_access.log combined
    </VirtualHost>

Save and close. Then, enable the needed Apache2 modules and site.

..  code-block:: bash

    sudo a2enmod rewrite ssl proxy proxy_http proxy_wstunnel headers
    sudo a2ensite gitlab-omnibus
    sudo systemctl restart apache2

PostgreSQL
-------------------------------------

Rather than use the bundled PostgreSQL server in the GitLab instance, we need to
use a separately managed database. This enables us to easily administer it for this
and other applications.

Connect to the PostgreSQL instance and install the required extensions, like this:

..  code-block:: bash

    sudo su - postgres
    psql

..  code-block:: sql

    CREATE EXTENSION IF NOT EXISTS pg_trgm;
    CREATE EXTENSION IF NOT EXISTS btree_gist;
    CREATE EXTENSION IF NOT EXISTS plpgsql;
    \dx

Running the last command should show all three extensions enabled. If so, set up
the necessary users and databases.

..  code-block:: sql

    CREATE ROLE gitlab WITH LOGIN PASSWORD 'password';
    ALTER USER gitlab CREATEDB;
    CREATE DATABASE gitlabhq_production OWNER gitlab;
    \q

You can now `exit` out of the `postgres` shell session as well.

Installing GitLab
========================================

Now we install the dependencies and the official GitLab Omnibus package.

..  code-block:: bash

    sudo apt install -y curl openssh-server ca-certificates tzdata perl
    curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
    sudo apt update
    sudo apt install gitlab-ee

..  note:: Although we're installing Enterprise Edition (EE), without a license, it will
    still operate as the free Community Edition (CE).

Whatever you picked as your primary domain and subdomain for GitLab, you'll need
to make GitLab aware of it. Modify the following file:

..  code-block:: bash

    sudo vim /etc/gitlab/gitlab.rb

Change the following lines, uncommenting as necessary. Because we need to install
additional services beyond what is included with the GitLab Omnibus installer,
we need to switch away from the built-in NGINX server to our system Apache2
instance, and from the internal PostgreSQL instance to the system instance.

Change the URLs and `PASSWORD` instances as appropriate for your use cases.

..  code-block:: ruby

    external_url 'https://gitlab.mousepawmedia.com'

    gitlab_rails['allowed_hosts'] = ['gitlab.mousepawmedia.com', '127.0.0.1', 'localhost']

    letsencrypt['enable'] = false

    pages_external_url "https://pages.mousepawmedia.com/"

    registry_external_url 'https://registry.mousepawmedia.com'

    nginx['enable'] = false

    web_server['external_users'] = ['www-data']

    gitlab_workhorse['listen_network'] = "tcp"
    gitlab_workhorse['listen_addr'] = "127.0.0.1:8181"

    postgresql['enable'] = false

    gitlab_rails['db_adapter'] = "postgresql"
    gitlab_rails['db_encoding'] = "unicode"
    gitlab_rails['db_database'] = "gitlabhq_production"
    gitlab_rails['db_username'] = "gitlab"
    gitlab_rails['db_password'] = "PASSWORD"
    gitlab_rails['db_host'] = '127.0.0.1'
    gitlab_rails['db_port'] = 5432

    mattermost['enable'] = false

Save and close, and then run...

..  code-block:: bash

    sudo gitlab-ctl reconfigure

This will take several minutes to run, so sit back and enjoy the view.

When it's finished, run the following command to get the initial root password:

..  code-block:: bash

    sudo cat /etc/gitlab/initial_root_password

Log into your GitLab instance with the username `root` and that password. Be sure
to *immediately* change the password.

S3 Bucket Storage
----------------------------------------

We want GitLab to store its artifacts and other files in our Object Storage.

In Linode, set up Object Storage with the following buckets:

* mpm-gitlab-artifacts
* mpm-gitlab-external-diffs
* mpm-gitlab-lfs
* mpm-gitlab-uploads
* mpm-gitlab-packages
* mpm-gitlab-dependency-proxy
* mpm-gitlab-terraform-state
* mpm-gitlab-ci-secure-files
* mpm-gitlab-pages

Create an Access Key called `mpm-gitlab` with Limited Access, and give it
Read/Write Access to each of the buckets above, but nothing else. Ensure you
save the Access Key and Secret Key, and provide them in the next step where
indicated in place of `ACCESS_KEY` and `SECRET_KEY` respectively.

Edit the following file:

..  code-block:: bash

    sudo vim /etc/gitlab/gitlab.rb

Change the following lines, uncommenting as necessary.

..  code-block:: ruby

    gitlab_rails['object_store']['enabled'] = true
    gitlab_rails['object_store']['connection'] = {
        'provider' => 'AWS',
        'endpoint' => 'https://us-east-1.linodeobjects.com',
        'path_style' => false,
        'region' => 'us-east-1',
        'aws_access_key_id' => 'ACCESS_KEY',
        'aws_secret_access_key' => 'SECRET_KEY'
    }
    gitlab_rails['object_store']['proxy_download'] = true
    gitlab_rails['object_store']['objects']['artifacts']['bucket'] = 'mpm-gitlab-artifacts'
    gitlab_rails['object_store']['objects']['external_diffs']['bucket'] = 'mpm-gitlab-external-diffs'
    gitlab_rails['object_store']['objects']['lfs']['bucket'] = 'mpm-gitlab-lfs'
    gitlab_rails['object_store']['objects']['uploads']['bucket'] = 'mpm-gitlab-uploads'
    gitlab_rails['object_store']['objects']['packages']['bucket'] = 'mpm-gitlab-packages'
    gitlab_rails['object_store']['objects']['dependency_proxy']['bucket'] = 'mpm-gitlab-dependency-proxy'
    gitlab_rails['object_store']['objects']['terraform_state']['bucket'] = 'mpm-gitlab-terraform-state'
    gitlab_rails['object_store']['objects']['ci_secure_files']['bucket'] = 'mpm-gitlab-ci-secure-files'
    gitlab_rails['object_store']['objects']['pages']['bucket'] = 'mpm-gitlab-pages'

    gitlab_rails['external_diffs_enabled'] = true

Save and close, and then run...

..  code-block:: bash

    sudo gitlab-ctl reconfigure

STMP Settings
---------------------------

To allow GitLab to send email via our existing email servers,
edit the following file:

..  code-block:: bash

    sudo vim /etc/gitlab/gitlab.rb

Change the following lines, uncommenting as necessary.

..  code-block:: ruby

    gitlab_rails['smtp_enable'] = true
    gitlab_rails['smtp_address'] = "mail.mousepawmedia.com"
    gitlab_rails['smtp_port'] = 465
    gitlab_rails['smtp_user_name'] = "noreply@mousepawmedia.com"
    gitlab_rails['smtp_password'] = "PASSWORD"
    gitlab_rails['smtp_domain'] = "mail.mousepawmedia.com"
    gitlab_rails['smtp_authentication'] = "login"
    gitlab_rails['smtp_enable_starttls_auto'] = false
    gitlab_rails['smtp_tls'] = true
    gitlab_rails['smtp_openssl_verify_mode'] = 'none'

    gitlab_rails['gitlab_email_from'] = 'noreply@mousepawmedia.com'
    gitlab_rails['gitlab_email_display_name'] = 'MousePaw Media'
    gitlab_rails['gitlab_email_subject_suffix'] = '[GitLab]'


Save and close, and then run...

..  code-block:: bash

    sudo gitlab-ctl reconfigure

Discourse
=======================================

Domain and Certificates
---------------------------------------

We first set up the domain name and the certificates. For our configuration, we used `discourse.mousepawmedia.com`.

Run the following to create the certificates for GitLab, changing the certificate URLs as appropriate:

..  code-block:: bash

    sudo a2ensite 000-default
    sudo systemctl restart apache2
    sudo certbot certonly --apache -d discourse.mousepawmedia.com

Database
----------------------------------------

Connect to the PostgreSQL instance like this:

..  code-block:: bash

    sudo su - postgres
    psql

Now set up the necessary users and databases.

..  code-block:: sql

    CREATE ROLE discourse WITH LOGIN PASSWORD 'password';
    CREATE DATABASE discourse_production OWNER discourse;
    \q

You can now `exit` out of the `postgres` shell session as well.

Installing Discourse
-------------------------------------

To set up Discouse, run the following:

..  code-block:: bash

    sudo git clone https://github.com/discourse/discourse_docker.git /var/discourse
    cd /var/discourse/
    sudo chmod 700 containers
    sudo cp /var/discourse/samples/web_only.yml /var/discourse/containers/app.yml
    sudo vim /var/discourse/containers/app.yml

That last command will open a file for editing.

Check that the following lines are commented, uncommented, or changed as shown,
substiting values as needed. Lines not included below should be left unchanged
in the file, unless you know what you're doing.

..  code-block:: yaml
    templates:
        - "templates/redis.template.yml"
        - "templates/web.template.yml"
        - "templates/web.socketed.template.yml"
        ## Uncomment the next line to enable the IPv6 listener
        #- "templates/web.ipv6.template.yml"
        - "templates/web.ratelimited.template.yml"
        ## Uncomment these two lines if you wish to add Lets Encrypt (https)
        #- "templates/web.ssl.template.yml"
        #- "templates/web.letsencrypt.ssl.template.yml"

        ## which TCP/IP ports should this container expose?
        ## If you want Discourse to share a port with another webserver like Apache or nginx,
        ## see https://meta.discourse.org/t/17247 for details
    expose:
        - "9080:80"   # http
        - "9443:443" # https

    env:
        DISCOURSE_HOSTNAME: 'discourse.mousepawmedia.com'
        DOCKER_USE_HOSTNAME: true
        DISCOURSE_DEVELOPER_EMAILS: 'it@mousepawmedia.com'
        DISCOURSE_SMTP_ADDRESS: mail.mousepawmedia.com
        DISCOURSE_SMTP_PORT: 587
        DISCOURSE_SMTP_USER_NAME: noreply@mousepawmedia.com
        DISCOURSE_SMTP_PASSWORD: <PASSWORD>
        DISCOURSE_SMTP_ENABLE_START_TLS: true
        DISCOURSE_NOTIFICATION_EMAIL: noreply@mousepawmedia.com

        DISCOURSE_DB_USERNAME: discourse
        DISCOURSE_DB_PASSWORD: <PASSWORD>
        DISCOURSE_DB_NAME: discourse_production
        DISCOURSE_DB_HOST: 172.17.0.1
        DISCOURSE_DB_PORT: 5432

        DISCOURSE_USE_S3: true
        DISCOURSE_S3_REGION: us-east-1
        DISCOURSE_S3_HTTP_CONTINUE_TIMEOUT: 0
        DISCOURSE_S3_ENDPOINT: https://us-east-1.linodeobjects.com
        DISCOURSE_S3_ACCESS_KEY_ID: <ACCESS KEY>
        DISCOURSE_S3_SECRET_ACCESS_KEY: <SECRET KEY>
        DISCOURSE_S3_BUCKET: mpm-discourse
        DISCOURSE_S3_BACKUP_BUCKET: mpm-discourse-backup
        DISCOURSE_BACKUP_LOCATION: s3
        # A CDN is STRONGLY encouraged, but we have to skip it for now.

    links:
        #  - link:
        #    name: data
        #    alias: data

    hooks:
        after_code:
            - exec:
                cd: $home/plugins
                cmd:
                    - git clone https://github.com/discourse/docker_manager.git
                    - git clone https://github.com/discourse/discourse-oauth2-basic
                    - git clone https://github.com/paviliondev/discourse-custom-wizard
                    - git clone https://github.com/discourse/discourse-solved
                    - git clone https://github.com/discourse/discourse-docs
                    - git clone https://github.com/discourse/discourse-checklist
                    - git clone https://github.com/discourse/discourse-user-notes
                    - git clone https://github.com/discourse/discourse-assign
                    - git clone https://github.com/discourse/discourse-policy
                    - git clone https://github.com/discourse/discourse-templates
                    - git clone https://github.com/discourse/discourse-math
                    - git clone https://github.com/discourse/discourse-reactions
                    - git clone https://github.com/discourse/discourse-footnote
                    - git clone https://github.com/discourse/discourse-automation
                    - git clone https://github.com/graydenshand/discourse-quiz
            #- exec: awk -F\# '{print $1;}' ~/.ssh/authorized_keys | awk 'BEGIN { print "Authorized SSH keys for this container:"; } NF>=2 {print $NF;}'

You will notice that I am adding several plugins, and also disabling a
line of code from the *very* end of the file that will typically fail.

You can change which plugins to install, but you MUST install the first two!

..  note:: Before I could rebuild the container, I had to temporarily stop GitLab with :code:`sudo gitlab-ctl stop`,
    otherwise I didn't have enough free memory for the Docker build.

Save and close, and then run...

..  code-block:: bash

    sudo /var/discourse/launcher rebuild app
    sudo chmod o-rwx containers/app.yml


`SOURCE: Run other websites on the same machine as Discourse <https://meta.discourse.org/t/run-other-websites-on-the-same-machine-as-discourse/17247>`_

Apache2 Configuration
------------------------------------

Now we create an Apache2 reverse proxy for Discourse.

..  code-block:: bash

    sudo vim /etc/apache2/sites-available/discourse.conf

Set the file to the following contents:

..  code-block:: apache

    <VirtualHost discourse.mousepawmedia.com:80>
        ServerName discourse.mousepawmedia.com
        ServerSignature Off

        RewriteEngine on
        RewriteCond %{HTTPS} !=on
        RewriteRule .* https://%{SERVER_NAME}%{REQUEST_URI} [NE,R,L]
    </VirtualHost>

    <VirtualHost *:443>
        ServerName discourse.mousepawmedia.com
        ServerSignature Off

        SSLEngine On
        SSLProtocol TLSv1 TLSv1.1 TLSv1.2
        SSLProxyEngine on
        SSLHonorCipherOrder On
        SSLCipherSuite "EECDH+ECDSA+AESGCM:EECDH+aRSA+AESGCM:EECDH+ECDSA+SHA256:EECDH+aRSA+SHA256:EECDH+ECDSA+SHA384:EECDH+ECDSA+SHA256:EECDH+aRSA+SHA384:EDH+aRSA+AESGCM:EDH+aRSA+SHA256:EDH+aRSA:EECDH:!aNULL:!eNULL:!MEDIUM:!LOW:!3DES:!MD5:!EXP:!PSK:!SRP:!DSS:!RC4:!SEED"
        Header set Strict-Transport-Security "max-age=31536000"

        SSLCertificateFile /etc/letsencrypt/live/discourse.mousepawmedia.com/fullchain.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/discourse.mousepawmedia.com/privkey.pem

        ProxyPreserveHost On
        ProxyRequests Off
        RequestHeader set X-Forwarded-Proto "https"
        RequestHeader set X-Real-IP expr=%{REMOTE_ADDR}
        RequestHeader set X-Forwarded-Port "443"

        <Location />
            ProxyPass unix:/var/discourse/shared/web-only/nginx.http.sock|http://localhost/
            ProxyPassReverse unix:/var/discourse/shared/web-only/nginx.http.sock|http://localhost/
        </Location>

    </VirtualHost>

Save and close, and enable the new site.

..  code-block:: bash

    sudo a2ensite discourse
    sudo systemctl reload apache2

Now you should be able to browse to the URL configured for your Discourse
instance and create an account.

Discourse Configuration
-------------------------------------

Once you've successfully signed into Discourse, you need to set up
GitLab authentication with OAuth2.

In GitLab, under Admin -> Applications, create a new OAuth2 application
with the following settings:

* Name: :code:`Discourse`
* Redirect URI: :code:`https://discourse.mousepawmedia.com/auth/oauth2_basic/callback`
* Trusted: Yes
* Confidential: Yes
* Scopes: :code:`read_user`

Click :guilabel:`Save Application`, and take note of the application ID
and the secret. You'll need both in the next step.

In Discourse, go to Admin -> Settings -> Login. Set the following settings:

* invite only: no
* must approve users: no
* allow new registrations: yes
* enable signup cta: yes
* oauth2 enabled: yes
* oauth2 client id: (Use application ID from prior step)
* oauth2 client secret: (Use secret from prior step)
* oauth2 authorize url: :code:`https://gitlab.mousepawmedia.com/oauth/authorize`
* oauth2 authorize signup url: (blank)
* oauth2 token url: :code:`https://gitlab.mousepawmedia.com/oauth/token`
* oauth2 token url method: :code:`POST`
* oauth2 fetch user details: yes
* oauth2 user json url: :code:`https://gitlab.mousepawmedia.com/api/v4/user`
* oauth2 user json url method: :code:`GET`
* oauth2 json user id path: :code:`id`
* oauth2 json username path: :code:`username`
* oauth2 json name path: :code:`name`
* oauth2 json email path: :code:`email`
* oauth2 json email verified path: (blank)
* oauth2 json avatar path: :code:`avatar_url`
* oauth2 email verified: yes
* oauth2 overrides email: yes
* oauth2 send auth header: yes
* oauth2 send auth body: yes
* oauth2 authorize options: :code:`scope`
* oauth2 scope: :code:`read_user`
* oauth2 button title: :code:`MousePaw Media GitLab`
* oauth2 allow association change: false

Save each setting as you go. You may choose to change
:code:`oauth2 button title` to what is relevant for you.

Confirm registration and login with GitLab (NOT username/password) works on
Discourse. Ensure you can login to your Discourse root/admin account with the
GitLab root/admin account.

..  note:: You MUST ensure you can log into a Discourse admin account with a
    GitLab admin account! Otherwise, you will lock yourself out of admin
    in the next step.

Once you've confirmed GitLab OAuth2can be used as the sole authentication
method, change the following settings under Admin -> Settings -> Login:

* enable local logins: no
* enable local logins via email: no
* auth immediately: yes
* auth overrides email: yes
* auth overrides username: yes
* auth overrides name: yes

Save each setting as you go.

OrangeHRM
============================

OrangeHRM is our ECO (HR) platform.

Installation
----------------------

The latest release of OrangeHRM open source can be found on the
`OrangeHRM SourceForge <https://sourceforge.net/projects/orangehrm/>`_.
Unfortunately, they make it hard to find the direct download link, but it
can be constructed as in the :code:`wget` command below:

..  code-block:: bash

    sudo mkdir /opt/orangehrm
    cd /opt/orangehrm
    sudo wget https://downloads.sourceforge.net/project/orangehrm/stable/5.5/orangehrm-5.5.zip
    sudo unzip orangehrm-5.5.zip
    sudo chown mpm:www-data -R /opt/orangehrm

Also install the necessary dependencies:

..  code-block:: bash

    sudo apt install php8.1 php8.1-mysql php8.1-curl php8.1-xml php8.1-zip php8.1-gd php8.1-intl php8.1-ldap libapache2-mod-php8.1
    sudo a2enmod php8.1
    sudo systemctl restart apache2

Domain and Certificates
---------------------------------------

We first set up the domain name and the certificates. For our configuration, we used `discourse.mousepawmedia.com`.

The ``000-default`` Apache site is what we'll use for initially generating on
a domain name. After generating the cert, we disable that site again so the
other sites will work.

..  code-block:: bash

    sudo a2ensite 000-default
    sudo systemctl reload apache2
    sudo certbot certonly --apache -d eco.mousepawmedia.com

In the output for the certbot command, take note of the paths where the
certificate and chain were saved. You'll need that in the next step.

Apache2 Configuration
---------------------------------------

Now we configure Apache2.

..  code-block:: bash

    sudo vim /etc/apache2/sites-available/eco.conf

Set the contents of that file to the following, substituting the
domain name in place for :code:`ServerName`, and fixing the paths for
the :code:`SSLCertificateFile` and :code:`SSLCertificateKeyFile`.
Also set the :code:`DocumentRoot` to the desired directory.

..  code-block:: apache

    <VirtualHost *:80>
        ServerName eco.mousepawmedia.com
        ServerSignature Off

        RewriteEngine on
        RewriteCond %{HTTPS} !=on
        RewriteRule .* https://%{SERVER_NAME}%{REQUEST_URI} [NE,R,L]
    </VirtualHost>

    <VirtualHost *:443>
        ServerName eco.mousepawmedia.com
        ServerAdmin webmaster@mousepawmedia.com
        DocumentRoot /opt/orangehrm/orangehrm-5.5

        SSLEngine On
        SSLProtocol TLSv1 TLSv1.1 TLSv1.2
        SSLProxyEngine on
        SSLHonorCipherOrder On
        SSLCipherSuite "EECDH+ECDSA+AESGCM:EECDH+aRSA+AESGCM:EECDH+ECDSA+SHA256:EECDH+aRSA+SHA256:EECDH+ECDSA+SHA384:EECDH+ECDSA+SHA256:EECDH+aRSA+SHA384:EDH+aRSA+AESGCM:EDH+aRSA+SHA256:EDH+aRSA:EECDH:!aNULL:!eNULL:!MEDIUM:!LOW:!3DES:!MD5:!EXP:!PSK:!SRP:!DSS:!RC4:!SEED"
        Header always set Strict-Transport-Security "max-age=31536000"
        Header always set Content-Security-Policy upgrade-insecure-requests

        SSLCertificateFile /etc/letsencrypt/live/eco.mousepawmedia.com/fullchain.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/eco.mousepawmedia.com/privkey.pem

    </VirtualHost>

We need to tell Apache2 to read this directory.

..  code-block:: bash

    sudo vim /etc/apache2/apache2.conf

Scroll down to the section with all the directories, and add these entries:

..  code-block:: apache

    <Directory /opt/eco/>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

Save and close. Then restart Apache2.

..  code-block:: bash

    sudo systemctl restart apache2

Database
---------------------------------------

We need to create the user and database for OrangeHRM in MySQL.

..  code-block:: bash

    sudo mysql -u root

Run the following:

..  code-block:: text

    CREATE USER 'orangehrm'@'localhost' IDENTIFIED BY 'some_password';
    GRANT ALL PRIVILEGES ON *.* TO 'orangehrm'@'localhost' WITH GRANT OPTION;
    SET GLOBAL sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));
    \q

Type `exit` to return to the main terminal session.

Setup
---------------------------------------

Go to :code:`https://eco.<yourdomain>/` and follow the wizard.
For the database, use `localhost`, and the database, user, and password
you created in the previous step.

Nextcloud
===============================================

Domain and Certificates
---------------------------------------

We first set up the domain name and the certificates. For our configuration, we used `cloud.mousepawmedia.com`.

The ``000-default`` Apache site is what we'll use for initially generating on
a domain name. After generating the cert, we disable that site again so the
other sites will work.

..  code-block:: bash

    sudo a2ensite 000-default
    sudo systemctl reload apache2
    sudo certbot certonly --apache -d cloud.mousepawmedia.com

In the output for the certbot command, take note of the paths where the
certificate and chain were saved. You'll need that in the next step.

Installation
----------------------------

Let's install the other PHP packages we need for this. Most of these are
probably already installed, but we're putting them here to be certain.

..  code-block:: bash

    sudo apt install php8.1 php8.1-curl php8.1-gd php8.1-xml php8.1-mbstring php8.1-xml php8.1-zip php8.1-pgsql php8.1-bz2 php8.1-intl php8.1-imap php8.1-bcmath php8.1-gmp php8.1-apcu php8.1-memcached php8.1-redis php8.1-imagick ffmpeg
    sudo phpenmod bcmath gmp

Now we can install Nextcloud itself.

..  note:: While we are installing 27.1.3 below, Nextcloud may have been
    updated since. Adjust commands below according to the latest stable
    version of Nextcloud.

..  code-block:: bash

    cd /tmp
    curl -LO https://download.nextcloud.com/server/releases/nextcloud-27.1.3.tar.bz2
    curl -LO https://download.nextcloud.com/server/releases/nextcloud-27.1.3.tar.bz2.sha256
    shasum -a 256 -c nextcloud-27.1.3.tar.bz2.sha256 < nextcloud-27.1.3.tar.bz2

Ensure that last command says "OK" before continuing, as that confirms the
tarball hasn't been tampered with or spoofed.

..  code-block:: bash

    rm nextcloud-27.1.3.tar.bz2.sha256
    sudo tar -C /opt -xvjf /tmp/nextcloud-27.1.3.tar.bz2
    vim /tmp/nextcloud.sh

Set the contents of that file to...

..  code-block:: bash

    ocpath='/opt/nextcloud'
    htuser='www-data'
    htgroup='www-data'
    rootuser='root'

    printf "Creating possible missing Directories\n"
    mkdir -p $ocpath/data
    mkdir -p $ocpath/assets
    mkdir -p $ocpath/updater

    printf "chmod Files and Directories\n"
    find ${ocpath}/ -type f -print0 | xargs -0 chmod 0640
    find ${ocpath}/ -type d -print0 | xargs -0 chmod 0750
    chmod 755 ${ocpath}

    printf "chown Directories\n"
    chown -R ${rootuser}:${htgroup} ${ocpath}/
    chown -R ${htuser}:${htgroup} ${ocpath}/apps/
    chown -R ${htuser}:${htgroup} ${ocpath}/assets/
    chown -R ${htuser}:${htgroup} ${ocpath}/config/
    chown -R ${htuser}:${htgroup} ${ocpath}/data/
    chown -R ${htuser}:${htgroup} ${ocpath}/themes/
    chown -R ${htuser}:${htgroup} ${ocpath}/updater/

    chmod +x ${ocpath}/occ

    printf "chmod/chown .htaccess\n"
    if [ -f ${ocpath}/.htaccess ]
    then
    chmod 0664 ${ocpath}/.htaccess
    chown ${rootuser}:${htgroup} ${ocpath}/.htaccess
    fi
    if [ -f ${ocpath}/data/.htaccess ]
    then
    chmod 0664 ${ocpath}/data/.htaccess
    chown ${rootuser}:${htgroup} ${ocpath}/data/.htaccess
    fi

Save and close, and then run the file.

..  code-block:: bash

    sudo bash /tmp/nextcloud.sh

After that finishes, we can start configuring Apache2.

Apache2 Configuration
----------------------------

Let's create an Apache2 site configuration for Nextcloud.

..  code-block:: bash

    sudo vim /etc/apache2/sites-available/cloud.conf

Set the contents to...

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName cloud.mousepawmedia.com
            DocumentRoot /opt/nextcloud

            SSLCertificateFile /etc/letsencrypt/live/cloud.mousepawmedia.com/fullchain.pem
            SSLCertificateKeyFile /etc/letsencrypt/live/cloud.mousepawmedia.com/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf
            Header always set Strict-Transport-Security "max-age=31536000"
            Header always set Content-Security-Policy upgrade-insecure-requests

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            <Directory /opt/nextcloud>
                Options +FollowSymLinks
                AllowOverride All
                Satisfy Any

                <IfModule mod_dave.c>
                        Dav off
                </IfModule>

                SetEnv HOME /opt/nextcloud
                SetEnv HTTP_HOME /opt/nextcloud
            </Directory>

            BrowserMatch "MSIE [2-6]" \
                nokeepalive ssl-unclean-shutdown \
                downgrade-1.0 force-response-1.0
            # MSIE 7 and newer should be able to use keepalive
            BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown
        </VirtualHost>
    </IfModule>

Save and close. Now, we need to also allow access to the Nextcloud directory
in Apache2's core directory.

..  code-block:: bash

    sudo vim /etc/apache2/apache2.conf

Add the following below the other ``<Directory>`` entries...

..  code-block:: apache

    <Directory /opt/nextcloud>
        Options Indexes FollowSymLinks
        Require all granted
    </Directory>

Then, enable the site and restart Apache2.

..  code-block:: bash

    sudo a2ensite cloud
    sudo a2enmod headers
    sudo systemctl restart apache2

Database
----------------------------------------

Connect to the PostgreSQL instance like this:

..  code-block:: bash

    sudo su - postgres
    psql

Now set up the necessary users and databases.

..  code-block:: sql

    CREATE ROLE nextcloud WITH LOGIN PASSWORD 'password';
    CREATE DATABASE nextcloud OWNER nextcloud;
    \q

You can now `exit` out of the `postgres` shell session as well.

Now ensure that PHP is set up to work with MySQL.

..  code-block:: bash

    sudo phpenmod pgsql
    sudo systemctl restart apache2

Redis
----------------------------------------

We also need to install and configure Redis.

..  code-block:: bash

    sudo apt install redis-server
    sudo vim /etc/redis/redis.conf

Add or change the following lines, substituting a real password in place
of PASSWORDHERE:

..  code-block:: text

    supervised systemd

    bind 127.0.0.1 -::1

    requirepass PASSWORDHERE

Save and close. Then run:

..  code-block:: bash

    sudo systemctl restart redis-server
    redis-cli

In the interactive session that appears, enter the following commands
at the prompt (:code:`>`). Responses are displayed inline without the leading
:code:`>` below:

..  code-block:: text

    > auth PASSWORDHERE
    OK
    > ping
    PONG
    > quit

That confirms Redis is configured.

Nextcloud Configuration
------------------------------

Go to the new Nextcloud site. On the setup screen, specify an admin account.

Click ``Storage and Database``, set the Data folder to ``/opt/nextcloud/data``.
Select ``PostgreSQL`` for the database, and provide the database user, password,
and database name. The fourth field should be ``localhost:5432``.

Click ``Finish setup``.

`SOURCE How To Install and Configure Nextcloud on Ubuntu 16.04 (DigitalOcean) <https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-nextcloud-on-ubuntu-16-04>`_

Preventing Lockouts
-----------------------------

Due to a glitch in Nextcloud, we have to configure fail2ban to prevent lockouts.

..  code-block:: bash

    sudo vim /etc/fail2ban/filter.d/apache-auth.conf

Change or add the following lines:

..  code-block:: text

    # ignore intentional auth failures from nextcloud admin page
    ignoreregex = cloud/[data/.ocdata|config]

Save and close, and run...

..  code-block:: bash

    sudo systemctl restart fail2ban


PHP Configuration
----------------------------

Nextcloud recommends a few tweaks to php.ini. Run...

.. code-block:: bash

    sudo vim /etc/php/8.1/apache2/php.ini

Add or edit (or uncomment) the following lines:

..  code-block:: text

    date.timezone = America/Chicago
    memory_limit = 512M

    opcache.enable=1
    opcache.enable_cli=1
    opcache.interned_strings_buffer=8
    opcache.max_accelerated_files=10000
    opcache.memory_consumption=128
    opcache.save_comments=1
    opcache.revalidate_freq=1
    opcache.jit = 1255
    opcache.jit_buffer_size = 128M

Save and close. Now open...

..  code-block:: bash

    sudo vim /etc/php/8.1/mods-available/apcu.ini

Add the following line:

..  code-block:: text

    apc.enable_cli=1

Save and close, and restart Apache2.

..  code-block:: bash

    sudo systemctl restart apache2

Configuring Memory Caching
-----------------------------

To improve performance, we'll enable memory caching. We are using APCu and
Redis, so we need to enable this for Nextcloud.

..  code-block:: bash

    sudo vim /opt/nextcloud/config/config.php

Add the following lines, substituting your actual Redis password in place of
:code:`PASSWORDHERE`:

..  code-block:: php

    'filelocking.enabled' => true,
    'memcache.local' => '\OC\Memcache\APCu',
    'memcache.distributed' => '\OC\Memcache\Redis',
    'memcache.locking' => '\OC\Memcache\Redis',
    'redis' => array(
        'host' => 'localhost',
        'port' => 6379,
        'timeout' => 0.0,
        'password' => 'PASSWORDHERE'
    ),
    'default_phone_region' => 'US',

Save and close, and restart Apache2:

..  code-block:: bash

    sudo systemctl restart apache2

Set Up Cronjob
----------------------------

It is recommended to use Cron for background tasks. We will set this up now.

..  code-block:: bash

    sudo crontab -u www-data -e

Add the following line:

..  code-block:: text

    */15  *  *  *  * php -f /opt/nextcloud/cron.php

Save and close.

Finally, in the Nextcloud Admin pane, go to ``Basic settings`` and select the ``Cron`` option.

`SOURCE: Background Jobs Configuration (Nextcloud) <https://docs.nextcloud.com/server/10/admin_manual/configuration_server/background_jobs_configuration.html>`_

S3 Bucket Storage
--------------------------------

We'll be setting an S3 Object Storage Bucket as the primary data host.

..  note:: Do NOT enable the Encryption app; it's not compatible with S3!

In Linode, activate Object Storage and create a bucket, as well as an
access key.

Then edit the Nextcloud configuration:

..  code-block:: bash

    sudo vim /opt/nextcloud/config/config.php

Add the following to that file, before the closing ``);``, substituting your
values (especially in place of :code:`ACCESSKEY` and :code:`SECRETKEY`):

..  code-block:: php

    'objectstore' => array(
        'class' => '\\OC\\Files\\ObjectStore\\S3',
        'arguments' => array(
            'bucket' => 'mpm-nextcloud',
            'autocreate' => false,
            'key' => 'ACCESSKEY',
            'secret' => 'SECRETKEY',
            'hostname' => 'us-east-1.linodeobjects.com',
            'port' => 443,
            'use_ssl' => true,
            'region' => 'us-east-1',
        ),
    ),

Save and close. Restart Apache2. Nextcloud will start storing in that
bucket instead of on the system disk.

`SOURCE: How to Configure Nextcloud to use Linode Object Storage as an External Storage Mount <https://www.linode.com/docs/guides/how-to-configure-nextcloud-to-use-linode-object-storage-as-an-external-storage-mount/>`_

`SOURCE: Deploy Nextcloud with Object Storage (Scaleway) <https://www.scaleway.com/en/docs/install-and-configure-nextcloud-object-storage/>`_

Pretty URLs
-----------------------------

The default URLs for NextCloud all include `index.php`, which isn't very
nice to look at all the time. Let's fix this.

..  code-block:: bash

    sudo vim /opt/nextcloud/config/config.php

In that file, add the following lines:

..  code-block:: php

    'overwrite.cli.url' => 'https://cloud.mousepawmedia.com/',
    'htaccess.RewriteBase' => '/',

Save and close. Then run:

..  code-block:: bash

    sudo -u www-data php /opt/nextcloud/occ maintenance:update:htaccess
    sudo systemctl restart apache2

Changing Default Files
--------------------------------

To change or remove the default files, change what is found in
``/opt/nextcloud/core/skeleton``. For our instance of Nextcloud, we've removed
all these files.

OAuth2 with GitLab
---------------------------------

We want to allow logging in with our GitLab OAuth2 provider, and provide
integration with GitLab besides. To do this, we need to configure a new
OAuth2 Application in GitLab.

In GitLab, under Admin -> Applications, create a new OAuth2 application
with the following settings:

* Name: :code:`Discourse`
* Trusted: Yes
* Confidential: Yes
* Scopes: :code:`read_user`

The Redirect URIs are:

..  code-block:: text

    https://cloud.mousepawmedia.com/apps/oidc_login/oidc
    https://cloud.mousepawmedia.com/apps/integration_gitlab/oauth-redirect

Click :guilabel:`Save Application`, and take note of the application ID
and the secret. You'll need both in the next step.

In NextCloud, install the following applications:

* OpenID Connect Login
* GitLab Integration
* Everyone Group
* Group folders

Go to the Users area, and at left, use the button :guilabel:`Add group` to
create the following groups. You MUST name them with all-lowercase and dashes,
and then rename them to uppercase-with-spaces AFTER creation. (These are all
based on the groups created in GitLab; change to fit your instance.)

* content-development
* management
* applications
* designers
* devops
* librarians
* platform
* hiring

Edit the Nextcloud configuration:

..  code-block:: bash

    sudo vim /opt/nextcloud/config/config.php

Add the following to that file, before the closing ``);``, substituting your
values (especially in place of :code:`ACCESSKEY` and :code:`SECRETKEY`):

..  code-block:: php

    'allow_user_to_change_display_name' => false,
    'lost_password_link' => 'disabled',
    'oidc_login_provider_url' => 'https://gitlab.mousepawmedia.com',
    'oidc_login_client_id' => 'ACCESSKEY',
    'oidc_login_client_secret' => 'SECRETKEY',
    'oidc_login_auto_redirect' => false,
    'oidc_login_end_session_redirect' => false,
    'oidc_login_default_quota' => '1000000000',
    'oidc_login_button_text' => 'Login with MousePaw Media GitLab',
    'oidc_login_hide_password_form' => true,
    'oidc_login_use_id_token' => false,
    'oidc_login_attributes' => array (
        'id' => 'nickname',
        'name' => 'name',
        'mail' => 'email',
        'photoURL' => 'picture',
        'groups' => 'groups',
        'is_admin' => 'groups_management',
        'login_filter' => 'groups',
    ),
    'oidc_login_filter_allowed_values' => array('staff'),
    'oidc_login_use_external_storage' => false,
    'oidc_login_scope' => 'openid read_user',
    'oidc_login_proxy_ldap' => false,
    'oidc_login_disable_registration' => false,
    'oidc_login_redir_fallback' => false,
    'oidc_login_alt_login_page' => 'assets/login.php',
    'oidc_login_tls_verify' => true,
    'oidc_create_groups' => false,
    'oidc_login_webdav_enabled' => false,
    'oidc_login_password_authentication' => false,
    'oidc_login_public_key_caching_time' => 86400,
    'oidc_login_min_time_between_jwks_requests' => 10,
    'oidc_login_well_known_caching_time' => 86400,
    'oidc_login_update_avatar' => true,
    'oidc_login_skip_proxy' => false,
    'oidc_login_code_challenge_method' => '',

Save and close. Restart Apache2.

Users are now automatically added to the Nextcloud groups that correspond to
the GitLab groups they are members of. Additionally, adding a user to the
GitLab group "Management" will grant them administration privileges on
Nextcloud.

From the Administration panel, you can select
:guilabel:`Connected Accounts` and set up GitLab integration with the
same OAuth app instance address (:code:`https://gitlab.mousepawmedia.com`),
application ID, and client secret you used above.

Once you've set all this up, you may want to create group folders from
:guilabel:`Administration` -> :guilabel:`Group folders`.

`SOURCE: Nextcloud OIDC Login -- GitHub <https://github.com/pulsejet/nextcloud-oidc-login>`
