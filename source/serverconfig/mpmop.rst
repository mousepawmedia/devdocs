Server Configuration: MPM Operations Server
##############################################

This documentation outlines how the Linode "mpmop" server was built.

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
    $ sudo mysql_secure_installation

Validate Password is optional; you should specify the root password
and answer ``Y`` to the following:

* Remove anonymous users?
* Disallow root login remotely?
* Remove test database and access to it?
* Reload privilege tables now?

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

..  code-block:: text

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

..  code-block:: text

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

Static Websites
============================================

We host all our websites public Git repositories. We can leverage that to
easily publish websites and documentation.

..  code-block:: bash

    $ sudo mkdir /opt/sources
    $ sudo chown www-data:www-data /opt/sources
    $ cd /opt/sources
    $ sudo -u www-data git clone git://github.com/mousepawmedia/mpm-websites.git
    $ sudo ln -sf /opt/sources/mpm-websites/mousepawmedia.com /opt/mpm
    $ sudo chown www-data:www-data /opt/mpm
    $ sudo ln -sf /opt/sources/mpm-websites/mousepawmedia.com /opt/mpg
    $ sudo chown www-data:www-data /opt/mpg
    $ sudo ln -sf /opt/sources/mpm-websites/eco.mousepawmedia.com /opt/eco
    $ sudo chown www-data:www-data /opt/eco
    $ sudo ln -sf /opt/sources/mpm-websites/id.mousepawmedia.com /opt/id
    $ sudo chown www-data:www-data /opt/id
    $ sudo a2enmod rewrite include
    $ sudo vim /etc/apache2/apache2.conf

Add the following:

..  code-block:: apache

    <Directory /opt/sources/>
        Options FollowSymLinks
        Require all granted
    </Directory>

Save and close.

Kimai
===========================================

Installing Composer
------------------------------------

First, we need to install the lastest Composer:

..  code-block:: bash

    $ sudo apt install php7.4-cli php7.4-intl unzip
    $ cd /tmp
    $ curl -sS https://getcomposer.org/installer -o composer-setup.php
    $ HASH=`curl -sS https://composer.github.io/installer.sig`
    $ php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"

You should see "Installer verified". If so, we can install.

..  code-block:: bash

    $ sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
    $ composer

If that works (displaying a help menu), we can move on to installing Kimai.

`SOURCE: How to Install Composer on Ubuntu 20.04 (DigitalOcean) <https://www.digitalocean.com/community/tutorials/how-to-install-composer-on-ubuntu-20-04-quickstart>`_

Installing Kimai
------------------------------------

Next, in MyPHPAdmin, create a user and a database called ``kimai2``. You'll
need the password for that user shortly.

In my case, I'm importing from a prior Kimai2 database:

..  code-block:: bash

    gunzip -c /home/mpm/IMPORTED/backup.sql.gz | sudo mysql -u root

Now I create the ``kimai2`` user and grant its permissions.

..  code-block:: bash

    $ sudo mysql -u root

Run the following:

..  code-block:: text

    CREATE USER 'kimai2'@'localhost' IDENTIFIED BY 'some_password';
    GRANT ALL PRIVILEGES ON `kimai2`.* TO 'kimai2'@'localhost' WITH GRANT OPTION;
    \q

We install Kimai like this:

..  code-block:: bash

    $ cd /tmp
    $ git clone -b 1.13 --depth 1 https://github.com/kevinpapst/kimai2.git
    $ sudo mv kimai2 /opt/time
    $ cd /opt/time/
    $ composer install --no-dev --optimize-autoloader
    $ vim .env

Now edit that file so it contains something like the following, changing the
values ``CHANGE_ME`` (two places below) as appropriate.

..  code-block:: text

    # This file is a "template" of which env vars need to be defined for your application
    # Copy this file to .env file for development, create environment variables when deploying to production
    # https://symfony.com/doc/current/best_practices/configuration.html#infrastructure-related-configuration

    ###> symfony/framework-bundle ###
    APP_ENV=prod
    APP_SECRET=CHANGE_ME
    #TRUSTED_PROXIES=127.0.0.1,127.0.0.2
    #TRUSTED_HOSTS=localhost,example.com
    ###< symfony/framework-bundle ###

    ###> doctrine/doctrine-bundle ###
    # Format described at http://docs.doctrine-project.org/projects/doctrine-dbal/en/latest/reference/configuration.html#connecting-using-a-url
    # For a MySQL database, use: "mysql://db_user:db_password@127.0.0.1:3306/db_name?serverVersion=10.2.12&charset=utf8"
    # For a MariaDB database, use: "mysql://db_user:db_password@127.0.0.1:3306/db_name?serverVersion=mariadb-10.2.12"
    # For an SQLite database, use: "sqlite:///%kernel.project_dir%/var/data/kimai.sqlite"
    # IMPORTANT: You MUST configure your server version, either here or in config/packages/doctrine.yaml
    DATABASE_URL=mysql://kimai2:CHANGE_ME@127.0.0.1:3306/kimai2
    #DATABASE_URL=sqlite:///%kernel.project_dir%/var/data/kimai.sqlite
    ###< doctrine/doctrine-bundle ###

    ###> nelmio/cors-bundle ###
    CORS_ALLOW_ORIGIN=^https?://localhost(:[0-9]+)?$
    ###< nelmio/cors-bundle ###

    ### Email configuration
    # SMTP: smtp://localhost:25?encryption=&auth_mode=
    # Google: gmail://username:password@default
    # Amazon: ses://ACCESS_KEY:SECRET_KEY@default?region=eu-west-1
    # Mailchimp: mandrill://KEY@default
    # Mailgun: mailgun://KEY:DOMAIN@default
    # Postmark: postmark://ID@default
    # Sendgrid: sendgrid://KEY@default
    # Disable emails: null://null
    MAILER_URL=null://null
    MAILER_FROM=eco@mousepawmedia.com

Save and close, and then run the following:

..  code-block:: bash

    $ bin/console kimai:install -n
    $ sudo chown -R mpm:www-data /opt/time
    $ chmod -R g+r /opt/time
    $ chmod -R g+rw /opt/time/var/
    $ chmod -R g+rw /opt/time/public/avatars/

Kimai itself is now installed.

`SOURCE: Installation (Kimai Docs) <https://www.kimai.org/documentation/installation.html>`_

Setting Up Apache2 for Kimai
------------------------------------

To setup Apache, first follow the instructions earlier for adding a new
domain name and Let's Encrypt certificate. Then add or modify the following
file:

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/time.conf

Set the contents of that file to:

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost time.mousepawmedia.com:443>
            ServerName time.mousepawmedia.com
            DocumentRoot /opt/time/public

            SSLEngine on
            SSLCertificateFile /etc/letsencrypt/live/time.mousepawmedia.com/fullchain.pem
            SSLCertificateKeyFile /etc/letsencrypt/live/time.mousepawmedia.com/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf
            Header always set Strict-Transport-Security "max-age=31536000"
            Header always set Content-Security-Policy upgrade-insecure-requests

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            <Directory /opt/time/public>
                Options +FollowSymLinks
                AllowOverride All

                <IfModule mod_dave.c>
                    Dav off
                </IfModule>

                Require all granted

                FallbackResource /index.php
            </Directory>

            <Directory /opt/time>
                Options FollowSymLinks
            </Directory>

            <Directory /opt/time/public/bundles>
                FallbackResource disabled
            </Directory>

            BrowserMatch "MSIE [2-6]" \
            nokeepalive ssl-unclean-shutdown \
            downgrade-1.0 force-response-1.0
            # MSIE 7 and newer should be able to use keepalive
            BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown
        </VirtualHost>
    </IfModule>

Save and close, and then run this:

..  code-block:: bash

    $ sudo vim /etc/apache2/apache2.conf

Add the following section:

..  code-block:: apache

    <Directory /opt/time>
        Options FollowSymLinks
    </Directory>

    <Directory /opt/time/public>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

Save and close, and then enable the site and restart Apache2:

..  code-block:: bash

    $ sudo a2ensite time
    $ sudo systemctl restart apache2

Now go to ``https://time.<serveraddress>`` and verify that everything works so far.

`SOURCE: Webserver configuration (Kimai Docs) <https://www.kimai.org/documentation/webserver-configuration.html#apache>`_

Reload/Repair Script
--------------------------------------------

Kimai is particularly vulnerable to getting its permissions messed up.
This script will (usually hopefully) fix that:

..  code-block:: bash

    $ vim /opt/time/cache.sh

Set the contents of that file to this:

..  code-block:: bash

    #!/bin/bash

    cd /opt/time

    sudo chown -R mpm:www-data .
    chmod -R g+r .
    chmod -R 775 var/
    chmod -R g+rw public/avatars/

    if [[ ! -d "var/" || ! -d "var/cache/prod/" ]];
    then
    echo "Cache directory does not exist at: var/cache/prod/"
    exit 1
    fi

    if [[ ! -f "bin/console" ]];
    then
    echo "Kimai console does not exist at: bin/console"
    exit 1
    fi

    sudo rm -rf var/cache/prod/*
    sudo -u www-data bin/console kimai:reload --env=prod
    sudo chown -R mpm:www-data .
    chmod -R g+r .
    chmod -R 775 var/
    chmod -R g+rw public/avatars/

Save and close, and then make that script executable.

..  code-block:: bash

    $ chmod +x /opt/time/cache.sh

Finally, run the script.

..  code-block:: bash

    $ /opt/time/cache.sh

Kimai should be working now.

LDAP for Kimai
------------------------------------

Let's set up LDAP for Kimai.

..  code-block:: bash

    $ cd /opt/time
    $ composer update
    $ composer require laminas/laminas-ldap --update-no-dev --optimize-autoloader
    $ vim /opt/time/config/packages/local.yaml

Set the contents of that file to this:

..  code-block:: yaml

    kimai:
        user:
            registration: false
            password_reset: false
        permissions:
            roles:
                ROLE_USER: ['!password_own_profile']
                ROLE_TEAMLEAD: ['!password_own_profile']
                ROLE_ADMIN: ['!password_own_profile']
        ldap:
            connection:
                host: id.mousepawmedia.com
                port: 389
                useStartTls: true
                #bindRequiresDn: true
            user:
                baseDn: ou=Users, dc=id, dc=mousepawmedia, dc=com
                usernameAttribute: uid
                filter: (&(objectClass=inetOrgPerson))
                attributes:
                    - { ldap_attr: "usernameAttribute", user_method: setUsername }
                    - { ldap_attr: mail, user_method: setEmail }
                    - { ldap_attr: cn, user_method: setAlias }
            role:
                baseDn: ou=Groups, dc=id, dc=mousepawmedia, dc=com
                filter: (&(objectClass=posixGroup)(|(cn=management)(cn=admin)))
                usernameAttribute: uid
                userDnAttribute: memberUid
                nameAttribute: cn
                groups:
                    - { ldap_value: management, role: ROLE_TEAMLEAD }
                    - { ldap_value: admin, role: ROLE_SUPER_ADMIN }
    security:
        providers:
            chain_provider:
                chain:
                    providers: [kimai_ldap]
        firewalls:
            secured_area:
                kimai_ldap: ~

Save and close. Finally, reload Kimai.

..  code-block:: bash

    sudo -u www-data bin/console kimai:reload --env=prod

Now you should be able to browse to Kimai and log in with LDAP.

`SOURCE: LDAP (Kimai Docs) <https://www.kimai.org/documentation/ldap.html>`_

Nextcloud
===============================================

Installation
----------------------------

Let's install the other PHP packages we need for this. Most of these are
probably already installed, but we're putting them here to be certain.

..  code-block:: bash

    $ sudo apt install php7.4-bz2 php7.4-intl php7.4-xml php7.4-zip php7.4-curl php7.4-gd php-imagick php7.4-mbstring php7.4-ldap php7.4-imap php-7.4-apcu php7.4-bcmath php7.4-gmp imagick ffmpeg libmagickcore-6.q16-6-extra
    $ sudo phpenmod bcmath gmp

Now we can install Nextcloud itself.

..  note:: While we are installing 21.0.2 below, Nextcloud has been upgraded
    many times since. Adjust commands below according to the latest stable
    version of Nextcloud.

..  code-block:: bash

    $ cd /tmp
    $ curl -LO https://download.nextcloud.com/server/releases/nextcloud-21.0.2.tar.bz2
    $ curl -LO https://download.nextcloud.com/server/releases/nextcloud-21.0.2.tar.bz2.sha256
    $ shasum -a 256 -c nextcloud-21.0.2.tar.bz2.sha256 < nextcloud-21.0.2.tar.bz2

Ensure that last command says "OK" before continuing, as that confirms the
tarball hasn't been tampered with or spoofed.

..  code-block:: bash

    $ rm nextcloud-21.0.2.tar.bz2.sha256
    $ sudo tar -C /opt -xvjf /tmp/nextcloud-21.0.2.tar.bz2
    $ vim /tmp/nextcloud.sh

Set the contents of that file to...

..  code-block:: bash

    ocpath='/opt/cloud'
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
    chmod 0644 ${ocpath}/.htaccess
    chown ${rootuser}:${htgroup} ${ocpath}/.htaccess
    fi
    if [ -f ${ocpath}/data/.htaccess ]
    then
    chmod 0644 ${ocpath}/data/.htaccess
    chown ${rootuser}:${htgroup} ${ocpath}/data/.htaccess
    fi

Save and close, and then run the file.

..  code-block:: bash

    $ sudo bash /tmp/nextcloud.sh

After that finishes, we can start configuring Apache2.

Apache2 Configuration
----------------------------

Let's create an Apache2 site configuration for Nextcloud.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/cloud.conf

Set the contents to...

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost cloud.mousepawmedia.com:443>
            ServerName cloud.mousepawmedia.com
            DocumentRoot /opt/cloud

            SSLCertificateFile /etc/letsencrypt/live/cloud.mousepawmedia.com/fullchain.pem
            SSLCertificateKeyFile /etc/letsencrypt/live/cloud.mousepawmedia.com/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf
            Header always set Strict-Transport-Security "max-age=31536000"
            Header always set Content-Security-Policy upgrade-insecure-requests

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            <Directory /opt/cloud>
                Options +FollowSymLinks
                AllowOverride All
                Satisfy Any

                <IfModule mod_dave.c>
                        Dav off
                </IfModule>

                SetEnv HOME /opt/cloud
                SetEnv HTTP_HOME /opt/cloud
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

    $ sudo vim /etc/apache2/apache2.conf

Add the following below the other ``<Directory>`` entries...

..  code-block:: apache

    <Directory /opt/cloud>
        Options Indexes FollowSymLinks
        Require all granted
    </Directory>

Then, enable the site and restart Apache2.

..  code-block:: bash

    $ sudo a2ensite cloud
    $ sudo a2enmod headers
    $ sudo systemctl restart apache2

Database Setup
-------------------------------------

Before setting up the database, we need to change a couple of things in the
MySQL configuration.

..  code-block:: bash

    $ sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf

Add or change the following lines:

..  code-block:: bash

    transaction_isolation = READ-COMMITTED
    binlog_format = ROW

Save and close, and restart the database.

..  code-block:: bash

    $ sudo systemctl restart mysql

We need to create a ``nextcloud`` database and a ``nextcloud`` user in MySQL.

..  code-block:: bash

    $ sudo mysql -u root

Run the following:

..  code-block:: text

    CREATE USER 'nextcloud'@'localhost' IDENTIFIED BY 'some_password';
    CREATE DATABASE IF NOT EXISTS nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
    GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost' WITH GRANT OPTION;
    FLUSH privileges;
    \q

Now ensure that PHP is set up to work with MySQL.

..  code-block:: bash

    $ sudo phpenmod pdo_mysql

Nextcloud Configuration
------------------------------

Go to the new Nextcloud site. On the setup screen, specify an admin account.

Click ``Storage and Database``, set the Data folder to ``/opt/cloud/data``.
Select ``MySQL`` for the database, and provide the database user, password,
and database name. The fourth field should be ``localhost``.

Click ``Finish setup``.

..  note:: If you have problems logging into the database on this screen,
    check PHPmyadmin → ``nextcloud`` (database) → Privileges. The ``nextcloud``
    user should be listed, with ``Grant Yes``.

`SOURCE How To Install and Configure Nextcloud on Ubuntu 16.04 (DigitalOcean) <https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-nextcloud-on-ubuntu-16-04>`_

Preventing Lockouts
-----------------------------

Due to a glitch in Nextcloud, we have to configure fail2ban to prevent lockouts.

..  code-block:: bash

    $ sudo vim /etc/fail2ban/filter.d/apache-auth.conf

Change or add the following lines:

..  code-block:: text

    # ignore intentional auth failures from nextcloud admin page
    ignoreregex = cloud/[data/.ocdata|config]

Configuring Memory Caching
-----------------------------

To improve performance, we'll enable memory caching. We are using APCu (since
we're using PHP 7.4), so we simply need to enable this for Nextcloud.

..  code-block:: bash

    $ sudo vim /opt/cloud/config/config.php

Add the following line before the end:

..  code-block:: text

    'memcache.local' => '\OC\Memcache\APCu',

Save and close, and then restart Apache2.

PHP Configuration
----------------------------

Nextcloud recommends a few tweaks to php.ini. Run...

.. code-block:: bash

    $ sudo vim /etc/php/7.4/apache2/php.ini

Find and edit (or uncomment) the following lines:

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

Save and close, and restart Apache2.

Also edit Nextcloud's configuration:

..  code-block:: bash

    $ sudo vim /opt/cloud/config/config.php

Add or edit the following line

..  code-block:: php

    'default_phone_region' => 'US',

Save and close.

Set Up Cronjob
----------------------------

It is recommended to use Cron for background tasks. We will set this up now.

..  code-block:: bash

    $ sudo crontab -u www-data -e

Add the following line:

..  code-block:: text

    */15  *  *  *  * php -f /opt/cloud/cron.php

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

    $ sudo vim /opt/cloud/config/config.php

Add the following to that file, before the closing ``);``:

..  code-block:: php

    'objectstore' => array(
        'class' => '\\OC\\Files\\ObjectStore\\S3',
        'arguments' => array(
        'bucket' => 'mpm-cloud',
        'autocreate' => false,
        'key' => '2T67FBD41Z99MJ6HY70O',
        'secret' => 'xpGOIP7I9pSmuWh9QwXDpdS2s8hESPQw8TTMSGIK',
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

LDAP Authentication
--------------------------------

In Nextcloud, go to ``Apps`` and enable LDAP. Then, go to ``Admin`` and ``LDAP``.

Set the following options:

* Server

  * Host: ``id.mousepawmedia.com``

  * Port: ``389``

  * Base DN: ``ou=Users, dc=id, dc=mousepawmedia, dc=com``

Click ``Test Base DN``, and then ``Continue``.

Set ``Only these object classes:`` to just ``inetOrgPerson``, and click
``Verify settings and count users``, and then ``Continue``.

Check ``LDAP/AD Username`` and ``LDAP/AD Email Address``, and then click
``Advanced.`` Set...

* Directory Settings

  * User Display Name Field: ``uid``

  * 2nd User Display Name Field: ``cn``

  * Base User Tree: ``ou=Users, dc=id, dc=mousepawmedia, dc=com``

  * Group Display Name Field: ``cn``

  * Base Group Tree: ``ou=Groups, dc=id, dc=mousepawmedia, dc=com``

  * Group-Member association: ``memberUid``

* Special Attributes

  * Email field: ``mail``

Also, click the Groups tag and select all the Groups that you want to use
in Nextcloud. We use all but ``users`` and ``disabled``.

Click ``Test Configuration``.

The settings are automatically saved. Log in as an LDAP user to test.

..  note:: If a user profile picture (``jpegPhoto``) is specified on the
    LDAP account, it will be used automatically on Nextcloud.

`SOURCE: User Auth with LDAP (Nextcloud) <https://docs.nextcloud.com/server/9/admin_manual/configuration_user/user_auth_ldap.html>`_

Changing Default Files
--------------------------------

To change or remove the default files, change what is found in
``/opt/cloud/core/skeleton``. For our instance of Nextcloud, we've removed
all these files.

Collabora Office Online
--------------------------------

These instructions assume you've already set up Collabora Office Online
on its own separate cloud.

To connect Collabora Office Online to Nextcloud, go to Nextcloud.
Click the menu, and go to :guilabel:`Apps --> Productivity`. Install the
"Collabora Online connector". Then, go to
:guilabel:`Admin --> Additional settings --> Collabora Online`.

Set :guilabel:`Collabora Online server` to :code:`https://office.mousepawmedia.com/`
(or whatever the url is) and click :guilabel:`Apply`.

Now you can go to the Office app in Nextcloud to access Collabora Office!

`SOURCE: Getting started in 3 steps (Nextcloud) <https://nextcloud.com/collaboraonline/>`_

Last, we need to modify fail2ban so it won't lock users out when using
CollaboraOffice.

..  code-block:: bash

    $ sudo nano /etc/fail2ban/filter.d/cloud.conf

Set the contents of that file to:

..  code-block:: text

    [Definition]
    failregex={"reqId":".*","remoteAddr":".*","app":"core","message":"Login failed: '.*' \(Remote IP: '<host>'\)","level":2,"time":".*"}
    ignoreregex =

Save and close. Then, run...

..  code-block:: bash

    $ sudo vim /etc/fail2ban/jail.local

Set the contents of that file to:

..  code-block:: text

    [nextcloud]
    enabled = true # set to false to disable
    filter  = nextcloud
    port    =  http,https # Change this to https if you aren't using http
    logpath = /opt/cloud/data/nextcloud.log # Make sure this is the right path.
    ignoreip = 10.0.2.1/24 # Change/Delete this if you want to Ignore one or more IP's

Save and close. Then, restart fail2ban.

..  code-block:: bash

    $ sudo systemctl restart fail2ban

`SOURCE: Setup Fail2Ban with Owncloud (TechKnight) <https://techknight.eu/2015/07/25/setup-fail2ban-with-owncloud-8-1-0/>`_

Mattermost
================

We'll be using Mattermost self-hosted on the free plan. We won't be integrating
with LDAP at this time. Rather, anyone can create accounts. Invites must be
issued to join the Staff team.

Start by creating the certificate for the chat domain. You need to decide
on your domain before starting the installation of Mattermost.

..  code-block:: bash

    $ sudo a2ensite 000-default
    $ sudo systemctrl restart apache2
    $ sudo certbot certonly --apache -d chat.mousepawmedia.com
    $ sudo a2dissite 000-default
    $ sudo systemctrl restart apache2

Install Mattermost
------------------------------

To set up Mattermost, we need to first create the database:

..  code-block:: bash

    $ sudo mysql -u root

Run the following:

..  code-block:: text

    CREATE USER 'mattermost'@'%' IDENTIFIED BY 'mmuser-password';
    CREATE DATABASE mattermost;
    GRANT ALL PRIVILEGES ON mattermost.* to 'mattermost'@'%';
    exit;

Now we download and install the latest version of Mattermost. The version and
download link are on `mattermost.com/deploy <https://mattermost.com/deploy/>`_

..  code-block:: bash

    $ cd /tmp
    $ wget https://releases.mattermost.com/6.0.0/mattermost-6.0.0-linux-amd64.tar.gz
    $ tar -xvzf mattermost*.gz
    $ sudo mv mattermost /opt/mattermost
    $ sudo mkdir /opt/mattermost/data
    $ sudo useradd --system --user-group mattermost
    $ sudo chown -R mattermost:mattermost /opt/mattermost
    $ sudo chmod -R g+w /opt/mattermost
    $ sudo vim /opt/mattermost/config/config.json

Change the following sections of that file to match the following, subtituting
in your own values as appropriate:

..  code-block:: json

    {
      "ServiceSettings": {
        "SiteURL": "https://chat.mousepawmedia.com",
      }
      "SqlSettings": {
        "DriverName": "mysql",
        "DataSource": "mattermost:mmuser-password@tcp(localhost:3306)/mattermost?charset=utf8mb4,utf8&writeTimeout=30s",
        }
    }

Save and close.

Now test Mattermost by running:

..  code-block:: bash

    $ cd /opt/mattermost
    $ sudo -u mattermost ./bin/mattermost

Watch for the text ``Server is listening on :8065``. If you see it, everything
worked. Now we set this up to work in production.

..  code-block:: bash

    sudo vim /lib/systemd/system/mattermost.service

Set the contents of that file to:

..  code-block:: text

    [Unit]
    Description=Mattermost
    After=network.target
    After=mysql.service
    BindsTo=mysql.service

    [Service]
    Type=notify
    ExecStart=/opt/mattermost/bin/mattermost
    TimeoutStartSec=3600
    KillMode=mixed
    Restart=always
    RestartSec=10
    WorkingDirectory=/opt/mattermost
    User=mattermost
    Group=mattermost
    LimitNOFILE=49152

    [Install]
    WantedBy=mysql.service

Save and close, and then run:

..  code-block:: bash

    $ sudo systemctl daemon-reload
    $ sudo systemctl status mattermost.service

Ensure the unit was loaded, and then...

..  code-block:: bash

    $ sudo systemctl start mattermost.service
    $ curl http://localhost:8065

Ensure that responds, then...

..  code-block:: bash

    $ sudo systemctl enable mattermost.service

`SOURCE: Installing Mattermost on Ubuntu 20.04 (Mattermost) <https://docs.mattermost.com/install/installing-ubuntu-2004-LTS.html>`_

Apache2 Proxy
-----------------

We'll use Apache2 as the proxy. The recommended technique is actually to use
NGINX, but as we're already using Apache2 on this server, we'll stick with it.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/chat-mattermost.conf

Set the contents of that file to:

..  code-block:: apache

    <VirtualHost chat.mousepawmedia.com:443>
        ServerName chat.mousepawmedia.com
        DocumentRoot /opt/mattermost
        ServerAdmin webmaster@mousepawmedia.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        SSLEngine on
        SSLCertificateFile /etc/letsencrypt/live/chat.mousepawmedia.com/fullchain.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/chat.mousepawmedia.com/privkey.pem
        Include /etc/letsencrypt/options-ssl-apache.conf

        ProxyPreserveHost on

        RewriteEngine On
        RewriteCond %{REQUEST_URI} /api/v[0-9]+/(users/)?websocket [NC]
        RewriteCond %{HTTP:UPGRADE} ^WebSocket$ [NC]
        RewriteCond %{HTTP:CONNECTION} \bUpgrade\b [NC]
        RewriteRule .* ws://127.0.0.1:8065%{REQUEST_URI} [P,QSA,L]

        <Location />
            Require all granted
            ProxyPass http://127.0.0.1:8065/
            ProxyPassReverse http://127.0.0.1:8065/
            ProxyPassReverseCookieDomain 127.0.0.1 mysubdomain.mydomain.com
        </Location>

    </VirtualHost>

..  code-block:: bash

    $ sudo a2enmod rewrite proxy proxy_http proxy_wstunne
    $ sudo a2ensite chat-mattermost
    $ sudo systemctl restart apache2

`SOURCE: Configuring Apache2 as a proxy for Mattermost Server (Unofficial) (Mattermost) <https://docs.mattermost.com/configure/config-proxy-apache2.html>`_

Mattermost Configuration
-----------------------------------

You should be able to go to ``chat.mousepawmedia.com`` now and proceed with
Mattermost setup. Create the initial super user and the team. In this case,
the team is "MousePaw Media", with the URL "staff".

After setup, select the product menu in the top-left and select System Console.

Email Notifications
^^^^^^^^^^^^^^^^^^^^^^^^^^

On the System Console, from the left menu, go to :guilabel:`Environment`
and :guilabel:`SMTP`. Set the following settings:

* :guilabel:`SMTP Server`: ``mail.mousepawmedia.com``
* :guilabel:`SMTP Server Port`:  ``465``
* :guilabel:`Enable SMTP Authentication`: True
* :guilabel:`SMTP Server Username`: ``noreply@mousepawmedia.com``
* :guilabel:`SMTP Server Password`: (Password for email account)
* :guilabel:`Connection Security`: TLS

Click :guilabel:`Save`, and then :guilabel:`Test Connection`. (The other way
around will not use the new settings in the test.)

S3 Integration
^^^^^^^^^^^^^^^^^^^^^^^^^

On the System Console, from the left menu, go to :guilabel:`Environment`
and :guilabel:`File Storage`. Set the following settings:

* :guilabel:`File System Storage`: Amazon S3
* :guilabel:`Amazon S3 Bucket`: ``mpm-mattermost```
* :guilabel:`Amazon S3 Region`: ``us-east-1``
* :guilabel:`Amazon S3 Access Key ID`: (Your access key id here)
* :guilabel:`Amazon S3 Endpoint`: ``us-east-1.linodeobjects.com``
* :guilabel:`Amazon S3 Secret Access Key`: (Your secret access key here)
* :guilabel:`Enable Secure Amazon S3 Connections`: Yes

Click :guilabel:`Save`, and then :guilabel:`Test Connection`. (The other way
around will not use the new settings in the test.)

Mattermost IRC Integration
------------------------------------

We'll connect to our IRC room via Matterbridge.

Go to the `Matterbridge GitHub Releases <https://github.com/42wim/matterbridge/releases/latest>`_
page and copy the link address for `matterbridge-x.xx.x-linux-64bit`. You'll use
that in the following bash commands, in place of the :code:`URLHERE`. You will
also need to revise the other reference to :code:`matterbridge-x.xx.x-linux-64bit`:

..  code-block::  bash

    $ sudo mkdir /opt/matterbridge
    $ cd /opt/matterbridge
    $ sudo wget URLHERE
    $ sudo mv matterbridge-x.xx.x-linux-64bit matterbridge
    $ sudo chmod a+x /opt/matterbridge
    $ sudo vim /opt/matterbridge/matterbridge.toml

Set the contents of that file to the following, replacing PASSWORD
with the password as appropriate. Notice that we are using dedicated accounts
on both services for Matterbridge's use:

    [irc.myirc]
    Nick="mousepawmedia"
    NickServNick="mousepawmedia"
    NickServPassword="PASSWORD"
    Server="irc.libera.chat:6697"
    UseTLS=true
    UseSASL=true
    SkipTLSVerify=false
    RemoteNickFormat="[{PROTOCOL}] <{NICK}> "

    [mattermost.mymattermost]
    #The mattermost hostname. (do not prefix it with http or https)
    Server="chat.mousepawmedia.com"

    #the team name as can be seen in the mattermost webinterface URL
    #in lowercase, without spaces
    Team="staff"

    #login/pass of your bot.
    #Use a dedicated user for this and not your own!
    Login="developers@mousepawmedia.com"
    Password="PASSWORD"

    RemoteNickFormat="[{PROTOCOL}] <{NICK}> "
    PrefixMessagesWithNick=true

    [[gateway]]
    name="gateway1"
    enable=true

    [[gateway.inout]]
    account="irc.myirc"
    channel="#mousepawmedia"

    [[gateway.inout]]
    account="mattermost.mymattermost"
    channel="town-square"

Save and close. Now run the following to start Matterbridge. Watch
the output here; ensure it works.

..  code-block:: bash

    /opt/matterbridge/matterbridge -conf /opt/matterbridge/matterbridge.toml

If that works, we can disown the process, so it keeps running separate from the
current SSH session...

..  code-block:: bash

    bg
    jobs
    disown -h %1

Now we edit cron to start Matterbridge automatically:

..  code-block:: bash

    $ crontab -e

Add the following line:

..  code-block:: text

    @reboot /opt/matterbridge/matterbridge -conf /opt/matterbridge/matterbridge.toml

Save and close.

SOURCE: `Matterbridge README: Installing / upgrading <https://github.com/42wim/matterbridge/blob/master/README.md#installing--upgrading>`_

SOURCE: `How to create your config (Matterbridge) <https://github.com/42wim/matterbridge/wiki/How-to-create-your-config>`_


LimeSurvey
=================

Let's install LimeSurvey.

Install the dependencies.

..  code-block:: bash

    $ sudo apt install php-gd php-ldap php-zip php-imap php-ldap

Get the download link from `LimeSurvey Community Edition <https://community.limesurvey.org/downloads/>`_,
which we'll use in the next step.

Download and unzip the latest version. Be sure to replace the URL and filename
with the one appropriate for your download. We'll also set our permissions here.

..  note:: If you're paranoid like me about unzipping directly in an important
    directory like ``/opt/password_providers``, you can always unzip it in another folder and
    move the ``limesurvey`` folder to ``/opt/papers``.

..  code-block:: bash

    $ cd /tmp
    $ sudo wget https://download.limesurvey.org/latest-stable-release/limesurvey5.0.8+210712.zip
    $ sudo unzip limesurvey2.67.2+170719.zip
    $ sudo mv /tmp/limesurvey /opt/papers
    $ sudo chown -R mpm:www-data /opt/papers
    $ sudo chmod -R 755 /opt/papers
    $ sudo chmod -R 775 /opt/papers/tmp
    $ sudo chmod -R 775 /opt/papers/upload
    $ sudo chmod -R 775 /opt/papers/application/config

We gave the webserver group write permissions to three specific directories,
and read-only access to the rest of the folders for LimeSurvey.

Apache2
---------------

Let's set up the subsite for LimeSurvey. Before proceeding, ensure you've
created the certificate for the domain you're using via the Certbot steps
described earlier in this document.

We must allow Apache2 to access the directory we just set up:

..  code-block:: bash

    $ sudo vim /etc/apache2/apache2.conf

Add the following Directory block to that file:

..  code-block:: apache

    <Directory /opt/papers/>
        Options FollowSymLinks
        AllowOverride all
        Require all granted
    </Directory>

Save and close. Then, we create the Apache2 site.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/papers.conf

Set the contents of that file to the following:

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost papers.mousepawmedia.com:443>
            ServerName papers.mousepawmedia.com

            ServerAdmin webmaster@mousepawmedia.com
            DocumentRoot /opt/papers

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            <Directory /opt/papers>
                    Options -MultiViews -Indexes
                    AllowOverride All
            </Directory>

            # prevent iframing this site
            #Header set X-Frame-Options DENY

            # SSL
            SSLEngine on
            SSLCertificateFile      /etc/letsencrypt/live/papers.mousepawmedia.com/fullchain.pem
            SSLCertificateKeyFile   /etc/letsencrypt/live/papers.mousepawmedia.com/privkey.pem

            Include /etc/letsencrypt/options-ssl-apache.conf

            <FilesMatch "\.(cgi|shtml|phtml|php)$">
                    SSLOptions +StdEnvVars
            </FilesMatch>
            <Directory /usr/lib/cgi-bin>
                    SSLOptions +StdEnvVars
            </Directory>
        </VirtualHost>
    </IfModule>

Save and close. Now we can enable the site.

..  code-block:: bash

    $ sudo a2ensite survey
    $ sudo systemctl restart apache2

The site should now be accessible at ``papers.mousepawmedia.com``. However,
before continuing with the installer, we need to set up a couple more things.

Database
--------------

Let's set up the LimeSurvey database:

..  code-block:: bash

    $ sudo mysql -u root

Run the following:

..  code-block:: text

    CREATE USER 'limesurvey'@'localhost' IDENTIFIED BY 'some_password';
    CREATE DATABASE IF NOT EXISTS limesurvey;
    GRANT SELECT, CREATE, INSERT, UPDATE, DELETE, ALTER, DROP, INDEX ON limesurvey.* TO 'limesurvey'@'localhost' WITH GRANT OPTION;
    FLUSH privileges;
    \q

LDAP
----------------------

We want Limesurvey to use LDAP for user authentication.

Setup
----------

Go to ``https://survey.mousepawmedia.com`` and follow the setup wizard.

On the new installation, log in with the administrator account you set up
in the setup wizard.

Go to :guilabel:`Configuration -> Settings -> Global`, and set the following:

* :guilabel:`Email settings`:
  * :guilabel:`Email method`: ``SMTP``
  * :guilabel:`SMTP host`: ``mail.mousepawmedia.com:465``
  * :guilabel:`SMTP username`: ``noreply@mousepawmedia.com``
  * :guilabel:`SMTP password`: (the password for the email account)
  * :guilabel:`SMTP encryption`: ``SSL/TLS``
* :guilabel:`Bounce settings`:
  * :guilabel:`Default site bounce email`: ``noreply@mousepawmedia.com``
  * :guilabel:`Server type`: ``IMAP``
  * :guilabel:`Server name & port`: ``mail.mousepawmedia.com:993``
  * :guilabel:`User name`: ``noreply@mousepawmedia.com``
  * :guilabel:`Password`: (the password for the email account)
  * :guilabel:`Encryption type`: ``SSL/TLS``
* Security
  * :guilabel:`Force HTTPS`: ``On``

Be sure to save your settings changes.

`SOURCE: Installation (LimeSurvey Manual) <https://manual.limesurvey.org/Installation>`_

LDAP
--------------------

Go to :guilabel:`Configuration -> Plugins` and enable :guilabel:`AuthLDAP`.
Click the title of the plugin to go to its overview, and then click
:guilabel:`Settings`.

Set the following settings:

* :guilabel:`LDAP server`: ``ldap://id.mousepawmedia.com``
* :guilabel:`Port number`: ``389``
* :guilabel:`LDAP version`: ``LDAPv3``
* :guilabel:`Enable Start-TLS`: ``Yes``
* :guilabel:`Select how to perform authentication`: ``Search and bind``
* :guilabel:`Attribute to compare to the given login`: ``uid``
* :guilabel:`Base DN for the user search operation`: ``ou=Users,dc=id,dc=mousepawmedia,dc=com``
* :guilabel:`Optional extra LDAP filter to be ANDed...`: ``(objectClass=inetOrgPerson)``
* :guilabel:`LDAP attribute of email address`: ``mail``
* :guilabel:`LDAP attribute of full name`: ``cn``
* :guilabel:`Check to make default authentication method`: ``Yes``
* :guilabel:`Automatically create user if it exists in LDAP server`: ``Yes``
* :guilabel:`Grant survey creation permission to automatically created users`: ``No``
* :guilabel:`Optional base DN for group restriction`: ``ou=Groups,dc=id,dc=mousepawmedia,dc=com``
* :guilabel:`Optional filter for group restriction`: ``(&(|(cn=management)(cn=hiring)(cn=admin))(memberUid=$username))``
* :guilabel:`Allow initial user to login via LDAP`: ``No``
