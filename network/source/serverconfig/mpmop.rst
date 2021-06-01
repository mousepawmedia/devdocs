Server Configuration: Linode
##############################################

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

    $ echo "mpmdev" > /etc/hostname
    $ hostname -F /etc/hostname
    $ vim /etc/hosts

In that file, add the following lines below the first line, substituting the
actual IPv4 and IPv6 of the Linode in place of the NNN.NNN values:

```
NNN.NNN.NNN.NNN mpmdev
NNNN:NNNN::NNNN:NNNN:NNNN:NNNN mpmdev
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

MySQL
----------------

Now we will set up our database software.

..  code-block:: bash

    $ sudo apt install mysql-server

Because MySQL uses the ``auth_socket`` authentication plugin, it is not necessary
to set up a root password.

PHP
----------------

We'll be using PHP 7.4, which is the default in Ubuntu 20.04, for this server.

..  code-block:: bash

    $ sudo apt install php7.4 libapache2-mod-php7.4 php7.4-cli php7.4-common php7.4-curl php7.4-dev php7.4-gd php7.4-json php7.4-mbstring php7.4-mysql php7.4-opcache php7.4-readline php7.4-xml
    $ sudo vim /etc/php/7.4/apache2/php.ini

Edit the contents of that file so the following lines match the given values:

..  code-block:: text

    max_input_time = 30
    error_reporting = E_COMPILE_ERROR | E_RECOVERABLE_ERROR | E_ERROR | E_CORE_ERROR
    error_log = /var/log/php/error.log

Create the log directory for PHP, and give ownership to the Apache2 system user.
Finally, restart Apache2 to start using the changes.

..  code-block:: bash

    $ sudo mkdir /var/log/php
    $ sudo chown www-data /var/log/php
    $ sudo systemctl restart apache2

`SOURCE: Install LAMP on Ubuntu 16.04 (Linode) <https://www.linode.com/docs/web-servers/lamp/install-lamp-on-ubuntu-16-04/>`_

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

Harden PHP
---------------------------------------------

..  code-block:: bash

    $ sudo vim /etc/php/7.4/apache2/php.ini

Add or edit the following lines and save:

..  code-block:: text

    disable_functions = exec,system,shell_exec,passthru
    register_globals = Off
    expose_php = Off
    display_errors = Off
    track_errors = Off
    html_errors = Off
    magic_quotes_gpc = Off
    mail.add_x_header = Off
    session.name = NEWSESSID

Restart the Apache2 server and make sure it still works.

..  code-block:: bash

    $ sudo systemctl restart apache2

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

    $ sudo a2enmod headers
    $ sudo a2enmod security2
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
- php-url-fopen
- recidive
- slapd
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

    $ sudo cp /etc/fail2ban/fail2ban.conf /etc/fail2ban/fail2ban.local
    $ sudo vim /etc/fail2ban/fail2ban.local

Change the following values:

..  code-block:: text

    # NEVER SET TO DEBUG!!! [recidive] jail is enabled
    loglevel = INFO

    dbpurgeage = 648000

Save and close.

Finally, restart the fail2ban process.

..  code-block:: bash

    $ sudo systemctl restart fail2ban

Setup PSAD
------------------------------------------

..  code-block:: bash

    $ sudo apt install psad
    $ sudo iptables -A INPUT -j LOG
    $ sudo iptables -A FORWARD -j LOG
    $ sudo ip6tables -A INPUT -j LOG
    $ sudo ip6tables -A FORWARD -j LOG
    $ sudo vim /etc/psad/psad.conf

Change the following values:

..  code-block:: text

    EMAIL_ADDRESS mpm@localhost;
    HOSTNAME mpmdev;
    ALERTING_METHODS noemail;
    EMAIL_THROTTLE 100;
    ALERT_ALL N;
    ENABLE_AUTO_IDS_EMAIL N;
    EMABLE_DNS_LOOKUPS N;
    ENABLE_WHOIS_LOOKUPS N;

Save and close, and then reload like this:

..  code-block:: bash

    $ sudo psad -R
    $ sudo psad --sig-update
    $ sudo psad -H
    $ sudo psad --Status

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
    $ sudo certbot certonly --apache -d eco.mousepawmedia.com

In the output for the certbot command, take note of the paths where the
certificate and chain were saved. You'll need that in the next step.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/eco.conf

Set the contents of that file to the following, substituting the
domain name in place for :code:`ServerName`, and fixing the paths for
the :code:`SSLCertificateFile` and :code:`SSLCertificateKeyFile`.
Also set the :code:`DocumentRoot` to the desired directory.

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName eco.mousepawmedia.com

            ServerAdmin webmaster@mousepawmedia.com
            DocumentRoot /opt/eco

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            SSLCertificateFile /etc/letsencrypt/live/eco.mousepawmedia.com/fullchain.pem
            SSLCertificateKeyFile /etc/letsencrypt/live/eco.mousepawmedia.com/privkey.pem
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
    $ sudo mkdir eco
    $ sudo chown www-data:www-data eco
    $ sudo chmod 775 eco

We need to tell Apache2 to read this directory.

..  code-block:: bash

    $ sudo vim /etc/apache2/apache2.conf

Scroll down to the section with all the directories, and add these entries:

..  code-block:: apache

    <Directory /opt/eco/>
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
    $ sudo a2ensite eco
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

Server Controls
============================================

PHPMyAdmin
---------------------------------------------

..  code-block:: bash

    $ sudo apt install phpmyadmin

On the configuration dialog, select ``apache2`` by selecting it and tapping
:kbd:`Space`. Enter an application password (different from the MySQL root
password) and confirm it.

Edit the configuration for PHP, to force HTTPS.

..  code-block:: bash

    $ sudo vim /etc/phpmyadmin/config.inc.php

Add the following line to the bottom of that file.

..  code-block:: php

    $cfg['ForceSSL'] = true;

Save and close.

Now enable one necessary PHP module and restart Apache2.

..  code-block:: bash

    $ sudo phpenmod mbstring
    $ sudo systemctl restart apache2

Validate that you can access ``https://<serveraddress>/phpmyadmin``. Log
in there with the username ``phpmyadmin`` and the password you defined.

..  warning:: You may need to disable the Apache2 module ``security2``
    before you can access PHPMyAdmin. Otherwise, it throws an internal 404.
    We're not sure why. To fix the problem, run ``sudo a2dismod security2`` and
    restart the Apache2 service.

By default, you can't do much of anything from this account. To enable
access to all databases, run the following:

..  code-block:: bash

    $ sudo mysql -u root

Run the following commands in the MySQL shell:

..  code-block:: mysql

    GRANT ALL PRIVILEGES ON *.* TO 'phpmyadmin'@'localhost' WITH GRANT OPTION;
    FLUSH PRIVILEGES;
    \q

Then refresh PHPMyAdmin in your browser. You should be able to edit all
databases now.

Control Access Switch
----------------------------------------------

For security reasons, we want to be able to turn on and off controls like
PHPMyAdmin using a script.

..  code-block:: bash

    $ sudo vim /opt/scripts/sys/controls

The contents of that file are as follows.

..  code-block:: bash

    #!/bin/bash

    set -e

    case $1 in
    'on')
        sudo a2enconf phpmyadmin
        sudo systemctl restart apache2
        echo "Admin control panels are turned ON."
        ;;
    'off')
        sudo a2disconf phpmyadmin
        sudo systemctl restart apache2
        echo "Admin control panels are turned OFF."
        ;;
    *)
        echo "You must specify 'on' or 'off'."
        exit 1
        ;;
    esac

Save and close, and then make executable.

..  code-block:: bash

    $ sudo chmod +x /opt/scripts/sys/controls

Now you can run :code:`controls on` or :code:`controls off` to toggle
access to PHPMyAdmin.

LDAP Server
===============================================

In this section, I'll set up an LDAP Server which uses StartTLS for
security. Start by following the Apache setup instructions to create
a virtual host and certificate for ``id.mousepawmedia.com``.

Installation
------------------------------------

..  code-block:: bash

    $ sudo apt install slapd ldap-utils ldap-account-manager
    $ sudo dpkg-reconfigure slapd

During the configuration, use these settings:

- Omit OpenLDAP server configuration? No
- DNS domain name? id.mousepawmedia.com
- Organization name? mousepawmedia
- Administrator password? (enter one)
- Database backend to use? MDB
- Remove the database when slapd is purged? No
- Move old database? Yes
- Allow LDAPv2 protocol? No

Now we open the LDAP port and check that LDAP is working:

..  code-block:: bash

    $ sudo ufw allow 389
    $ ldapwhoami -H ldap:// -x

The last command should return ``anonymous``.

Now we need to set up LDAP to use the Let's Encrypt certificates we created.

..  code-block:: bash

    $ sudo vim /opt/scripts/root/postrenew

Set the contents of that file to:

..  code-block:: bash

    cp /etc/letsencrypt/live/id.mousepawmedia.com/* /etc/ldap/sasl2
    chown -R openldap:openldap /etc/ldap/sasl2
    chmod 700 /etc/ldap/sasl2

Save and close. Then, make executable and run.

..  code-block:: bash

    $ sudo chmod +x /opt/scripts/root/postrenew
    $ sudo /opt/scripts/root/postrenew

That script will need to be run every time certificates are renewed, so let's
update our cron job.

..  code-block:: bash

    $ sudo crontab -e

Modify the crontab for certbot to the following (without changing the time
you have already specified, of course):

..  code-block:: cron

    17 3 * * * /usr/bin/certbot renew --post-hook /opt/scripts/root/postrenew

Save and close.

LDAP is modified through commands and scripts. We need to create an ``ldif``
file in our current directory (e.g. home) like this:

..  code-block:: bash

    $ sudo vim ssl.ldif

Set the contents to the following, changing the path to the certificates
if necessary.

..  code-block:: text

    dn: cn=config
    changetype: modify
    add: olcTLSCACertificateFile
    olcTLSCACertificateFile: /etc/ldap/sasl2/fullchain.pem
    -
    add: olcTLSCertificateKeyFile
    olcTLSCertificateKeyFile: /etc/ldap/sasl2/privkey.pem
    -
    add: olcTLSCertificateFile
    olcTLSCertificateFile: /etc/ldap/sasl2/cert.pem

Save and close, and then modify LDAP with that file.

..  code-block:: bash

    $ sudo ldapmodify -Y EXTERNAL -H ldapi:/// -f /home/mpm/ssl.ldif

Check the output. If you get the infamous ``ldap_modify: Other (e.g., implementation specific) error (80)``,
check that all of the above are correct, especially permissions.

Once the command works, restart LDAP and ensure that StartTLS is working.

..  code-block:: bash

    $ sudo systemctl restart slapd
    $ ldapwhoami -H ldap://id.mousepawmedia.com -x -ZZ

That should return ``anonymous`` if it's working.

`SOURCE: How to Configure OpenLDAP with StartTLS Mode on Ubuntu 16.04 (Medium) <https://medium.com/@stsarut/how-to-configure-openldap-with-starttls-mode-on-ubuntu-16-04-3af036b16c6c>`_

`SOURCE: OpenLDAP with TLS and LetsEncrypt on UBuntu 16.04 (Peter Hicks) <https://blog.poggs.com/2018/08/19/openldap-with-tls-and-letsencrypt-on-ubuntu-16-04/>`_

LDAP Account Manager Config
-----------------------------------

Now we need to adjust LDAP Account Manager's configuration. Go to
https://id.mousepawmedia.com/lam and click ``LAM Configuration`` in the upper-right
corner.

Click ``General settings``. If this is your first time logging in, the Master
Password is ``lam``.

On the page that appears, enter a new master password. Be sure not to lose it!

You can adjust other settings here as needed, but the defaults should be fine.

Click ``Ok``.

Now go to ``LAM Configuration`` and ``Edit server profiles``. Click ``Manage
server profiles`` and rename the profile to ``admin``. Click ``OK``.

Now go to ``LAM Configuration`` and ``Edit server profiles``. Log in.
The first time you do so, the password is ``lam``. Be sure to change it.

Ensure the following settings on the General settings tab:

* Server address: ``ldap://id.mousepawmedia.com:389``
* Activate TLS: yes
* Tree suffix: ``dc=id,dc=mousepawmedia,dc=com``
* Advanced options > Display name: ``MousePaw Media ID``
* Default language: ``English (USA)``
* Time zone: ``America/Chicago``

Scroll down to Security settings. Set the Login method to Fixed list, and then
set ``List of valid users`` to ``cn=admin,dc=id,dc=mousepawmedia,dc=com``.

Click ``Save``.

Configuring LDAP Schema
--------------------------------

Go to ``LAM Configuration`` and ``Edit server profiles``. Log in.

Then, go to the Account types tab. Create two account types:

* Users
    * LDAP suffix: ``ou=Users,dc=id,dc=mousepawmedia,dc=com``
    * List attributes: ``#uid;#cn;#mail;#uidNumber;#gidNumber``
* Groups
    * LDAP suffix: ``ou=Groups,dc=id,dc=mousepawmedia,dc=com``
    * List attributes: ``#cn;#gidNumber;#memberUID;#description``

Go to the Modules tab. For Users, select these modules:

* Personal (inetOrgPerson)(*)
* Unix (posixAccount)
* Shadow (shadowAccount)

For Groups, select these modules:

* Unix (posixGroup)(*)

On the Module settings, you can hide some options. Customize this as you like.

Then, click ``Save``.

Login on the main page with the ``admin`` LDAP account.

Click the Groups tab and create the groups you want. Here are ours:

* user
* admin
* alumni
* community
* contentdev
* designproduction
* former
* hiring
* management
* masscomm
* programming
* repomaster
* staff

The ``user`` group will be the base group for everyone, while the others will
be used by other applications for permissions and group membership.

Go to ``Tools`` and ``Profile editor``. Modify the default ``Users`` group
to have the following defaults:

* Profile name: ``default``
* RDN identifier: ``uid``
* Primary group: ``user``
* Password warning: (empty)
* Password expiration: (empty)
* Minimum password age: (empty)
* Maximum password age: (empty)

Save the profile.

Now click ``Users`` and create the user accounts, with the following
fields at minimum. (Notes about the fields are in parentheses below.)

* RDN identifier: uid
* Personal
    * First name
    * Last name
    * Email address
* Unix
    * Username
    * Common name (full name with middle initial)
    * Primary group (always set to ``user``)
    * Additional groups (set as desired for user)

Now click ``Save`` and ``Edit again``. Click ``Set password`` at the top to
set the user password.

..  NOTE: If you set the RDN identifier wrong, you can use ``Tree view`` to
    ``Rename`` from ``cn=name,ou=Users,dc=id,dc=mousepawmedia,dc=com`` to
    ``uid=name,ou=Users,dc=id,dc=mousepawmedia,dc=com``.
