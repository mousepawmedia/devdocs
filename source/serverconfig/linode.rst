Server Configuration: Linode
##############################################

This documentation outlines how the Linode "Webster" server was built.

Initial Setup
==============================================

We are using a Linode 1024 with an Ubuntu 16.04 disk instance deployed
from the ``Rebuild`` pane on the Linode dashboard.

Using LISH, we will log in as the root user.

Setting Hostname and Timezone
--------------------------------

We'll start by defining the name of our host.

..  code-block:: bash

    # echo "delavega" > /etc/hostname
    # hostname -F /etc/hostname
    # vim /etc/hosts

In that file, add ``delavega`` to the end of the second line.

Save and quit.

Now we set the timezone.

..  code-block:: bash

    # dpkg-reconfigure tzdata

Use the arrow and ENTER keys to set your timezone.

Reboot the system.

`SOURCE: Getting Started with Linode (Linode) <https://www.linode.com/docs/getting-started>`_

Defining a Non-Root User
----------------------------

Next, we'll use the root account to set up the regular
user account.

..  code-block:: bash

    # adduser webster

Define the password for the new user, and other information if desired.
Then, we add the user to the ``sudo`` group.

..  code-block:: bash

    # usermod -aG sudo webster
    # groups webster

The second command will list all of the groups ``webster`` is in. Ensure
it includes the ``sudo`` group.

Finally, we'll log out of root...

..  code-block:: bash

    # logout

And log in as ``webster``. Once logged in, we can test out the user's sudo
abilities by running...

..  code-block:: bash

    $ sudo echo Hi

If it works, we have set up sudo properly.

Updates
----------------------

Before we continue, we need to check for any updates.

..  code-block:: bash

    $ sudo apt update
    $ sudo apt full-upgrade

Set Up Longview
----------------------

It will be beneficial for us to use the free plan for Longview, a service
provided through Linode. Let's install that now.

On the Linode Manager web interface, go to Longview and add a new client.
After a moment, a white box will pop up with a command to run in the server
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

Next, we'll edit the configuration file to turn off ``KeepAlive``, as that
uses up extra memoery (and we don't have that much to spare).

..  code-block:: apache

    KeepAlive Off

Save and close.

Next, we'll change the settings for the ``mpm_prefork`` module.

..  code-block:: apache

    $ sudo vim /etc/apache2/mods-available/mpm-prefork.conf

Set the file to the following...

..  code-block:: apache

    <IfModule mpm_prefork_module>
            StartServers            2
            MinSpareServers         5
            MaxSpareServers         10
            MaxRequestWorkers       39
            MaxConnectionsPerChild  3000
    </IfModule>

Save and close. Now we'll enable the prefork module.

..  code-block:: bash

    $ sudo a2dismod mpm_event
    $ sudo a2enmod mpm_prefork
    $ sudo systemctl restart apache2

Next, we will add our user to the ``wwww-data`` group, which will be
helpful for permissions, and we'll create the directory we'll be using
for all our websites.

..  code-block:: bash

    $ sudo usermod -aG www-data webster
    $ cd /opt
    $ sudo mkdir html
    $ sudo chown webster:www-data html
    $ sudo chmod 775 html

We need to tell Apache2 to read this directory.

..  code-block:: bash

    $ sudo vim /etc/apache2/apache2.conf

Scroll down to the section with all the directories, and add this entry:

..  code-block:: apache

    <Directory /opt/html/>
            Options FollowSymLinks
            AllowOverride All
            Require all granted
    </Directory>

Save and close, and then restart Apache2.

..  code-block:: bash

    $ sudo systemctl restart apache2

Browse to the web server using whatever address is most convenient,
and ensure the Apache2 default page is appearing.

Adding Sites
----------------------

We'll set up a new VirtualHost, so we can get started. You'll repeat this
process for every unique domain.

..  NOTE:: Since we're transferring a live site from another shared hosting
    service, we need to start with a domain that isn't actively used for
    much. We'll transfer each as we get things set up.

..  code-block:: bash

    $ sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/mousepawgames.net.conf
    $ sudo vim /etc/apache2/sites-available/mousepawgames.net.conf

Set the contents of that file to...

..  code-block:: apache

    <VirtualHost *:80>
            ServerName mousepawgames.net

            ServerAdmin webmaster@mousepawmedia.com
            DocumentRoot /opt/html/mousepawgames.net

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

    </VirtualHost>

Save and close. Now we'll create the folder for the website in ``/opt/html``.

..  code-block:: bash

    $ mkdir /opt/html/mousepawmgames.net

We can also create an ``index.html`` in that folder, with the text "Hello, world!",
just to make sure the site works.

We'll be revisiting this later when we set up the other sites.

MySQL
----------------

Now we will set up our database software.

..  code-block:: bash

    $ sudo apt install mysql-server

When prompted, set the root password.

PHP
----------------

We'll be using PHP 7, which is the default in Ubuntu 16.04, for this server.

..  code-block:: bash

    $ sudo apt install php7.0 php-pear libapache2-mod-php7.0 php7.0-mysql
    $ sudo vim /etc/php/7.0/apache2/php.ini

Edit the contents of that file so the following lines match the given values::

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

Server Hardening
======================

Firewall
-------------------------

We'll enable our firewall, and allow SSH and HTTP(S) through.

..  code-block:: bash

    $ sudo ufw enable

    # Open for SSH
    $ sudo ufw allow 22

    # Open for HTTP
    $ sudo ufw allow 80

    # Open for HTTPS
    $ sudo ufw allow 443

SSH Security
-------------------------

We need to lock down SSH for further security.

..  code-block:: bash

    $ sudo vim /etc/ssh/sshd_config

Edit the file so the following lines have the given settings::

    PermitRootLogin no
    PasswordAuthentication no

Save and close the file, and then run...

..  code-block:: bash

    $ sudo systemctl restart sshd

Secure Shared Memory
--------------------------

..  code-block:: bash

    $ sudo vim /etc/fstab

At the bottom of the file, add the lines::

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

Edit the file, uncommenting or adding the following lines.::

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

Prevent IP Spoofing
-------------------------------------------

To prevent IP spoofing, we edit ``/etc/hosts``.

..  code-block:: bash

    $ sudo vim /etc/host.conf

Add or edit the following lines.

..  code-block:: apache

    order bind,hosts
    nospoof on

Harden PHP
---------------------------------------------

..  code-block:: bash

    $ sudo vim /etc/php/5.6/apache2/php.ini

Add or edit the following lines and save.::

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

Change or add the following lines::

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

Now we'll copy the default configuration and edit it.

..  code-block:: bash

    $ sudo mv /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
    $ sudo vim /etc/modsecurity/modsecurity.conf

Add and edit the lines::

    SecRuleEngine On
    SecServerSignature FreeOSHTTP
    SecRequestBodyLimit 33554432
    SecRequestBodyInMemoryLimit 33554432

..  index:: file size limits

Those last two lines define the maximum upload size in *bytes*. At the moment,
we're setting the limit to **32 MB**.

Now we download the latest OWASP security rules.

..  code-block:: bash

    $ cd /etc/modsecurity
    $ sudo git clone https://github.com/SpiderLabs/owasp-modsecurity-crs.git
    $ cd owasp-modsecurity-crs
    $ sudo cp crs-setup.conf.example crs-setup.conf
    $ cd rules
    $ sudo cp REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf
    $ sudo cp RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf.example RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf


Edit the configuration for the ModSecurity Apache module...

..  code-block:: bash

    $ sudo vim /etc/apache2/mods-available/security2.conf

Add the following line just below the other ``IncludeOptional`` directive.

..  code-block:: apache

    IncludeOptional /etc/modsecurity/owasp-modsecurity-crs/crs-setup.conf
    IncludeOptional /etc/modsecurity/owasp-modsecurity-crs/rules/*.conf

Enable the modules and restart Apache2, ensuring that it still works.

..  code-block:: bash

    $ sudo a2enmod headers
    $ sudo a2enmod security2
    $ sudo systemctl restart apache2

Finally, to make sure it works, go to ``http://<serveraddress>/?param="><script>alert(1);</script>``.
Check ``/var/log/apache2/error.log`` for an error report from ``mod_security``.
If one is there, the configuration worked!


`SOURCE: owasp-modsecurity-crs INSTALL <https://github.com/SpiderLabs/owasp-modsecurity-crs/blob/v3.0/master/INSTALL>`_

Setup ModEvasive
--------------------------------------------

To harden against DDoS attacks, we'll install ModEvasive.

..  code-block:: bash

    $ sudo apt install libapache2-mod-evasive

For the ``Postfix Configuration``, select ``Local Only`` and use the default
FQDN (``ubuntu.members.linode.com``). We **will** be changing this later.

Now we'll create the log directory for ModEvasive and set its permissions
accordingly.

..  code-block:: bash

    $ sudo mkdir /var/log/mod_evasive
    $ sudo chown www-data:www-data /var/log/mod_evasive/

Edit the ModEvasive configuration file...

..  code-block:: bash

    $ sudo vim /etc/apache2/mods-available/evasive.conf

Modify the file to match the following.

..  code-block:: apache

    <ifmodule mod_evasive20.c>
       DOSHashTableSize 3097
       DOSPageCount  15
       DOSSiteCount  50
       DOSPageInterval 1
       DOSSiteInterval  1
       DOSBlockingPeriod  10

       DOSLogDir   /var/log/mod_evasive
       DOSEmailNotify  webster@ubuntu.members.linode.com
       DOSWhitelist   127.0.0.1
    </ifmodule>

There is also a bug reported for Ubuntu 12.04 regarding email. I don't know
if it's fixed, but the workaround doesn't hurt anything anyway.

..  code-block:: bash

    $ sudo ln -s /etc/alternatives/mail /bin/mail/

Enable the modules and restart Apache2, ensuring that it still works.

..  code-block:: bash

    $ sudo a2enmod evasive
    $ sudo systemctl restart apache2

`Read the Docs <https://www.linode.com/docs/websites/apache-tips-and-tricks/modevasive-on-apache/>`_

Setup DenyHosts
--------------------------------------------

DenyHosts blocks SSH attacks and tracks suspicious IPs.

..  code-block:: bash

    $ sudo apt install denyhosts
    $ sudo vim /etc/denyhosts.conf

Edit the following lines.::

    ADMIN_EMAIL = hawksnest@localhost
    SMTP_FROM = DenyHosts
    SYSLOG_REPORT = YES

Setup Fail2Ban
-----------------------------------------------

Fail2Ban does much the same things as DenyHosts, but its coverage includes
Apache, FTP, and other things.

..  code-block:: bash

    $ sudo apt install fail2ban
    $ sudo vim /etc/fail2ban/jail.conf

To turn on various "jails", scroll down to the ``# JAILS`` section. Place
``enabled = true`` under each jail name you want turned on. This is the list
of jails we enabled:

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

We also need to modify a file for ``apache-fakegooglebot`` to work around a bug.
If you run ``python -V`` and it reports a version of Python2 (which it almost
certainly will), run...

..  code-block:: bash

    $ sudo vim /etc/fail2ban/filter.d/ignorecommands/apache-fakegooglebot

Change the first line to ``#!/usr/bin/python3``, and then save and close.

`SOURCE: Fail2Ban fakegooglebot Jail Bug (Shell and Co) <https://www.shellandco.net/fail2ban-fakegooglebot-jail-bug/>`_

Finally, restart the fail2ban process.

..  code-block:: bash

    $ sudo systemctl restart fail2ban

Setup PSAD
------------------------------------------

..  code-block:: bash

    $ sudo apt install psad
    $ sudo vim /etc/psad/psad.conf

Change "EMAIL_ADDRESS" to ``webster@localhost`` and "HOSTNAME" to
``delavega``.

..  code-block:: bash

    $ sudo iptables -A INPUT -j LOG
    $ sudo iptables -A FORWARD -j LOG
    $ sudo ip6tables -A INPUT -j LOG
    $ sudo ip6tables -A FORWARD -j LOG
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
so it looks something like this::

    After=network.target iptables.service firewalld.service ufw.service psad.service

Save and close, and then reload the daemons for systemctl and restart fail2ban.

..  code-block:: bash

    $ sudo systemctl daemon-reload
    $ sudo systemctl restart fail2ban

Now we need to adjust the UFW settings.

..  code-block:: bash

    $ sudo ufw logging high
    $ sudo vim /etc/ufw/before.rules

Add the following lines before the final commit message.::

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

We have a script set up on the system that runs the following...

..  code-block:: bash

    #!/bin/bash
    sudo ckrootkit
    sudo rkhunter --update
    sudo rkhunter --propupd
    sudo rkhunter --check --cronjob -l
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

Server Controls
============================================

PHPMyAdmin
---------------------------------------------

..  code-block:: bash

    $ sudo apt-get update
    $ sudo apt-get install phpmyadmin

On the configuration dialog, select ``apache2`` by selecting it and tapping
:kbd:`Space`. Enter an application password (different from the MySQL root
password) and confirm it.

Now enable two necessary PHP modules and restart Apache2.

..  code-block:: bash

    $ sudo phpenmod mcrypt
    $ sudo phpenmod mbstring
    $ sudo systemctl restart apache2

Validate that you can ``http://<serveraddress>/phpmyadmin``.

Email Server
===============================================

Postfix Setup
------------------

These instructions assume you've already configured your DNS correctly,
according to Linode's instructions. (See the SOURCE at the bottom of this
section.)

We'll start by installing the necessary software.

..  code-block:: bash

    $ sudo apt install postfix postfix-mysql dovecot-core dovecot-imapd dovecot-pop3d dovecot-lmtpd dovecot-mysql
    $ sudo dpkg-configure postfix

This time, select the following options:

- Internet Site
- ``mousepawgames.net``
- ``webster``
- ``$myhostname, localhost, delavega, ubuntu.members.linode.com, mousepawmedia.com, mousepawgames.com, indeliblebluepen.com``
- Force Synchronous Updates? **No**
- (Default)
- ``0``
- ``+``
- ``all``

Postfix is now configured.

MySQL Setup
---------------------

Now we can set up the MySQL database. Replace ``password`` with a unique
password.

..  code-block:: bash

    $ mysqladmin -u root -p create mailserver
    $ mysql -u root -p mailserver

..  code-block:: sql

    GRANT SELECT ON mailserver.* TO 'postmaster'@'localhost' IDENTIFIED BY 'password';
    FLUSH PRIVILEGES;

    CREATE TABLE `virtual_domains` (
      `id` int(11) NOT NULL auto_increment,
      `name` varchar(50) NOT NULL,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    CREATE TABLE `virtual_users` (
      `id` int(11) NOT NULL auto_increment,
      `domain_id` int(11) NOT NULL,
      `password` varchar(106) NOT NULL,
      `email` varchar(100) NOT NULL,
      PRIMARY KEY (`id`),
      UNIQUE KEY `email` (`email`),
      FOREIGN KEY (domain_id) REFERENCES virtual_domains(id) ON DELETE CASCADE
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    CREATE TABLE `virtual_aliases` (
      `id` int(11) NOT NULL auto_increment,
      `domain_id` int(11) NOT NULL,
      `source` varchar(100) NOT NULL,
      `destination` varchar(100) NOT NULL,
      PRIMARY KEY (`id`),
      FOREIGN KEY (domain_id) REFERENCES virtual_domains(id) ON DELETE CASCADE
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

We'll define the domains we'll have email addresses for.

..  code-block:: sql

    INSERT INTO `mailserver`.`virtual_domains`
      (`id` ,`name`)
    VALUES
      ('1', 'mousepawmedia.com'),
      ('2', 'mousepawgames.com'),
      ('3', 'mousepawgames.net'),
      ('4', 'indeliblebluepen.com');

    INSERT INTO `mailserver`.`virtual_users`
      (`id`, `domain_id`, `password` , `email`)
    VALUES
      ('1', '3', ENCRYPT('password123', CONCAT('$6$', SUBSTRING(SHA(RAND()), -16))), 'test@mousepawgames.net');

We can also define aliases like this.

..  code-block:: sql

    INSERT INTO `mailserver`.`virtual_aliases`
      (`id`, `domain_id`, `source`, `destination`)
    VALUES
      ('1', '1', 'test2@mousepawgames.net', 'test@mousepawgames.net');

We can confirm this all works by querying each table and verifying the contents.

..  code-block:: sql

    SELECT * FROM mailserver.virtual_domains;
    SELECT * FROM mailserver.virtual_users;
    SELECT * FROM mailserver.virtual_aliases;

Finally, if everything looks good, we exit SQL.

..  code-block:: sql

    exit

Postfix Configuration, Round Two
-------------------------------------

We need to further adjust Postfix. We'll make a backup of the configuration
file, and then start making our edits.

..  code-block:: bash

    $ sudo cp /etc/postfix/main.cf /etc/postfix/main.cf.orig
    $ sudo vim /etc/postfix/main.cf

`SOURCE: Email with Postfix, Dovecot, and MySQL (Linode) <https://www.linode.com/docs/email/postfix/email-with-postfix-dovecot-and-mysql>`_
