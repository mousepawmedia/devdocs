Server Configuration: Hawksnest
##############################################

This documentation outlines how the Hawksnest server was built.

Initial Installation
================================================

Installed Ubuntu 20.04 LTS x64 Server.

..  NOTE: Originally installed Ubuntu 16.04, upgraded via do-release-upgrade.
    We've attempted to document all changes, but there may still be some
    discrepancies.

* Hostname: ``hawksnest-server``

* Full name for user: ``Hawksnest``

* Username: ``hawksnest``

* Do **NOT** encrypt home folder.

* Set time zone to Pacific time.

* Partition Disks: **Guided - use entire disk** (not using LVM)

* No HTTP proxy.

* Selected ``Install secure updates automatically``.

* Selected the following default packages:

    * ``OpenSSH Server``

    * ``LAMP server``

    * ``Manual package selection``

* Install GRUB bootloader? ``Yes``

..  NOTE:: Set passwords for both the ``hawksnest`` user and the MySQL root user.

Updates
===================================================

..  code-block:: bash

    # Install initial updates.
    $ sudo apt update
    $ sudo apt dist-upgrade

FSTAB
====================================================

Because we have another hard drive for overflow and file storage, we need to
add it to FSTAB. In our case, the partition is ``/dev/sda5``, although it is
liable to change later when we've finished our setup, ensured everything is
moved over, and could reformat to use the *entire* disk.

We're also going to mount our backup drive.

First, we get the info about the attached drives.

..  code-block:: bash

    # NOTE: The argument is lowercase "L", NOT the numeral one.
    $ sudo fdisk -l

This will show us the names, sizes, and types for the drives. The ``Linux``
type is generally ``ext4``. We'll substitute the appropriate values in the
commands below.

..  code-block:: bash

    $ sudo mkdir /mnt/stash
    $ sudo mkdir /mnt/backup
    $ sudo mount -t ext4 /dev/sda5 /mnt/stash
    $ sudo mount -t ext4 /dev/sdc1 /mnt/backup

Now navigate to ``/mnt/stash`` and ``/mnt/backup``, confirming that each
mounted correctly. If so, we can unmount and edit ``fstab`` so that the
mounts will be performed at startup.

..  code-block:: bash

    $ sudo umount /mnt/stash
    $ sudo umount /mnt/backup
    $ sudo vim /etc/fstab

Add the following lines, substituting the values from the earlier ``mount``
commands as appropirate::

    /dev/sda5 /mnt/stash ext4 defaults 0 2
    /dev/sdc1 /mnt/backup ext4 defaults 0 2

Save and close. Finally, run the following to remount using fstab. This
command will automatically skip over any drives already mounted, such as
your base file system, so it's safe to run.

..  code-block:: bash

    $ sudo mount -a

Double check ``/mnt/stash`` and ``/mnt/media`` to ensure they mounted correctly.

Logs and Scripts
===================================================

Let's define a folder in ``/opt`` for our own logs.

..  code-block:: bash

    $ sudo mkdir /opt/log
    $ sudo chown hawksnest /opt/log
    $ sudo chmod u=rwx,g=rwx,o=rx /opt/log

Let's also define a folder for root scripts.

..  code-block:: bash

    $ sudo mkdir /opt/scripts
    $ sudo chown root /opt/scripts
    $ sudo chgrp root /opt/scripts
    $ sudo chmod u=rwx,g=rwx,o=rx /opt/scripts

Automatic Updates
===============================

We need to install a couple of packages for managing updates automatically.

..  code-block:: bash

    $ sudo apt install unattended-upgrades apticron
    $ sudo vim /etc/apt/apt.conf.d/50unattended-upgrades

By default, only the system and security updates will be automatically
installed. We can uncomment additional changes here as well.

..  IMPORTANT:: Those are variables in the file. Don't hard-code the
    distro_id and distro_codename.

Once you're happy with that file, save and close. Next, we'll configure
the schedule.

..  code-block:: bash

    $ sudo vim /etc/apt/apt.conf.d/10periodic

Set the contents of that file::

    APT::Periodic::Update-Package-Lists "1";
    APT::Periodic::Download-Upgradeable-Packages "1";
    APT::Periodic::AutocleanInterval "7";
    APT::Periodic::Unattended-Upgrade "1";

Save and close. Now our system will automatically update and install the
types of packages designated in the other file.

Finally, let's configure apticon to notify us if something goes wrong.

..  code-block:: bash

    $ sudo vim /etc/apticron/apticron.conf

Set ``EMAIL`` to ``"hawksnest@localhost"``. Save and close.

LAMP Server
===================================================

PHP7.4
----------------------------------------------------

..  code-block:: bash

    $ sudo apt install php7.4

    # Verify that PHP is on version 7.4
    $ sudo php -v

    # Install the libapache module.
    $ sudo apt install libapache2-mod-php7.4

    # Install the needed PHP packages.
    $ sudo apt install php7.4-cli php7.4-common php7.4-curl php7.4-dev php7.4-gd php-gettext php7.4-json php7.4-mbstring php7.4-mysql php7.4-opcache php7.4-readline php7.4-xml

    # Finally, update the alternatives.
    $ sudo update-alternatives --config php
    # Select the option for php7.4

`SOURCE: How Do I Install Different PHP Version? (Ask Ubuntu) <http://askubuntu.com/a/109544/23786>`_

..  NOTE:: We are not installing APC because it is not supported on PHP 7.4
    or above. ``php7.4-opcache`` handles that now.

SSH
------------------------------------------

..  NOTE:: For security reasons, the SSH port we changed to has been
    scrubbed to ``123``.

The necessary packages for SSH were installed on installation. We need to
change a few settings and start the service.

..  code-block:: bash

    # Start the service to make sure it works.
    $ sudo systemctl start ssh

    # Make a backup of the default SSH configuration.
    $ sudo cp /etc/ssh/sshd_config{,.bak}

    # Edit the SSH configuration.
    $ sudo vim /etc/ssh/sshd_config

Change ``Port 22`` to ``Port 123`` and ``PermitRootLogin prohibit-password`` to
``PermitRootLogin no``. Also add the line ``DebianBanner no`` (you can put it under
"PermitRootLogin"). The rest of the default settings should be fine.
Save and close the file.

Now we'll restart the service.

..  code-block:: bash

    # Restart the SSH service.
    $ sudo systemctl restart ssh

On the **remote machine** (the computer you're connecting *from*), run...

..  code-block:: bash

    ssh-copy-id hawksnest.ddns.net -p 123

You can now connect to the server via SSH.

`SOURCE: How to Use SSH To Connect To A Remote Server (Digital Ocean) <https://www.digitalocean.com/community/tutorials/how-to-use-ssh-to-connect-to-a-remote-server-in-ubuntu>`_

MySQL
-----------------------------------------

..  NOTE: We originally used MySQL 5, but now *must* use MySQL 8 to remain
    compatible with some updated applications.

By default, we can only have 100 simultaneous connections to the MySQL database.
That may seem like a lot, but considering how many services we'll be running,
it's actually quite small.

We need to edit the following file...

..  code-block:: bash

    $ sudo vim /etc/mysql/mysql.conf.d/mysqld.conf

Look for the following line, uncomment it (remove the leading :code:`#`), and
edit it to match the value shown here::

    max_connections = 1000

Save and close, and then restart the MySQL service.

..  code-block:: bash

    $ sudo systemctl restart mysql

Java
-----------------------------------------

We're going to need Java for a few things, so let's install that now.

..  code-block:: bash

    $ sudo apt install default-jdk

Server Hardening
===========================================

Let's improve our system security before continuing.

`SOURCE: How To Secure Ubuntu 16.04 LTS Server (The Fan Club) <https://www.thefanclub.co.za/how-to/how-secure-ubuntu-1604-lts-server-part-1-basics>`_

Firewall
--------------------------------------------

We need to first enable the firewall. We can add rules as needed.

..  code-block:: bash

    # Enable firewall.
    $ sudo ufw enable

    # Open our port for HTML
    $ sudo ufw allow 80

    # Open our special SSL port from the previous step.
    $ sudo ufw allow 123

From your remote machine, test SSH again. Also, enter the server's IP address
in a web browser to make sure you get the Apache default page (on port 80).

Secure Shared Memory
------------------------------------------------

..  code-block:: bash

    $ sudo vim /etc/fstab

At the bottom of the file, add the lines::

    # Secure shared memory
    tmpfs /run/shm tmpfs defaults,noexec,nosuid 0 0

Save and close the file, and then restart the computer.

Lock Down ``sudo`` Privilege
--------------------------------------------------

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

    # Disable source packet routing
    net.ipv4.conf.all.accept_source_route = 0
    net.ipv6.conf.all.accept_source_route = 0

    # Ignore send redirects
    net.ipv4.conf.all.send_redirects = 0

    # Block SYN attacks
    net.ipv4.tcp_syncookies = 1

    # Log Martians
    net.ipv4.conf.all.log_martians = 1

    # Ignore ICMP redirects
    net.ipv4.conf.all.accept_redirects = 0
    net.ipv6.conf.all.accept_redirects = 0

Finally, reload ``sysctl``. If there are any errors, fix the associated lines.

..  code-block:: bash

    $ sudo sysctl -p

Prevent IP Spoofing
-------------------------------------------

To prevent IP spoofing, we edit ``/etc/hosts``.

..  code-block:: bash

    $ sudo vim /etc/host.conf

Add or edit the following line.

..  code-block:: apache

    order bind,host

Harden PHP
---------------------------------------------

..  code-block:: bash

    $ sudo vim /etc/php/7.4/apache2/php.ini

Add or edit the following lines and save.::

    register_globals = Off
    expose_php = Off
    display_errors = Off
    track_errors = Off
    html_errors = Off
    magic_quotes_gpc = Off
    mail.add_x_header = Off
    session.name = MPMSESSID
    memory_limit = 512M

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

    $ cd /tmp
    $ sudo wget -O SpiderLabs-owasp-modsecurity-crs.tar.gz https://github.com/SpiderLabs/owasp-modsecurity-crs/tarball/master
    $ sudo tar -zxvf SpiderLabs-owasp-modsecurity-crs.tar.gz
    $ sudo cp -R SpiderLabs-owasp-modsecurity-crs-*/* /etc/modsecurity/
    $ sudo rm SpiderLabs-owasp-modsecurity-crs.tar.gz
    $ sudo rm -R SpiderLabs-owasp-modsecurity-crs-*
    $ sudo cp /etc/modsecurity/modsecurity_crs_10_setup.conf.example /etc/modsecurity/modsecurity_crs_10_setup.conf

    $ cd /etc/modsecurity/base_rules
    $ for f in * ; do sudo ln -s /etc/modsecurity/base_rules/$f /etc/modsecurity/activated_rules/$f ; done
    $ cd /etc/modsecurity/optional_rules
    $ for f in * ; do sudo ln -s /etc/modsecurity/optional_rules/$f /etc/modsecurity/activated_rules/$f ; done

Edit the configuration for the ModSecurity Apache module...

..  code-block:: bash

    $ sudo vim /etc/apache2/mods-available/security2.conf

Add the following line just below the other ``IncludeOptional`` directive.

..  code-block:: apache

    IncludeOptional /etc/modsecurity/activated_rules/*.conf

Enable the modules and restart Apache2, ensuring that it still works.

..  code-block:: bash

    $ sudo a2enmod headers
    $ sudo a2enmod security2
    $ sudo systemctl restart apache2

..  WARNING:: While we followed the above to set this up, these instructions
    no longer work. See the Linode server configuration for a working (as of
    writing) version.

Setup ModEvasive
--------------------------------------------

To harden against DDoS attacks, we'll install ModEvasive.

..  code-block:: bash

    $ sudo apt install libapache2-mod-evasive

For the ``Postfix Configuration``, select ``Local Only`` and use the default
FQDN (``hawksnest-server.netgear.com``).

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
       DOSEmailNotify  hawksnest@hawksnest-server.netgear.com
       DOSWhitelist   127.0.0.1
       DOSWhitelist   192.168.1.*
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

Save and close.

We also need to modify a file for ``apache-fakegooglebot`` to work around a bug.
If you run ``python -V`` and it reports a version of Python2 (which it almost
certainly will), run...

..  code-block:: bash

    $ sudo vim /etc/fail2ban/filter.d/ignorecommands/apache-fakegooglebot

Change the first line to ``#!/usr/bin/python3``, and then save and close.

`SOURCE: Fail2Ban fakegooglebot Jail Bug (Shell and Co) <https://www.shellandco.net/fail2ban-fakegooglebot-jail-bug/>`_
`SOURCE: Client denied by server configuration: .ocdata (Nextcloud) <https://help.nextcloud.com/t/client-denied-by-server-configuration-ocdata/13250/8>`_

Finally, restart the fail2ban process.

..  code-block:: bash

    $ sudo systemctl restart fail2ban

Setup PSAD
------------------------------------------

..  code-block:: bash

    $ sudo apt install psad
    $ sudo vim /etc/psad/psad.conf

Change the following lines in that file
(they probably aren't all next to each other):

..  code-block::

    EMAIL_ADDRESSES             hawksnest@localhost;

    HOSTNAME                    hawksnest-server;

    HOME_NET                    192.168.0/24;;
    EXTERNAL_NET                any;

    ENABLE_SYSLOG_FILE          Y;
    IPT_WRITE_FWDATA            Y;
    IPT_SYSLOG_FILE             /var/log/syslog;

    ENABLE_AUTO_IDS Y;

Save and close.

In Ubuntu 20.04, there's a bug that must be corrected with this:

..  code-block:: bash

    $ sudo ln -s /usr/sbin/iptables /sbin/iptables
    $ sudo ln -s /usr/sbin/ip6tables /sbin/ip6tables

`SOURCE: github.com/mrash/psad/issues/76 <https://github.com/mrash/psad/issues/76>`_

Then run...

..  code-block:: bash

    $ sudo iptables -A INPUT -j LOG
    $ sudo iptables -A FORWARD -j LOG
    $ sudo ip6tables -A INPUT -j LOG
    $ sudo ip6tables -A FORWARD -j LOG
    $ sudo psad -H
    $ sudo psad --sig-update
    $ sudo psad -R
    $ sudo psad --Status

When you run that last command, it may whine about not finding a pidfile.
It appears we can ignore that error.

`SOURCE: How to install and use PSAD IDS on Ubuntu Linux (Rapid7 Blog) <https://blog.rapid7.com/2017/06/24/how-to-install-and-use-psad-ids-on-ubuntu-linux/>`_

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

Now enable one necessary PHP module and restart Apache2.

..  code-block:: bash

    $ sudo phpenmod mbstring
    $ sudo systemctl restart apache2

..  NOTE:: This previously required mcrypt, which is lacking in PHP7.4.
    However, it appears to be operating as expected without it.

Test Apache2 again, as always.

Next, we'll lock PHPMyAdmin down so it can only be used by anyone accessing
on the local network. This can be accomplished by modifying the Apache2
configuration for PHPMyAdmin.

..  code-block:: bash

    $ sudo vim /etc/apache2/conf-available/phpmyadmin.conf

Adjust the ``<Directory /usr/share/phpmyadmin>`` section to look like this.

..  code-block:: apache

    Options SymLinksIfOwnerMatch
    DirectoryIndex index.php
    AllowOverride none

    Order deny,allow
    Deny from all
    Allow from 127.0.0.1
    Allow from 192.168.1.0/24

Restart the Apache2 server...

..  code-block:: bash

    $ sudo systemctl restart apache2

And then validate that you can ``http://<serveraddress>/phpmyadmin``.

..  WARNING:: You may need to disable the Apache2 module ``security2``
    before you can access PHPMyAdmin. Otherwise, it throws an internal 404.
    We're not sure why. To fix the problem, run ``sudo a2dismod security2`` and
    restart the Apache2 service.

DDClient
---------------------------------------

In addition to installing ``ddclient``, we'll install a couple more tools we'll
be using later.

..  code-block:: bash

    $ sudo apt-get install iptraf ddclient screen
    $ sudo vim /etc/ddclient.conf

In the DDClient configuration file we just opened, set the following lines.::

    use=web. web='http://ip1.dynupdate.no-ip.com/8245/'
    protocol=noip
    ssl=yes
    login='theemailaddressfornoip@example.com'
    password='youknowwhatgoesherewiseguy'

Also, at the end of the file, add::

    mousepawmedia.net,hawksnest.ddns.net,hawksnest.serveftp.com,sparrowsgate.serveminecraft.net

Save and close. Next, we need to change how ``ddclient`` runs.

..  code-block:: bash

    $ sudo vim /etc/default/ddclient

Change the following settings to match the following::

    run_dhclient="true"
    run_ipup="true"
    run_daemon="true"
    daemon_interval="300"

Save and quit. Finally, we restart ``ddclient``.

..  code-block:: bash

    $ sudo systemctl restart ddclient

Let's Encrypt Certificates
============================================

We'll install the Let's Encrypt Certbot, and then create our server
certificates. While we can *technically* install the ``letsencrypt`` package,
it's out of date compared to ``certbot-auto``.

..  code-block:: bash

    $ cd /opt
    $ sudo mkdir certbot
    $ cd certbot
    $ sudo wget https://dl.eff.org/certbot-auto
    $ sudo chmod a+x certbot-auto

Now we'll get our certificates.

..  code-block:: bash

    $ sudo /opt/certbot/certbot-auto certonly -a webroot --webroot-path /var/www/html -d hawksnest.ddns.net
    $ sudo /opt/certbot/certbot-auto certonly -a webroot --webroot-path /var/www/html -d hawksnest.serveftp.com
    $ sudo /opt/certbot/certbot-auto certonly -a webroot --webroot-path /var/www/html -d mousepawmedia.net -d nextcloud.mousepawmedia.net -d phabricator.mousepawmedia.net -d ehour.mousepawmedia.net -d time.mousepawmedia.net -d jenkins.mousepawmedia.net -d registry.mousepawmedia.net -d secure.mousepawmedia.net -d files.mousepawmedia.net -d office.mousepawmedia.net -d sandbox.mousepawmedia.net -d pad.mousepawmedia.net -d ajc.mousepawmedia.net -d ibp.mousepawmedia.net

Of course, we would change the ``hawksnest.ddns.net`` part to match the domain
name we're getting the certificate for.

..  NOTE:: If you're needing to add a subdomain from a certificate,
    use the appropriate command above, and include the :code:`--expand` flag.

Follow the instructions on the screen to complete the process of getting the
certificates. If successful, they can be found (visible only as root) in
:file:`/etc/letsencrypt/live/hawksnest.ddns.net` (change the folder name to
match the domain, of course).

Next, we need to create symbolic links to the certificates so Apache can see
them. We'll be sudoing up to root after creating the directory.

Note we're only doing this for the fallback DNs. We'll need to
do something else for the main ``mousepawmedia.net`` certificate
(see ```Post-Renew Script``).

..  code-block:: bash

    $ sudo mkdir /etc/apache2/ssl
    $ sudo su
    # cd /etc/apache2/ssl
    # mkdir /etc/apache2/ssl/filecert
    # ln -s /etc/letsencrypt/live/hawksnest.ddns.net/cert.pem hawksnest/cert.pem
    # ln -s /etc/letsencrypt/live/hawksnest.ddns.net/chain.pem hawksnest/chain.pem
    # ln -s /etc/letsencrypt/live/hawksnest.ddns.net/fullchain.pem hawksnest/fullchain.pem
    # ln -s /etc/letsencrypt/live/hawksnest.ddns.net/privkey.pem hawksnest/privkey.pem
    # ln -s /etc/letsencrypt/live/hawksnest.serveftp.com/cert.pem filecert/cert.pem
    # ln -s /etc/letsencrypt/live/hawksnest.serveftp.com/chain.pem filecert/chain.pem
    # ln -s /etc/letsencrypt/live/hawksnest.serveftp.com/fullchain.pem filecert/fullchain.pem
    # ln -s /etc/letsencrypt/live/hawksnest.serveftp.com/privkey.pem filecert/privkey.pem
    # exit

The links I just created do indeed work for Apache, even though we cannot
view them without being root.

Post-Renew Script
-----------------------------------------

There are a few things we'll need to do every time the certificate is
renewed. Perhaps most important, we need to copy the certs over to a new
folder and change their permissions, so they can be used by various parts
of our server setup.

We'll start by creating a special group for accessing certificates.

..  code-block:: bash

    $ sudo groupadd certs

Now we'll create a directory for the copied certs, and make the script file.

..  code-block:: bash

    $ cd /etc/apache2/ssl
    $ sudo mkdir mousepawmedia.net
    $ cd mousepawmedia.net
    $ sudo vim renewcert_pre

Put the following contents into that file. Comment out the lines regarding
the sites you do not have. Be sure to uncomment them later!

..  code-block:: bash

    #!/bin/bash

    a2dissite 000-redirect ajc ibp jenkins kimai nextcloud office pad phab phabfile protected registry sandbox
    a2ensite 000-default
    systemctl restart apache2

Save and close. Now, let's create the post script.

..  code-block:: bash

    $ sudo vim renewcert_post

Put the following contents into that file. Comment out the lines regarding
the sites you do not have. Be sure to uncomment them later!

..  code-block:: bash

    #!/bin/bash

    # Work out of the Hawksnest SSL working directory.
    cd /etc/apache2/ssl/mousepawmedia.net

    # Copy the certificates over and update their permissions.
    cp /etc/letsencrypt/live/mousepawmedia.net/*.pem ./
    chgrp certs ./*.pem
    chmod u=rw,g=r,o= ./*.pem

    # Make sure this matches the password specified in JENKINS_ARG for HTTPS at /etc/default/jenkins
    PASS=thepassword

    # We must first remove the old keystore.
    rm ./*.pkcs12
    rm ./*.jks

    # Generate the new keystore using our certificates.
    openssl pkcs12 -inkey privkey.pem -in cert.pem -export -out keys.pkcs12 -passin pass:$PASS -passout pass:$PASS
    keytool -importkeystore -srckeystore keys.pkcs12 -srcstoretype pkcs12 -srcstorepass $PASS -destkeystore keys.jks -keypass $PASS -storepass $PASS -noprompt

    # Update permissions on the keystore.
    chgrp certs ./*.pkcs12
    chgrp certs ./*.jks

    # Restart critical services which use this.
    a2dissite 000-default
    a2ensite 000-redirect ajc ibp jenkins kimai nextcloud office pad phab phabfile protected registry sandbox
    systemctl restart apache2
    #systemctl restart jenkins
    #systemctl restart tomcat

Save and close. Change the script permissions so it can only be read, accessed,
and run by its owner and group (both root).

..  code-block:: bash

    $ sudo chmod u=rwx,g=rwx,o= renewcert_pre
    $ sudo chmod u=rwx,g=rwx,o= renewcert_post

Finally, we'll test the configuration.

..  code-block:: bash

    $ sudo /opt/certbot/certbot-auto renew --dry-run --pre-hook "/etc/apache2/ssl/mousepawmedia.net/renewcert_pre" --post-hook "/etc/apache2/ssl/mousepawmedia.net/renewcert_post"

Scheduling Auto-Renewal
------------------------------------------

Now we need to schedule the autorenewal task.

..  code-block:: bash

    $ sudo crontab -e

Add the following line to the end.::

    57 6 * * * /opt/certbot/certbot-auto renew --pre-hook "/etc/apache2/ssl/mousepawmedia.net/renewcert_pre" --post-hook "/etc/apache2/ssl/mousepawmedia.net/renewcert_post"

This will run the renewal script once a day at 11:42am. (Let's Encrypt asks
that a random time be used by each user, to spread out server load.)

Apache Configuration
--------------------------------------------

Create a new file...

..  code-block:: bash

    $ sudo vim /etc/letsencrypt/options-ssl-apache.conf

Set the contents of that file to...

..  code-block:: apache

    # Baseline setting to Include for SSL sites

    SSLEngine on

    # Intermediate configuration, tweak to your needs
    SSLProtocol all -SSLv2 -SSLv3
    SSLCipherSuite ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA
    SSLHonorCipherOrder on
    SSLCompression off

    SSLOptions +StrictRequire

    # Add vhost name to log entries:
    LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-agent}i\"" vhost_combined
    LogFormat "%v %h %l %u %t \"%r\" %>s %b" vhost_common

    #CustomLog /var/log/apache2/access.log vhost_combined
    #LogLevel warn
    #ErrorLog /var/log/apache2/error.log

    # Always ensure Cookies have "Secure" set (JAH 2012/1)
    #Header edit Set-Cookie (?i)^(.*)(;\s*secure)??((\s*;)?(.*)) "$1; Secure$3$4"

Save and exit.

LDAP Server
===============================================

Installation
------------------------------------

..  code-block:: bash

    $ sudo apt install slapd ldap-utils ldap-account-manager
    $ sudo dpkg-reconfigure slapd

During the configuration, use these settings:

- Omit OpenLDAP server configuration? No
- DNS domain name? ldap.mousepawmedia.net
- Organization name? mousepawmedia
- Administrator password? (enter one)
- Database backend to use? MDB
- Remove the database when slapd is purged? No
- Move old database? Yes
- Allow LDAPv2 protocol? No

`SOURCE: How To Install and Configure A Basic LDAP Server (DigitalOcean) <https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-a-basic-ldap-server-on-an-ubuntu-12-04-vps>`_

LDAP Account Manager Config
-----------------------------------

Now we need to adjust LDAP Account Manager's configuration. Go to
https://mousepawmedia.net/lam and click ``LAM Configuration`` in the upper-right
corner.

Click ``General settings``. If this is your first time logging in, the Master
Password is ``lam``.

On the page that appears, enter a new master password. Be sure not to lose it!

You can adjust other settings here as needed, but the defaults should be fine.

Click ``Ok``.

Now go to ``LAM Configuration`` and ``Edit server profiles``. Click ``Manage
server profiles`` and rename the profile to ``admin``. Click ``OK``.

Now go to ``LAM Configuration`` and ``Edit server profiles``. Log in.

Ensure the following settings on the General settings tab:

* Server address: ``ldap://localhost:389``
* Activate TLS: no
* Tree suffix: ``dc=ldap,dc=mousepawmedia,dc=net``
* Advanced options > Display name: ``PawID``

Scroll down to Security settings. Set the Login method to Fixed list, and then
set ``List of valid users`` to ``cn=admin,dc=ldap,dc=mousepawmedia,dc=net``.

Click ``Save``.

Configuring LDAP Schema
--------------------------------

Go to ``LAM Configuration`` and ``Edit server profiles``. Log in.

Then, go to the Account types tab. Create two account types:

* Users
    * LDAP suffix: ``ou=Users,dc=ldap,dc=mousepawmedia,dc=net``
    * List attributes: ``#uid;#cn;#mail;#uidNumber;#gidNumber``
* Groups
    * LDAP suffix: ``ou=Groups,dc=ldap,dc=mousepawmedia,dc=net``
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
    ``Rename`` from ``cn=name,ou=Users,dc=ldap,dc=mousepawmedia,dc=net`` to
    ``uid=name,ou=Users,dc=ldap,dc=mousepawmedia,dc=net``.

Kimai
===========================================

Installing Composer
------------------------------------

First, we need to install the lastest Composer:

..  code-block:: bash

    $ sudo apt install php-cli unzip
    $ cd /tmp
    $ curl -sS https://getcomposer.org/installer -o composer-setup.php
    $ HASH=`curl -sS https://composer.github.io/installer.sig`
    $ php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"

You should see "Installer verified". If so, we can install.

..  code-block:: bash

    $ sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
    $ composer

If that works, we can move on to installing Kimai.

`SOURCE: How to Install Composer on Ubuntu 20.04 (DigitalOcean) <https://www.digitalocean.com/community/tutorials/how-to-install-composer-on-ubuntu-20-04-quickstart>`_

Installing Kimai
------------------------------------

Next, in MyPHPAdmin, create a user and a database called `kimai2`. You'll
need the password for that user shortly.

We install Kimai like this:

..  code-block:: bash

    $ cd /tmp
    $ git clone -b 1.13 --depth 1 https://github.com/kevinpapst/kimai2.git
    $ sudo mv kimai2 /opt/kimai2
    $ cd /opt/kimai2/
    $ composer install --no-dev --optimize-autoloader
    $ vim .env

Now edit that file so it contains something like the following, changing the
values `CHANGE_ME` (two places below) as appropriate.

..  code-block:: env

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
    MAILER_FROM=hawksnest@mousepawmedia.com

Save and close, and then run the following:

..  code-block:: bash

    $ bin/console kimai:install -n
    $ sudo chown -R hawksnest:www-data .
    $ chmod -R g+r .
    $ chmod -R g+rw var/
    $ chmod -R g+rw public/avatars/

Kimai itself is now installed.

`SOURCE: Installation (Kimai Docs) <https://www.kimai.org/documentation/installation.html>`_

Setting Up Apache2 for Kimai
------------------------------------

    Then, to setup Apache, run the following:

    ..  code-block:: bash

        $ sudo vim /etc/apache2/sites_available/kimai.conf

    Set the contents of that file to:

    ..  code-block:: apache

        <IfModule mod_ssl.c>
            <VirtualHost *:443>
                ServerName time.mousepawmedia.net
                DocumentRoot /opt/kimai2/public

                SSLEngine on
                SSLCertificateFile     /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
                SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem
                Include /etc/letsencrypt/options-ssl-apache.conf

                ErrorLog ${APACHE_LOG_DIR}/error.log
                CustomLog ${APACHE_LOG_DIR}/access.log combined

                <Directory "/opt/kimai2/public">
                    Options +FollowSymLinks
                    AllowOverride All

                    <IfModule mod_dave.c>
                        Dav off
                    </IfModule>

                    Require all granted

                    FallbackResource /index.php
                </Directory>

                <Directory /opt/kimai2>
                    Options FollowSymLinks
                </Directory>

                <Directory /opt/kimai2/public/bundles>
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

        sudo vim /etc/apache2/apache2.conf

    Add the following section:

    ..  code-block:: apache

        <Directory "/opt/kimai2">
            Options FollowSymLinks
            AllowOverride All
            Require all granted
        </Directory>

    Save and close, and then enable the site and restart Apache2:

    ..  code-block:: bash

        $ sudo a2ensite kimai
        $ sudo systemctl restart apache2

Now go to ``https://time.<serveraddress>`` and verify that everything works so far.

`SOURCE: Webserver configuration (Kimai Docs) <https://www.kimai.org/documentation/webserver-configuration.html#apache>`_

LDAP for Kimai
------------------------------------

Let's set up LDAP for Kimai.

,.  code-block:: bash

    $ cd /opt/kimai2
    $ composer update
    $ composer require laminas/laminas-ldap --update-no-dev --optimize-autoloader
    $ vim /opt/kimai2/config/packages/local.yaml

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
                host: 127.0.0.1
                #port: 389
                #bindRequiresDn: true
            user:
                baseDn: ou=Users, dc=ldap, dc=mousepawmedia, dc=net
                usernameAttribute: uid
                filter: (&(objectClass=inetOrgPerson))
                attributes:
                    - { ldap_attr: "usernameAttribute", user_method: setUsername }
                    - { ldap_attr: mail, user_method: setEmail }
                    - { ldap_attr: cn, user_method: setAlias }
            role:
                baseDn: ou=Groups, dc=ldap, dc=mousepawmedia, dc=net
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


`SOURCE: LDAP (Kimai Docs) <https://www.kimai.org/documentation/ldap.html>`_

Reload/Repair Script
--------------------------------------------

Kimai is particularly vulnerable to getting its permissions messed up.
This script will (usually hopefully) fix that:

..  code-block:: bash

    $ vim /opt/kimai2/cache.sh

Set the contents of that file to this:

..  code-block:: bash

    #!/bin/bash

    cd /opt/kimai2

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
    sudo chown -R hawksnest:www-data .
    chmod -R g+r .
    chmod -R 775 var/
    chmod -R g+rw public/avatars/

Save and close, and then make that script executable.

..  code-block:: bash

    $ chmod +x /opt/kimai2/cache.sh

Finally, run the script.

..  code-block:: bash

    $ /opt/kimai2/cache.sh


Phabricator
===========================================

Setting Up System Group and Users
--------------------------------------------

We'll add a group to control who can access Phabricator's stuff. For ease of
use, we'll add our login user to this group. We will also create a new
user called ``phabdaemon`` for Phabricator-based daemons.

..  code-block:: bash

    $ sudo groupadd phab
    $ sudo useradd -G phab phabdaemon
    $ sudo usermod -a -G phab hawksnest
    $ sudo usermod -a -G phab www-data

Now we need to modify the ``phabdaemon`` user.

..  code-block:: bash

    $ sudo vim /etc/passwd

Look for the ``phabdaemon`` entry and set the last field to ``/usr/sbin/nologin``.
Save and close. Then...

..  code-block:: bash

    $ sudo vim /etc/shadow

Look for the ``phabdaemon`` entry again, and set the second field to ``*``. Save
and close.

Migrating
--------------------------------------------

We already had the ``phab``, ``phabfiles`` and ``phabrepo`` folders on the old
installation, so we can move those over to ``/opt``. (See Phabricator's official
installation instructions if you're doing a fresh install.)

Once you've moved the folders over, change their permissions as follows...

..  code-block:: bash

    $ cd /opt
    $ sudo chown -R hawksnest phab
    $ sudo chown -R phabdaemon phabfiles
    $ sudo chown -R phabdaemon phabrepo
    $ sudo chgrp -R phab phab
    $ sudo chgrp -R phab phabfiles
    $ sudo chgrp -R phab phabrepo
    $ sudo chmod u=rwx,g=rwx,o=rx -R phab
    $ sudo chmod u=rwx,g=rwx,o=rx -R phabfiles
    $ sudo chmod u=rwx,g=rwx,o=rx -R phabrepo
    $ /opt/phab/phabricator/bin/repository move-paths --from /home/hawksnest/phabrepo --to /opt/phabrepo

..  NOTE:: That last command migrates where repositories look for files.

We also exported the Phabricator database on the *old* server using...

..  code-block:: bash

    $ cd /home/hawksnest/phab/phabricator
    $ ./bin/storage dump | gzip > /home/hawksnest/backup.sql.gz

On the *new* server, we copy that backup to our ``IMPORTED`` directory, and then
run the following to move it into the new copy of MySQL.

..  code-block:: bash

    $ gunzip -c /home/hawksnest/IMPORTED/backup.sql.gz | mysql -u root -p

Enter the password, and then wait. You might take this opportunity to set
up a chess board and talk about playing badly. Don't count on actually
starting a game.

Configuring Apache
--------------------------------------------

We need to modify a few files to get this working. First, modify ``apache2.conf``.

..  code-block:: bash

    $ sudo vim /etc/apache2/apache2.conf

Near the other ``Directory`` sections, add the section...

..  code-block:: apache

    <Directory "/opt/phab/phabricator/webroot">
            Require all granted
    </Directory>

Next, modify ``ports.conf``...

..  code-block:: bash

    $ sudo vim /etc/apache2/ports.conf

Modify the file so it looks something like this (we only added the ``Port 8446``
lines. Leave the rest alone)...

..  code-block:: apache

    Listen 80

    <IfModule ssl_module>
        Listen 443
        Listen 8446
    </IfModule>

    <IfModule mod_gnutls.c>
        Listen 443
        Listen 8446
    </IfModule>

Finally, add a new site under ``sites-available``.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/phab.conf

Copy and paste the following into that file.

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
                ServerName phabricator.mousepawmedia.net
                ServerAdmin hawksnest@mousepawmedia.com

                DocumentRoot /opt/phab/phabricator/webroot

                RewriteEngine on
                RewriteRule ^/rsrc/(.*)     -                       [L,QSA]
                RewriteRule ^/favicon.ico   -                       [L,QSA]
                RewriteRule ^(.*)$          /index.php?__path__=$1  [B,L,QSA]

                ErrorLog ${APACHE_LOG_DIR}/error.log
                CustomLog ${APACHE_LOG_DIR}/access.log combined

                SSLEngine on
                SSLCertificateFile     /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
                SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem
                Include /etc/letsencrypt/options-ssl-apache.conf

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

        <VirtualHost *:8446>
            ServerName hawksnest.ddns.net
            RedirectMatch ^/(.*)$ https://phabricator.<serveraddress>/$1

            SSLEngine on
            SSLCertificateFile /etc/apache2/ssl/hawksnest/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/hawksnest/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf
        </VirtualHost>
    </IfModule>

Save and close the file. Finally, load them up.

..  code-block:: bash

    $ sudo a2ensite phab
    $ sudo a2enmod ssl
    $ sudo a2enmod php7.4
    $ sudo a2enmod rewrite
    $ sudo ufw allow 8446
    $ sudo systemctl restart apache2

Now see if ``https://<siteaddress>:8446/`` works.

Getting Phabricator Running
-----------------------------------------------------

Next, we need to make some modifications to ``php.ini`` for Phabricator to work.

..  code-block:: bash

    $ sudo vim /etc/php/7.4/apache2/php.ini

Make these changes...

- Comment out ``disable_functions``.

Next, we'll add a new user to MySQL using PHPMyAdmin, and give it
all privileges for the Phabricator databases.

Once we have these changes made, we need to adjust Phabricator's
configuration to access the database.

..  code-block:: bash

    $ ./bin/config set mysql.host localhost
    $ ./bin/config set mysql.user phab
    $ ./bin/config set mysql.pass thepasswordyouset

We also need to change some settings for MySQL:

..  code-block:: bash

    $ sudo vim /etc/mysql/my.cnf

Add or change the following lines in the ``[mysqld]`` section::

    sql_mode=STRICT_ALL_TABLES
    innodb_buffer_pool_size=1600M

Save and close, and then restart MySQL:

..  code-block:: bash

    $ sudo systemctl restart mysql.service

Set Log Locations
---------------------------------------------------

We need to set up the location for logging. We'll create a special folder
in ``/opt`` for this purpose, set its permissions, and tell Phabricator where
to find it.

..  WARNING:: This is critical! If you forget this, you'll have a plethora of
    ``500 Internal Server Error`` messages, an unhandled exception at the bottom
    of all pages, and some missing stuff.

..  code-block:: bash

    $ sudo mkdir /opt/log/phab
    $ sudo chown -R hawksnest /opt/log/phab
    $ sudo chgrp -R phab /opt/log/phab
    $ sudo chmod -R u=rwx,g=rwx,o=rx /opt/log/phab
    $ cd /opt/phab/phabricator
    $ ./bin/config set log.access.path /opt/log/phab/access.log
    $ ./bin/config set log.ssh.path /opt/log/phab/ssh.log
    $ ./bin/config set phd.log-directory /opt/log/phab/phd.log

Addressing Setup Issues
-------------------------------------------------

Various issues may crop up on the install. Once you can access Phabricator,
you can see these at ``https://<serveraddress>:8446/config/issues``.

View each and fix as prescribed. Here are a few fixes we did...

- We made a few changes to ``/etc/php/7.4/apache2/php.ini``.
- We had to make several changes to the MySQL configuration. If you're looking
  for the MySQL configuration file, it's spread out across multiple ``.cnf``
  files in ``/etc/mysql``. Chances are, you want
  ``/etc/mysql/mysql.conf.d/mysqld.cnf``.
- We set the ``php.ini`` value ``date.timezone = `` to ``America/Los_Angeles``.
- We also needed to install the packages ``python3-pygments`` and
  ``python-pygments``.
- We installed the packages ``subversion`` and ``imagemagik``.

Recaptcha
-------------------------------------------------

Sign up for Recaptcha on `their website <https://www.google.com/recaptcha/admin#list>`_
(I'm using indeliblebluepen@gmail.com to admin that). Then, substitute
``PRIVATEKEY`` and ``PUBLICKEY`` in the following commands for the keys you get
from that website.

..  code-block:: bash

    $ ./bin/config set recaptcha.enabled true
    $ ./bin/config set recaptcha.public-key PUBLICKEY
    $ ./bin/config set recaptcha.private-key PRIVATEKEY

Setting Up Alternative File Domain
-------------------------------------------------

Our DNS service and certificates are already set up to provide a second
domain name - ``hawksnest.serveftp.com`` - which we can use as Phabricator's
Alternative File Domain.

We must now configure Apache2 to serve files as expected.

We can copy and tweak the configuration file we used for Phabricator in Apache2.

..  code-block:: bash

    $ cd /etc/apache2/sites-available
    $ sudo cp phab.conf phabfiles.conf
    $ sudo vim phabfile.conf

Set the contents to the following...

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName files.mousepawmedia.net
            ServerAdmin hawksnest@mousepawmedia.com
            #ServerName hawksnest.serveftp.com:8446

            DocumentRoot /opt/phab/phabricator/webroot

            RewriteEngine on
            RewriteRule ^/rsrc/(.*)     -                       [L,QSA]
            RewriteRule ^/favicon.ico   -                       [L,QSA]
            RewriteRule ^(.*)$          /index.php?__path__=$1  [B,L,QSA]

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            SSLEngine on
            SSLCertificateFile  /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf

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


Save and close. Then, run...

..  code-block:: bash

    $ sudo a2ensite phabfiles
    $ sudo systemctl restart apache2

Go to ``https://<filedomainname>:8446``. You **should** see an error on the page
saying "Unhandled Exception ("AphrontMalformedRequestException")"
This means you're on the right track!

Next, we'll configure Phabricator to use this domain name for file serving.

..  code-block:: bash

    $ cd /opt/phab/phabricator
    $ ./bin/config set security.alternate-file-domain https://files.<serveraddress>/

Set Up Phabricator Daemons
-------------------------------------------------

We need to autostart the Phabricator daemons. I wrote a special script that
handles that.

..  code-block:: bash

    $ sudo mkdir /opt/scripts/phab
    $ sudo chown hawksnest /opt/scripts/phab
    $ sudo chgrp phab /opt/scripts/phab
    $ sudo chmod u=rwx,g=rwx,o=rx /opt/scripts/phab
    $ sudo vim /opt/scripts/phab/phd_start

Put the following in that file.

..  code-block:: bash

    #!/bin/bash
    #Start Phabricator daemons

    echo "STARTING PHD" > /opt/log/phab/phd_start.log
    sudo -u phabdaemon /opt/phab/phabricator/bin/phd start > /opt/log/phab/phd_start.log
    sudo -u phabdaemon /opt/phab/phabricator/bin/phd launch phabricatorbot /opt/phab/phabricator/resources/chatbot/botconfig.json > /opt/log/phab/phd_start.log

Save and close. Then, change its permissions.

..  code-block:: bash

    $ sudo chmod u=rwx,g=rwx,o=rx phd_start

Now, add this script to the crontab.

..  code-block:: bash

    $ sudo crontab -e

At the bottom, add the line::

    @reboot sleep 60; /opt/scripts/phab/phd_start

Save and close.

..  NOTE:: It is vital that we sleep for 60 seconds before running, as the
    script fails out of the gate otherwise. (Not sure why.)

Finally, update Phabricator's configuration to expect this user to run
the daemons.

..  code-block:: bash

    $ /opt/phab/phabricator/bin/config set phd.user phabdaemon

Of course, we can run this to start the Phabricator daemons right now...

..  code-block:: bash

    $ sudo /opt/scripts/phab/phd_start

..  NOTE:: If it complains about not being able to modify a path starting with
    ``/var/tmp/phd``, just CAREFULLY run ``sudo rm -r /var/tmp/phd``.

Phabricator Aphlict Notification Server
-------------------------------------------------------

Let's get the notification server for Phabricator running.

We need Node.JS for Aphlict to work. We can install it from the main
package repositories.

..  code-block:: bash

    $ sudo apt install nodejs npm
    $ cd /opt/phab/phabricator/support/aphlict/server/
    $ npm install ws

You can safely ignore the warning messages from ``npm``.

Next, we'll add the ``phabdaemon`` user to the group that can view
the SSL certificates.

..  code-block:: bash

    $ sudo usermod -a -G certs phabdaemon

Now we need to adjust the Aphlict configuration, or it won't start.

..  code-block:: bash

    $ cd /opt/phab/phabricator/conf/aphlict
    $ cp aphlict.default.json aphlict.custom.json
    $ vim aphlict.custom.json

The file should look like this::

    {
      "servers": [
        {
          "type": "client",
          "port": 22280,
          "listen": "0.0.0.0",
          "ssl.key": "/etc/apache2/ssl/mousepawmedia.net/privkey.pem",
          "ssl.cert": "/etc/apache2/ssl/mousepawmedia.net/fullchain.pem",
          "ssl.chain": null
        },
        {
          "type": "admin",
          "port": 22281,
          "listen": "127.0.0.1",
          "ssl.key": null,
          "ssl.cert": null,
          "ssl.chain": null
        }
      ],
      "logs": [
        {
          "path": "/opt/log/phab/aphlict.log"
        }
      ],
      "pidfile": "/var/tmp/aphlict/pid/aphlict.pid"
    }

Finally, open the necessary port and start Aphlict via...

..  code-block:: bash

    $ sudo ufw allow 22280
    $ cd /opt/phab/phabricator
    $ sudo -u phabdaemon ./bin/aphlict start

It should start up without any issues. If there are some, check the previous
steps.

Finally, we need to tell Phabricator to use Aphlict. In Phabricator, go to
Config→All Settings (``https://<serveraddress>:8446/config/all``). Look for
``notification.servers``. Enter the following in the field::

    [
      {
        "type": "client",
        "host": "phabricator.mousepawmedia.net",
        "port": 22280,
        "protocol": "https"
      },
      {
        "type": "admin",
        "host": "127.0.0.1",
        "port": 22281,
        "protocol": "http"
      }
    ]

Navigate to the Notification Servers section of Config
(``https://<serveraddress>/config/cluster/notifications/``) to ensure
the system is running correctly.

If all's well, let's add the Aphlict startup to our PHD start script.

..  code-block:: bash

    $ sudo vim /opt/scripts/phab/phd_start

Add the line...

..  code-block:: bash

    sudo -u phabdaemon /opt/phab/phabricator/bin/aphlict start > /opt/log/phab/phd_start.log

Save and close.

`SOURCE: Notifications Setup and Configuration (Phabricator) <https://secure.phabricator.com/book/phabricator/article/notifications/>`_

Phabricator Git SSH
---------------------------------------------

The system already has a ``www-data`` user, and we set up a ``phabdaemon`` user
earlier. We'll use both of those for use for this. We also need to add a ``git``
user, and then give these users appropriate sudo permissions.

..  code-block:: bash

    $ sudo useradd -m git
    $ /opt/phab/phabricator/bin/config set diffusion.ssh-user git
    $ sudo visudo

Add these lines to that file::

    # Configuration for Phabricator VCS
    www-data ALL=(phabdaemon) SETENV: NOPASSWD: /usr/bin/git, /usr/lib/git-core/git-http-backend
    git ALL=(phabdaemon) SETENV: NOPASSWD: /usr/bin/git, /usr/bin/git-upload-pack, /usr/bin/git-receive-pack

..  NOTE:: We had to comment out the recommended version for ``git`` and put in
    the second version, in order for SSH to work with our repositories. We need
    to find out what all binaries ``git`` is needing to use, and add them to the
    first path. When this is acheved, be sure to swap the comments...do NOT
    leave them both uncommented!

Also ensure that if there is the line ``Defaults    requiretty``, it is commented
out. If it's not there, we're good.

Save and close.

Now, we need to edit a couple other files.

..  code-block:: bash

    $ sudo vim /etc/shadow

Find the line for ``git`` and change the ``!`` in the second field to ``NP``. Save
and close.

Next, run...

..  code-block:: bash

    $ sudo vim /etc/passwd

Find the line for ``git`` and set (or change) the last field to ``/bin/sh``.
Save and close.

Let's also add the ``git`` user to our ``phab`` group, so it can write to logfile
locations.

..  code-block:: bash

    $ sudo usermod -a -G phab git

Now let's configure the ports and SSH settings.

..  code-block:: bash

    $ /opt/phab/phabricator/bin/config set diffusion.ssh-port 2222
    $ sudo ufw allow 2222

Now we need to copy the SSH hook script to our scripts directory. We will
need to create a special subdirectory that is owned by root and has permissions
``755``, otherwise it won't start.

..  code-block:: bash

    $ cd /opt/scripts
    $ sudo mkdir root_scripts
    $ sudo chmod 755 root_scripts
    $ cd root_scripts
    $ sudo cp /opt/phab/phabricator/resources/sshd/phabricator-ssh-hook.sh ./phabricator-ssh-hook
    $ sudo chmod 755 ./phabricator-ssh-hook
    $ sudo vim /opt/scripts/root_scripts/phabricator-ssh-hook

Edit that file so it matches the following...

..  code-block:: bash

    #!/bin/sh

    # NOTE: Replace this with the username that you expect users to connect with.
    VCSUSER="git"

    # NOTE: Replace this with the path to your Phabricator directory.
    ROOT="/opt/phab/phabricator"

    if [ "$1" != "$VCSUSER" ];
    then
    exit 1
    fi

    exec "$ROOT/bin/ssh-auth" $@

Save and close. Now we need to set up SSHD's configuration.

..  code-block:: bash

    $ sudo cp /opt/phab/phabricator/resources/sshd/sshd_config.phabricator.example /etc/ssh/sshd_config.phabricator
    $ sudo vim /etc/ssh/sshd_config.phabricator

In that file, set the following lines::

    AuthorizedKeysCommand /opt/scripts/root_scripts/phabricator-ssh-hook
    AuthorizedKeysCommandUser git
    AllowUsers git

    # You may need to tweak these options, but mostly they just turn off everything
    # dangerous.

    Port 2222

Save and close.

Now we try running SSHD in debug mode first.

..  code-block:: bash

    $ sudo /usr/sbin/sshd -d -d -d -f /etc/ssh/sshd_config.phabricator

Make sure you've added your SSH public key to your Phabricator profile. Then,
on the guest computer you use for SSH, run...

..  code-block:: bash

    echo {} | ssh git@phabricator.mousepawmedia.net -p 2222 conduit conduit.ping

After all is said and done, it should print out something like
``{"result":"hawksnest-server","error_code":null,"error_info":null}``.

..  NOTE:: If it gives the message "Could not chdir to home directory
    /home/git: No such file or directory", that means you didn't create
    the ``git`` user with a home directory. If that's the case, you can add
    one by running ``$ sudo mkhomedir_helper git`` (on the server).

Once you're assured of this working, run...

..  code-block:: bash

    $ sudo /usr/sbin/sshd -f /etc/ssh/sshd_config.phabricator

Double-check functionality by re-running the earlier command on the
computer you SSH from. Run this two or three times to be certain.

..  code-block:: bash

    echo {} | ssh git@phabricator.mousepawmedia.net -p 2222 conduit conduit.ping

If it works, then all's well! Add the sshd start command to the system cron.

..  code-block:: bash

    $ sudo crontab -e

On that file, add the line::

    @reboot /usr/sbin/sshd -f /etc/ssh/sshd_config.phabricator

Save and close.

Migrating Domain Names
-----------------------------------------------

..  WARNING:: I did this after the initial setup of Phabricator using the old
    domain names. If you're recreating again, DO NOT USE THIS unless you're
    actually changing domain names, and consider setting up with the old
    domain names first before following this.

..  code-block:: bash

    $ cd /opt/phab/phabricator/bin
    $ ./config set phabricator.allowed-uris '["https://<oldserveraddress>:8446/"]'
    $ ./config set phabricator.base-uri https://phabricator.<serveraddress>/

Then, revisit the other steps to ensure everything's working on the correct
domain names.

LDAP
-------------------------------------------------

I want to allow logging in with LDAP. In the terminal, run the following:

..  code-block:: bash

    $ cd /opt/phab/phabricator
    $ bin/auth unlock

Then, in Phabricator itself, go to the Auth app (``/auth``).

Add the ``LDAP`` provider. Use the following settings:

* LDAP Hostname: ``localhost``
* LDAP Port: ``389``
* Base Distinguished Name: ``ou=Users, dc=ldap, dc=mousepawmedia, dc=net``
* Search Attributes: ``uid cn mail`` (note: put each of those on a separate line)
* Always Search: Yes
* Username Attribute: ``uid``
* Realname Attributes: ``cn``
* LDAP Version: ``3``

Jenkins
=================================================

Installation
----------------------------------------------------

We first need to install Jenkins and VirtualBox, both of which must be
ready to go before we can migrate the old Jenkins installation over.

..  code-block:: bash

    $ sudo su
    # wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | apt-key add -
    # echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list
    # wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
    # echo deb http://download.virtualbox.org/virtualbox/debian xenial contrib > /etc/apt/sources.list.d/virtualbox.list
    # exit
    $ sudo apt update
    $ sudo apt install jenkins virtualbox-5.1

Wait for the installation to complete. Next, we need to download the Extension
Pack. Find the latest download link from `their download page <https://www.virtualbox.org/wiki/Downloads>`_.

..  code-block:: bash

    $ cd /tmp
    $ sudo wget http://download.virtualbox.org/virtualbox/5.1.8/Oracle_VM_VirtualBox_Extension_Pack-5.1.8-111374.vbox-extpack
    $ sudo vboxmanage extpack install --replace *.vbox-extpack
    $ sudo rm *.vbox-extpack

Now we're ready for migrating the old install.

If you're setting up Jenkins fresh, follow `this tutorial <http://www.indeliblebluepen.com/?p=901>`_
to get Jenkins and VirtualBox working together.

Migration
---------------------------------------------------

We copied the old ``$JENKINS_HOME`` folder to the new server, via...

..  code-block:: bash

    $ sudo mv /var/lib/jenkins /var/lib/jenkins_new
    $ sudo rsync -av /mnt/stash/var/lib/jenkins/ /opt/jenkins
    $ sudo chown -R jenkins /opt/jenkins
    $ sudo chgrp -R jenkins /opt/jenkins
    $ sudo ln -s /opt/jenkins /var/libjenkins

..  NOTE:: I originally couldn't start Jenkins, until I realized that
    I hadn't updated the owner and group.

This means that Jenkins' HOME folder is actually in /opt/jenkins, but we have a
symbolic link to it in /var/lib/jenkins, where Jenkins will be looking for it.
This makes life easier when we do backups.

Next, we'll put the VirtualBox in ``/opt``.

..  code-block:: bash

    $ sudo mkdir /opt/virtualbox
    $ cd /opt/virtualbox
    $ sudo cp -r /mnt/stash/home/hawksnest/VirtualBox\ VMs/LittleXenial ./LittleXenial
    $ sudo chown hawksnest LittleXenial
    $ sudo chgrp hawksnest LittleXenial
    $ cd LittleXenial
    $ cp /mnt/stash/home/hawksnest/LittleXenial/LittleXenial.vdi ./

That last copy will take a while, so sit back and relax.

Once all that's done, we need to update our permissions one more time.

..  code-block:: bash

    $ sudo chown -R hawksnest /opt/virtualbox/LittleXenial
    $ sudo chgrp -R hawksnest /opt/virtualbox/LittleXenial

Next, we need to register the LittleXenial VM with VirtualBox, and change
where it looks for it's virtual hard drive (``.vdi``).

..  code-block:: bash

    $ VBoxManage registervm /opt/virtualbox/LittleXenial/LittleXenial.vbox
    $ VBoxManage list hdds

Get the UUID of the HDD you want to remove, and then substitute it for ``UUID``
in the next command.

..  code-block:: bash

    $ VBoxManage closemedium UUID
    $ VBoxManage storageattach LittleXenial --storagectl "IDE Controller" --port 0 --device 0 --type hdd --medium /opt/virtualbox/LittleXenial/LittleXenial.vdi

Once all this is done, we'll start LittleXenial.

..  code-block:: bash

    VBoxManage startvm LittleXenial --type headless

We need to add that command to our Hawksnest user crontab (NOT the root crontab!)

..  code-block:: bash

    crontab -e

Add the following line::

    @reboot VBoxManage startvm LittleXenial --type headless > /opt/log/vm.log

Configuration
--------------------------------------------------

Next, we need to modify Jenkins' configuration.

..  code-block:: bash

    $ sudo vim /etc/default/jenkins

Change ``HTTP_PORT=8080`` to ``HTTP_PORT=8449``. Then, place the following
at the bottom of the file, replacing the last line.::

    # Old HTTP mode (turned off)
    #JENKINS_ARGS="--webroot=/var/cache/$NAME/war --httpPort=$HTTP_PORT"

    # HTTPS mode. Make sure the password matches the PASS arg defined in /etc/apache2/ssl/mousepawmedia.net/renewcert_post
    # We also open port 8459 for HTTP, to allow Phabricator in. Have everyone use 8449 instead.
    JENKINS_ARGS="--webroot=/var/cache/$NAME/war --httpsPort=$HTTP_PORT --httpPort=8459 --httpsKeyStore=/etc/apache2/ssl/mousepawmedia.net/keys.jks --httpsKeyStorePassword=a674dRnZ15A6a4ByQ"

..  NOTE:: The password specified on the last line, by the
    ``--httpsKeyStorePassword=``, must MATCH the password supplied when we
    set up the ``renewcert_post`` script under the Let's Encrypt section.

Finally, open the port for Jenkins...

..  code-block:: bash

    $ sudo ufw allow 8449
    $ sudo ufw allow 8459

Navigate to the Jenkins HTTPS URL on the server (``https://<serveraddress>:8449/``)
to test it out. Also, check the HTTP version that Phabricator uses
(``http://<serveraddress>:8459/``).

Apache2 Proxy
---------------------------------------------------

While we won't make any sort of effort to prevent access of Jenkins through
the usual ports (8449 and 8459), it would be helpful to redirect requests sent
to 80 and 443 for the Jenkins subdomain via a proxy. Let's set this up.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/jenkins.conf

Set the contents of that file to...

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName jenkins.mousepawmedia.net
            ServerAdmin hawksnest@mousepawmedia.com

            SSLProxyEngine on
            ProxyPreserveHost On
            ProxyPass         /  https://jenkins.<serveraddress>:8449/
            ProxyPassReverse  /  https://jenkins.<serveraddress>:8449/
            ProxyRequests     Off
            AllowEncodedSlashes NoDecode

            SSLEngine on
            SSLCertificateFile  /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf
        </VirtualHost>

        <VirtualHost *:80>
            Servername jenkins.mousepawmedia.net
            ServerAdmin hawksnest@mousepawmedia.com

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            RewriteEngine On
            RewriteCond %{HTTPS} off
            RewriteRule ^/(.*)$ https://jenkins.<serveraddress>/$1
        </VirtualHost>
    </IfModule>

Save and close. Make sure the needed modules are enabled, enable the site,
and restart Apache2.

..  code-block:: bash

    $ sudo a2enmod proxy
    $ sudo a2enmod proxy_http
    $ sudo a2enmod jenkins
    $ sudo systemctl apache2 restart

Navigate to ``http://jenkins.<serveraddress>/`` and
``https://jenkins.<serveraddress>/``. It should quietly proxy over to the HTTPS
version of Jenkins (proxy 8449).

Test to ensure ``http://jenkins.<serveraddress>:8459/`` still works over HTTP,
without redirecting.

LDAP Integration
---------------------------------------------------

All logins and permissions will be handled by LDAP.

You will first need to install the "LDAP Plugin" and
"Role-based Authorization Stragegy" plugins.

Go to ``Manage Jenkins`` and ``Configure Global Security``. Under Security Realm,
select ``LDAP`` and fill in the following details. (You may need to click
``Advanced Server Configuration...``

* Server: ``localhost:389``
* root DN: ``dc=ldap,dc=mousepawmedia,dc=net``
* User search base: ``ou=Users``
* User search filter: ``uid={0}``
* Group search base: ``ou=Groups``
* Group search filter: ``(& (cn={0}) (objectclass=posixGroup))``
* Group membership: Search for LDAP groups containing user
* Group membership filter: ``(memberUid={1})``
* Display Name LDAP attribute: ``cn``
* Email Address LDAP attribute: ``mail``

Click ``Test LDAP settings`` to make sure it works correctly. Login with an LDAP
account when prompted, and you should see the groups that user is a member of
in LDAP.

Also scroll down to Authroization and select ``Role-Based Strategy``.

Press ``Save``.

Now go to ``Manage and Assign Roles``. In ``Manage Roles``, create or modify
the roles "admin", "anonymous", "repomaster", and "staff", setting permissions
as appropriate.

Now to go ``Manage Jenkins``, ``Manage and Assign Roles``, and ``Assign Roles``.

Add the groups ``admin``, ``community``, ``repomaster``, and ``staff``. Grant
them roles as appropriate. (For us, both ``staff`` and ``community`` are
granted the ``staff`` role.)

Scroll down and click ``Save``.

HTML Landing Page
===================================================

We now need to configure the HTML landing pages.

..  code-block:: bash

    $ sudo mkdir /opt/html
    $ sudo chown hawksnest /opt/html
    $ sudo chgrp www-data /opt/html
    $ sudo chmod u=rwx,g=rwx,o=rx /opt/html
    $ cd html

We can create various directories in here. However, since we're transferring
from the old server, we'll do that here.

..  code-block:: bash

    $ cp -r /mnt/stash/home/hawksnest/HTML/common/ ./
    $ cp -r /mnt/stash/home/hawksnest/HTML/docs/ ./
    $ cp -r /mnt/stash/home/hawksnest/HTML/landing/ ./
    $ cp -r /mnt/stash/home/hawksnest/HTML/protected/ ./
    $ sudo chown -R hawksnest /opt/html
    $ sudo chgrp -R www-data /opt/html
    $ sudo chmod -R u=rwx,g=rwx,o=rx /opt/html

Now we need to adjust Apache2. First, modify ``apache2.conf``.

..  code-block:: bash

    $ sudo vim /etc/apache2/apache2.conf

Add the following ``<Directory>`` entries below the ones that are already there.

..  code-block:: apache

    <Directory "/opt/html/landing">
        Options Indext
        es FollowSymLinks
        Require all granted
    </Directory>

    <Directory "/opt/html/protected">
        Options Indexes FollowSymLinks
        Require all granted
    </Directory>

Next, modify ``ports.conf``...

..  code-block:: bash

    $ sudo vim /etc/apache2/ports.conf

Modify the file so it looks something like this (we only added the ``Port 8442``
lines. Leave the rest alone)...

..  code-block:: apache

    Listen 80

    <IfModule ssl_module>
        Listen 443
        Listen 8442
        Listen 8446
    </IfModule>

    <IfModule mod_gnutls.c>
        Listen 443
        Listen 8442
        Listen 8446
    </IfModule>

Now we need to create two new sites in Apache2.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/000-landing.conf

This file should look like this...

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName mousepawmedia.net

            ServerAdmin hawksnest@mousepawmedia.com
            DocumentRoot /opt/html/landing

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            SSLEngine on
            SSLCertificateFile     /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf

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

        <VirtualHost *:443>
            ServerName hawksnest.ddns.net
            RedirectMatch ^/(.*)$ https://<serveraddress>/$1
        </VirtualHost>
    </IfModule>


Save and close. Open up the next.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/protected.conf

This file should look like this...

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName secure.mousepawmedia.net
            ServerAdmin hawksnest@mousepawmedia.com

            DocumentRoot /opt/html/protected

            # We validate against our LDAP server.
            <Location "/">
                AllowOverride AuthConfig
                AuthType Basic
                AuthBasicProvider ldap
                AuthName "Restricted Resource"
                AuthLDAPURL "ldap://localhost:389/ou=Users, dc=ldap, dc=mousepawmedia, dc=net"
                Require valid-user
            </Location>

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            SSLEngine on

            SSLCertificateFile      /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf

            BrowserMatch "MSIE [2-6]" \
                            nokeepalive ssl-unclean-shutdown \
                            downgrade-1.0 force-response-1.0
            # MSIE 7 and newer should be able to use keepalive
            BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown
        </VirtualHost>

        <VirtualHost *:8442>
            ServerName hawksnest.ddns.net

            SSLEngine on
            SSLCertificateFile /etc/apache2/ssl/hawksnest/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/hawksnest/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf

            RedirectMatch ^/(.*)$ https://secure.<serveraddress>/$1
        </VirtualHost>
    </IfModule>

Save and close.

Now we enable both sites, disable the defaults, and restart Apache2.

..  code-block:: bash

    $ sudo a2dissite 000-default
    $ sudo a2dissite default-ssl
    $ sudo a2ensite landing
    $ sudo a2ensite protected
    $ sudo a2enmod ldap
    $ sudo a2enmod authnz_ldap
    $ sudo ufw allow 8442
    $ sudo systemctl restart apache2

Navigate to ``http://<serveraddress>`` and ``https://secure.<serveraddress>`` to test
the ``landing`` and ``protected`` sites respectively.

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

..  NOTE:: Apache2 sites are loaded in alphabetical order. Addresses and ports
    are first come, first served, so the first site that defines on a port
    gets all addresses on that port, unless something else snatches away
    a specific address.

Docker
===========================

**Docker** is used both by Jenkins for build agents, and by Collabora Office.

We start by installing Docker:

..  code-block:: bash

    $ sudo apt-get remove docker docker-engine docker.io
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    $ sudo apt update
    $ sudo apt install docker-ce docker-compose

We also add the main server user to the Docker account, so Docker instances
can be spun up that way.

..  code-block:: bash

    $ sudo usermod -aG docker hawksnest

Next, we set up Docker to be automatically started by systemd.

..  code-block:: bash

    $ sudo systemctl edit docker.service

Set the contents of that file to::

    [Service]
    ExecStart=
    ExecStart=/usr/bin/dockerd -H fd:// -H tcp://127.0.0.1:2375

Save and close, and then enable and restart Docker in systemd:

..  code-block:: bash

    $ sudo systemctl restart docker
    $ sudo systemctl enable docker

..  NOTE:: At this point, I added the certificate for the Docker registry.

Creating the Docker Registry
-----------------------------------

The Docker Registry hosts images for creating Docker containers. This speeds
up build times significantly.

I start by created a directory for the registry to live in, and then pointing
Docker to it.

..  code-block:: bash

    $ sudo mkdir /opt/registry
    $ docker run -d -p 5000:5000 \
    --restart=always \
    --name registry \
    -v /opt/registry:/var/lib/registry \
    registry:2

I can now set up Apache2 to host the registry.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/registry.conf

Set the contents of that file to:

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName registry.mousepawmedia.net
            ServerAdmin hawksnest@mousepawmedia.com

            Header add X-Forwarded-Proto "https"
            RequestHeader add X-Forwarded-Proto "https"

            ProxyPreserveHost On
            ProxyPass         /  http://127.0.0.1:5000/
            ProxyPassReverse  /  http://127.0.0.1:5000/

            <Location />
                Order deny,allow
                Allow from all

                #AllowOverride AuthConfig
                AuthName "Registry Authentication"
                AuthType basic
                AuthBasicProvider ldap
                AuthLDAPURL "ldap://localhost:389/ou=Users, dc=ldap, dc=mousepawmedia, dc=net"
                Require valid-user
            </Location>

            # Allow ping and users to run unauthenticated
            <Location /v1/_ping>
                Satisfy any
                Allow from all
            </Location>

            # Allow ping and users to run authenticated
            <Location /_ping>
                Satisfy any
                Allow from all
            </Location>

            SSLEngine on
            SSLCertificateFile  /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf
        </VirtualHost>
    </IfModule>

Save and close. Now we enable the site:

..  code-block:: bash

    $ sudo a2ensite registry
    $ sudo a2enmod headers
    $ sudo systemctl restart apache2

We can test it out with the following command:

..  code-block:: bash

    $ docker login registry.mousepawmedia.net
    $ docker pull ubuntu:18.04
    $ docker tag ubuntu:18.04 registry.mousepawmedia.net/mpm-bionic
    $ docker push registry.mousepawmedia.net/mpm-bionic
    $ docker image remove ubuntu:18.04
    $ docker image remove registry.mousepawmedia.net/mpm-bionic
    $ docker pull registry.mousepawmedia.net/mpm-bionic

Login with valid LDAP credentials. If it succeeds, everything is correctly
configured.

We now tear down our initial test container, and switch to ``docker-compose``
for running our registry long-term instead:

..  code-block:: bash

    $ docker container stop registry
    $ docker container rm -v registry
    $ sudo mkdir -p /opt/docker/registry
    $ sudo vim /opt/docker/registry/docker-compose.yml
    $ sudo chown root:docker -R /opt/docker
    $ cd /opt/docker/registry
    $ docker-compose up -d

The registry will now automatically restart with the ``docker.service``
managed by ``systemctl``.

SOURCE: `Securing a docker registry behind Apache <https://lathonez.com/2016/docker-registry-apache-letsencrypt/>`_

Setting Up Docker Login with Pass
-----------------------------------

For additional security, we want to use ``pass`` to store credentials for
Docker registry logins.

I'll start by logging out of the registry, and installing ``pass``:

..  code-block:: bash

    $ docker logout registry.mousepawmedia.net
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

For example, here's the key I want::

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

    $ docker-credential-pass version

That should print out the version of ``docker-credential-pass` that is
installed. If it works, you should also make sure it is communicating with
``pass`` via the following command:

..  code-block:: bash

    $ docker-credential-pass list

If that returns ``{}`` or some other data (instead of an error or warning),
everything is correctly installed.

Finally, I'll tell Docker to use ``docker-credential-pass``:

..  code-block:: bash

    $ vim ~/.docker/config.json

Set the ``"credsStore"`` property to ``"pass"`` (include the quotes!), and
then save and close.

You should now be able to login with Docker, which you can test via:

..  code-block:: bash

    $ docker login registry.mousepawmedia.net

Enter valid LDAP credentials. If it works, you'll see::

    Login Succeeded

Configuring Jenkins to Use Docker
--------------------------------------------

With Docker successfully installed, now we only need to setup Jenkins to use it.

I recommend the following Jenkins plugins:

* Docker API Plugin
* Docker Commons Plugin
* Docker Pipeline
* Docker plugin

Once those are installed from :menuselection:`Manage Jenkins --> Manage Plugins`,
go to :menuselection:`Manage Jenkins --> Configure System`.

Scroll down to :guilabel:`Global properties` and check the box
:guilabel:`Environment variables`. Add the following variable:

* :guilabel:`Name`: ``DOCKER_HOST``
* :guilabel:`Value`: ``tcp://127.0.0.1:2375``

Scroll down to :guilabel:`Declarative Pipeline (Docker)`, and set the following:

* :guilabel:`Docker Label`: ``docker``
* :guilabel:`Docker registry URL`: ``https://registry.mousepawmedia.net``
* :guilabel:`Registry credentials`: (Add the LDAP credentials you want to use here.)

Save those configuration details via the :guilabel:`Save` button at the bottom.

Now go to :menuselection:`Manage Jenkins --> Manage Nodes and Clouds`. At left,
click :guilabel:`Configure Clouds`. Add a new cloud with the following settings:

* :guilabel:`Name`: (Name it whatever you want. We call ours ``mpm-bionic``)
* :guilabel:`Docker Cloud details...`
  * :guilabel:`Docker Host URI`: ``unix:///var/run/docker.sock``
  * :guilabel:`Enabled`: [Checked]
  * :guilabel:`Container Cap`: 5
  * :guilabel:`Docker Agent Template`:
    * :guilabel:`Labels`: ``mpm-bionic``
    * :guilabel:`Enabled`: [Checked]
    * :guilabel:`Docker Image`: ``registry.mousepawmedia.net/jenkins.mpm-bionic``
    * :guilabel:`Container Settings...`
      * :guilabel:`Volumes`: ``/opt/jenkins/workspace:/workspace``
      * :guilabel:`Extra Hosts`: (If your router does not include NAT loopback,
        be sure to configure this to handle routing back to other subdomains
        on the server from inside the Docker container.
      * :guilabel:`Remote File System Root`: ``/``
      * :guilabel:`Usage`: ``Use this node as much as possible``
        (change if you like)
      * :guilabel:`Idle timeout`: ``2``
      * :guilabel:`Connect method`: ``Attach Docker container``
    * :guilabel:`Remove Volumes`: [Unchecked]
    * :guilabel:`Pull stretegy`: ``Pull once and update latest``
    * :guilabel:`Pull timeout`: ``300``

Save that cloud via the :guilabel:`Save` button at the bottom of the screen.
Now any job tagged to run on ``mpm-bionic`` will use this cloud.

..  NOTE:: The ``jenkins.mpm-bionic`` image's Dockerfile is configured to
    use the JNLP. See our ``buildenv`` repository for that Dockerfile.

You can continue to configure additional clouds along this same pattern.

Nextcloud
===========================

Installation
----------------------------

Let's install the other PHP packages we need for this. Most of these are
probably already installed, but we're putting them here to be certain.

..  code-block:: bash

    $ sudo apt install php7.4-bz2 php7.4-intl php7.4-xml php7.4-zip php7.4-curl php7.4-gd php-imagick php7.4-mbstring php7.4-ldap

Now we can install Nextcloud itself.

..  NOTE:: While we are installing 10.0.1 below, Nextcloud has been upgraded
    many times since; as of this, the latest is 15.0.5. Adjust commands below
    according to the latest stable version of Nextcloud.

..  code-block:: bash

    $ cd /tmp
    $ curl -LO https://download.nextcloud.com/server/releases/nextcloud-10.0.1.tar.bz2
    $ curl -LO https://download.nextcloud.com/server/releases/nextcloud-10.0.1.tar.bz2.sha256
    $ shasum -a 256 -c nextcloud-10.0.1.tar.bz2.sha256 < nextcloud-10.0.1.tar.bz2

Ensure that last command says "OK" before continuing, as that confirms the
tarball hasn't been tampered with or spoofed.

..  code-block:: bash

    $ rm nextcloud-10.0.1.tar.bz2.sha256
    $ sudo tar -C /opt -xvjf /tmp/nextcloud-10.0.1.tar.bz2
    $ vim /tmp/nextcloud.sh

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

    $ sudo vim /etc/apache2/sites-available/nextcloud.conf

Set the contents to...

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName nextcloud.mousepawmedia.net
            DocumentRoot /opt/nextcloud

            SSLEngine on
            SSLCertificateFile     /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            <Directory "/opt/nextcloud">
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

    $ sudo vim /etc/apache2/apache2.conf

Add the following below the other ``<Directory>`` entries...

..  code-block:: apache

    <Directory "/opt/nextcloud">
        Options Indexes FollowSymLinks
        Require all granted
    </Directory>

Then, enable the site and restart Apache2.

..  code-block:: bash

    $ sudo a2ensite nextcloud
    $ sudo systemctl restart apache2

..  WARNING: We are intentionally ignoring the recommendation to enable
    the Headers mod. At this time, doing so forces use of Jenkins over HTTPS,
    which prevents Phabricator from interfacing with it.

Database Setup
-------------------------------------

We need to create a ``nextcloud`` database and a ``nextcloud`` user in MySQL.
This can be done through PHPmyadmin.

nextCloud Configuration
------------------------------

On the nextcloud page, specify an admin account.

Click ``Storage and Database``, set the Data folder to ``/opt/nextcloud/data``.
Select ``MySQL`` for the database, and provide the database user, password,
and database name. The fourth field should be ``localhost``.

Click ``Finish setup``.

..  NOTE:: If you have problems logging into the database on this screen,
    check PHPmyadmin → ``nextcloud`` (database) → Privileges. The ``nextcloud``
    user should be listed, with ``Grant Yes``.

`SOURCE How To Install and Configure Nextcloud on Ubuntu 16.04 (DigitalOcean) <https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-nextcloud-on-ubuntu-16-04>`_

Preventing Lockouts
-----------------------------

Due to a glitch in Nextcloud, we have to configure fail2ban to prevent lockouts.

..  code-block:: bash

    $ sudo vim /etc/fail2ban/filter.d/apache-auth.conf

Change or add the following lines::

    # ignore intentional auth failures from nextcloud admin page
    ignoreregex = [nextcloud|ajc|ibp]/[data/.ocdata|config]

..  NOTE:: ``nextcloud``, ``ibp``, and ``ajc`` are the three Nextcloud
    instance directories on this server. Change these as needed.

Configuring Memory Caching
-----------------------------

To improve performance, we'll enable memory caching. We are using APCu (since
we're using PHP 7.4), so we simply need to enable this for Nextcloud.

..  code-block:: bash

    $ sudo vim /opt/nextcloud/config/config.php

Add the following line before the end::

    'memcache.local' => '\OC\Memcache\APCu',

Save and close, and then restart Apache2.

PHP Configuration
----------------------------

Nextcloud recommends a few tweaks to php.ini. Run...

.. code-block:: bash

    $ sudo vim /etc/php/7.4/apache2/php.ini

Find and edit the following lines::

    opcache.enable=1
    opcache.enable_cli=1
    opcache.interned_strings_buffer=8
    opcache.max_accelerated_files=10000
    opcache.memory_consumption=128
    opcache.save_comments=1
    opcache.revalidate_freq=1

Save and close.

Set Up Cronjob
----------------------------

It is recommended to use Cron for background tasks. We will set this up now.

..  code-block:: bash

    $ sudo crontab -u www-data -e

Add the following line::

    */15  *  *  *  * php -f /opt/nextcloud/cron.php

Save and close.

Finally, in the Nextcloud Admin pane, go to ``Cron`` and select the ``Cron`` option.

`SOURCE: Background Jobs Configuration (Nextcloud) <https://docs.nextcloud.com/server/10/admin_manual/configuration_server/background_jobs_configuration.html>`_

HSTS Header
--------------------------------

To improve security, install the Nextcloud app `HSTS Header`.

LDAP Authentication
--------------------------------

In Nextcloud, go to ``Apps`` and enable LDAP. Then, go to ``Admin`` and ``LDAP``.

Set the following options:

* Server

    * Host: ``localhost``

    * Port: ``389``

    * Base DN: ``ou=Users, dc=ldap, dc=mousepawmedia, dc=net``

Click ``Test Base DN``, and then ``Continue``.

Set ``Only these object classes:`` to just ``inetOrgPerson``, and click
``Verify settings and count users``, and then ``Continue``.

Check ``LDAP/AD Username`` and ``LDAP/AD Email Address``, and then click
``Advanced.`` Set...

* Directory Settings

    * User Display Name Field: ``uid``

    * 2nd User Display Name Field: ``cn``

    * Base User Tree: ``ou=Users, dc=ldap, dc=mousepawmedia, dc=net``

    * Group Display Name Field: ``cn``

    * Base Group Tree: ``ou=Groups, dc=ldap, dc=mousepawmedia, dc=net``

    * Group-Member association: ``memberUid``

* Special Attributes

    * Email field: ``mail``

Also, click the Groups tag and select all the Groups that you want to use
in Nextcloud. We use all but ``users`` and ``disabled``.

Click ``Test Configuration``.

The settings are automatically saved. Log in as an LDAP user to test.

..  TODO:: Set up LDAP Avatar Integration.

`SOURCE: User Auth with LDAP (Nextcloud) <https://docs.nextcloud.com/server/9/admin_manual/configuration_user/user_auth_ldap.html>`_

Collabora Office Online
--------------------------------

Make sure Docker is installed (see earlier section).

Next, we'll pull in the Docker container for Collabora Office online.

..  code-block:: bash

    $ docker pull collabora/code

This download will take a while, so sit back and wait.

Next, we'll deploy the docker image. Make sure you substitute your value
in on the ``password`` option.

..  code-block:: bash

    $ docker run --privileged -t -d -p 127.0.0.1:9980:9980 -e 'domain=nextcloud\\.mousepawmedia\\.net|ajc\\.mousepawmedia\\.net|ibp\\.mousepawmedia\\.net' -e 'username=admin' -e 'password=CollaboraPassword' --restart always --cap-add MKNOD collabora/code

Next, we will set up Apache to proxy to Collabora Office.

..  code-block:: bash

    $ sudo a2enmod proxy proxy_wstunnel proxy_http ssl
    $ sudo systemctl restart apache2
    $ sudo vim /etc/apache2/sites-available/office.conf

Set the contents of that file to the following...

..  code-block:: apache

    <VirtualHost *:443>
        ServerName office.mousepawmedia.net:443

        SSLEngine on
        SSLCertificateFile /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
        SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem
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

..  IMPORTANT:: The above Apache2 configuration is for `CODE 2.0 updates 2 <https://www.collaboraoffice.com/community-en/code-2-0-updates-2/>`_
    and onward. Using the old configuration will break things.

Then, enable the site and restart Apache2.

..  code-block:: bash

    $ sudo a2ensite office
    $ sudo systemctl restart apache2

You can see stats and admin options at ``https://office.<serveraddress>/loleaflet/dist/admin/admin.html``.

Next, go to Nextcloud. Click the menu, and go to
:menuselection:`Apps --> Productivity`. Install the "Collabora Online connector".
Then, go to :menuselection:`Admin --> Additional settings --> Collabora Online`.

Set :guilabel:`Collabora Online server` to :code:`https://office.mousepawmedia.net/`
and click :guilabel:`Apply`.

Now you can go to the Office app in Nextcloud to access Collabora Office!

`SOURCE: Getting started in 3 steps (Nextcloud) <https://nextcloud.com/collaboraonline/>`_

Last, we need to modify fail2ban so it won't lock users out when using
CollaboraOffice.

..  code-block:: bash

    $ sudo nano /etc/fail2ban/filter.d/nextcloud.conf

Set the contents of that file to::

    [Definition]
    failregex={"reqId":".*","remoteAddr":".*","app":"core","message":"Login failed: '.*' \(Remote IP: '<host>'\)","level":2,"time":".*"}
    ignoreregex =

Save and close. Then, run...

..  code-block:: bash

    $ sudo vim /etc/fail2ban/jail.local

Set the contents of that file to::

    [nextcloud]
    enabled = true # set to false to disable
    filter  = nextcloud
    port    =  http,https # Change this to https if you aren't using http
    logpath = /opt/nextcloud/data/nextcloud.log # Make sure this is the right path.
    ignoreip = 10.0.2.1/24 # Change/Delete this if you want to Ignore one or more IP's

Save and close. Then, restart fail2ban.

..  code-block:: bash

    $ sudo systemctl restart fail2ban

`SOURCE: Setup Fail2Ban with Owncloud (TechKnight) <https://techknight.eu/2015/07/25/setup-fail2ban-with-owncloud-8-1-0/>`_

Etherpad
----------------------------------

Installing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We'll also set up Etherpad to work with Nextcloud.

First, we'll install the dependencies.

..  code-block:: bash

    $ sudo apt install git curl python libssl-dev pkg-config build-essential


We'll also set up node.js to work on our server.

..  code-block:: bash

    $ cd /tmp
    $ wget https://nodejs.org/dist/v6.9.2/node-v6.9.2-linux-x64.tar.xz
    $ tar xJf node-v6.9.2-linux-x64.tar.xz
    $ sudo mkdir /opt/nodejs
    $ sudo chown hawksnest:www-data /opt/nodejs
    $ mv node-v6.9.2-linux-x64/* /opt/nodejs
    $ echo "PATH=$PATH:/opt/nodejs/bin" >> ~/.profile

At this point, you'll need to reload your terminal session (quitting and
re-joining SSH should be enough).

Now we'll pull down etherpad-lite from the official repository.

..  code-block:: bash

    $ sudo mkdir /opt/etherpad
    $ sudo chown hawksnest:www-data /opt/etherpad
    $ cd /opt/etherpad
    $ git clone git://github.com/ether/etherpad-lite.git

Next, we need to set up the database for Etherpad. Be sure to replace
``PASSWORD`` with a secure password for the etherpad mysql user.

..  code-block:: bash

    $ mysql -u root -p
    create database `etherpad-lite`;
    grant all privileges on `etherpad-lite`.* to 'etherpad'@'localhost' identified by 'PASSWORD';
    exit

Next, we need to edit the configuration...

..  code-block:: bash

    $ cp settings.json.template settings.json
    $ vim /opt/etherpad/etherpad-lite/settings.json

Comment out or remove the following section...

..  code-block:: javascript

    "dbType" : "dirty",
      //the database specific settings
      "dbSettings" : {
                       "filename" : "var/dirty.db"
                     },

Edit the next section so it matches the following, where ``PASSWORD`` is the
SQL password you specified earlier. Make sure you also are uncommenting this
section!

..  code-block:: javascript

    /* An Example of MySQL Configuration */
    "dbType" : "mysql",
    "dbSettings" : {
                    "user"    : "etherpad",
                    "host"    : "localhost",
                    "password": "PASSWORD",
                    "database": "etherpad-lite",
                    "charset" : "utf8mb4"
                  },

Also, look for the following section, and change the passwords. Again, make sure
you are uncommenting this section!

..  code-block:: javascript

    /* Users for basic authentication. is_admin = true gives access to /admin.
     If you do not uncomment this, /admin will not be available! */
    "users": {
    "admin": {
      "password": "changeme1",
      "is_admin": true
    }
    },

For security reasons, let's **not** show the contents of this file in the
admin panel.

..  code-block:: javascript

    // Option to hide/show the settings.json in admin page, default option is set to true
    "showSettingsInAdminPage" : false,

Finally, look for this section and change it to match the following, thereby
pointing to our Let's Encrypt certificates.

..  code-block:: javascript

    /*
    // Node native SSL support
    // this is disabled by default
    //
    // make sure to have the minimum and correct file access permissions set
    // so that the Etherpad server can access them
    */

    "ssl" : {
            "key"  : "/etc/apache2/ssl/mousepawmedia.net/privkey.pem",
            "cert" : "/etc/apache2/ssl/mousepawmedia.net/cert.pem",
            "ca": ["/etc/apache2/ssl/mousepawmedia.net/chain.pem"]
          },

We will need to add our ``hawksnest`` user to our ``certs`` group, since
``hawksnest`` is what runs Etherpad, and it must be able to read the
certificates for SSH to work.

..  code-block:: bash

    $ sudo usermod -a -G certs hawksnest

After running that command, we may need to quit and start our terminal session
again.

Next, we'll let Etherpad set up its other dependencies.

..  code-block:: bash

    $ /opt/etherpad/etherpad-lite/bin/installDeps.sh

Now we can run Etherpad for the first time.

..  code-block:: bash

    $ /opt/etherpad/etherpad-lite/bin/run.sh

If all goes well and it starts, press :kbd:`Ctrl+C` to quit. Now we need
to make some more changes to the database.

..  code-block:: bash

    $ mysql -u root -p
    alter database `etherpad-lite` character set utf8 collate utf8_bin;
    use `etherpad-lite`;
    alter table `store` convert to character set utf8 collate utf8_bin;
    exit

With all that done, let's allow the Etherpad port through, and then
start Etherpad again.

..  code-block:: bash

    $ sudo ufw allow 9001
    $ /opt/etherpad/etherpad-lite/bin/run.sh

Navigate to ``https://<serveraddress>:9001`` to test that Etherpad is working.
If you can access it, we're ready to configure Nextcloud to work with it.

`SOURCE: Install Etherpad web-based real time collaborative editor on Ubuntu 16.04 Linux (LinuxConfig.org) <https://linuxconfig.org/install-etherpad-web-based-real-time-collaborative-editor-on-ubuntu-16-04-linux>`_

`SOURCE: How to Install Etherpad For Production with Node.js and MySQL on a VPN (DigitalOcean) <https://www.digitalocean.com/community/tutorials/how-to-install-etherpad-for-production-with-node-js-and-mysql-on-a-vps>`_

Configuring Nextcloud
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In Nextcloud, go to ``Apps``. Under ``Tools``, look for ``OwnPad`` and enable
it.

Then, go to ``Admin`` and ``Additional settings``. Scroll down to
``Collaborative documents`` and check ``Enable Etherpad``. Set the
``Etherpad Host`` to ``http://<serveraddress>.net:9001``.

Finally, we need to adjust the MIME types.

..  code-block:: bash

    $ sudo su
    # cd /opt/nextcloud
    # cp resources/config/mimetypemapping.dist.json config/mimetypemapping.json
    # vim config/mimetypemapping.json

After the comment sections, add the following line.

..  code-block:: javascript

    "pad": ["application/x-ownpad"],

That's it! You can now create new pad items on Nextcloud by creating a new
item, and selecting ``Pad``.

Be sure to exit out of the root session on your terminal.

Automatically Starting Etherpad
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Finally, we need to configure the server to automatically start Etherpad.

..  code-block:: bash

    $ sudo mkdir /opt/scripts/other
    $ sudo chown hawksnest:www-data other/
    $ cd /opt/scripts/other
    $ vim start_etherpad

Set the contents of that file to the following...

..  code-block:: bash

    #!/usr/bin/env bash
    screen -S "etherpad" -d -m /opt/etherpad/etherpad-lite/bin/run.sh

Save and close, and then make it executable and add it to the user-level
crontab.

..  code-block:: bash

    $ chmod +x start_etherpad
    $ crontab -e

Add the following to the bottom of your crontab, save, and close::

    @reboot /opt/scripts/other/start_etherpad

Let's go ahead and run that script to startup Etherpad.

..  code-block:: bash

    $ /opt/scripts/other/start_etherpad

After a minute, check to make sure ``http://<serveraddress>:9001`` is working,
and then we're ready to go.

Apache2 Proxy
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's set up a proxy for Etherpad.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/pad.conf

Set the contents of that file to the following...

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName pad.mousepawmedia.net
            ServerAdmin hawksnest@mousepawmedia.com

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            SSLEngine on
            SSLCertificateFile  /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem
            Include /etc/letsencrypt/options-ssl-apache.conf

            <IfModule mod_proxy.c>
                # the following allows "nice" urls such as https://etherpad.example.org/padname
                # But, some users reported issues with this
                RewriteEngine On
                RewriteRule /p/*$ https://pad.mousepawmedia.net/ [NC,L]
                RewriteCond %{REQUEST_URI} !^/locales/
                RewriteCond %{REQUEST_URI} !^/locales.json
                RewriteCond %{REQUEST_URI} !^/admin
                RewriteCond %{REQUEST_URI} !^/p/
                RewriteCond %{REQUEST_URI} !^/static/
                RewriteCond %{REQUEST_URI} !^/pluginfw/
                RewriteCond %{REQUEST_URI} !^/javascripts/
                RewriteCond %{REQUEST_URI} !^/socket.io/
                RewriteCond %{REQUEST_URI} !^/ep/
                RewriteCond %{REQUEST_URI} !^/minified/
                RewriteCond %{REQUEST_URI} !^/api/
                RewriteCond %{REQUEST_URI} !^/ro/
                RewriteCond %{REQUEST_URI} !^/error/
                RewriteCond %{REQUEST_URI} !^/jserror
                RewriteCond %{REQUEST_URI} !^/redirect
                RewriteCond %{REQUEST_URI} !^/favicon.ico
                RewriteCond %{REQUEST_URI} !^/robots.txt
                RewriteCond %{REQUEST_URI} !^/list/
                RewriteCond %{REQUEST_URI} !^/public/
                RewriteRule ^/p/(.+)$ https://pad.mousepawmedia.net/p/$1 [L]

                SSLProxyEngine On
                SSLProxyVerify none
                SSLProxyCheckPeerCN off
                SSLProxyCheckPeerName off
                SSLProxyCheckPeerExpire off

                ProxyVia On
                ProxyRequests Off
                ProxyPass / https://mousepawmedia.net:9001/
                ProxyPassReverse / https://mousepawmedia.net:9001/
                ProxyPreserveHost on
                <Proxy *>
                    Options FollowSymLinks MultiViews
                    AllowOverride All
                    Order allow,deny
                    allow from all
                </Proxy>
            </IfModule>
        </VirtualHost>
    </IfModule>

Save and close, and then load it up.

..  code-block:: bash

    $ sudo a2ensite pad.conf
    $ sudo systemctl reload apache2

Go to ``https://pad.<serveraddress>`` to check that the new site works.

Adding Plugins
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We can add various plugins to Etherpad. Simply go to
``https://<serveraddress>:9001/admin`` and select ``Plugin Manager``.

We are enabling the following plugins:

* activepads
* adminpads
* authorship_toggle
* autocomp
* cursortrace
* print
* scrollto
* syntaxhighlighting
* wrap

EtherDraw
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  NOTE:: This isn't currently working - we're waiting on a fix.

We'll set up Etherdraw for integration with Etherpad.

..  code-block:: bash

    $ sudo apt install libpng-dev libfreetype6-dev libfontconfig1-dev libcairo2-dev libpango1.0-dev libgif-dev
    $ cd /opt/etherpad
    $ git clone git://github.com/JohnMcLear/draw.git
    $ cd draw
    $ mysql -u root -p
    create database `etherdraw`;
    grant all privileges on `etherdraw`.* to 'etherpad'@'localhost';
    exit
    $ sudo ufw allow 9002
    $ cp settings.json.template settings.json
    $ vim settings.json

Set the contents of that file to the following, being sure to replace
``PASSWORD`` with the Etherpad SQL password from earlier.

..  code-block:: javascript

    /*
      This file must be valid JSON. But comments are allowed

      Please edit settings.json, not settings.json.template
    */

    {
      //IP and port which etherpad should bind at
      "ip" : "0.0.0.0",
      "port" : 9002,

      /* MySQL Configuration */
      "dbType" : "mysql",
      "dbSettings" : {
        "user"    : "etherpad",
        "host"    : "localhost",
        "password": "PASSWORD",
        "database": "etherdraw"
      },

      /* The default selected tool - 'pencil', 'brush', 'select' */
      "tool": "brush",

      /* SSL Configuration */
        "ssl" : {
          "key": "/etc/apache2/ssl/mousepawmedia.net/privkey.pem",
          "cert": "/etc/apache2/ssl/mousepawmedia.net/cert.pem"
        }
    }

We'll run Etherdraw, to make sure it works...

..  code-block:: bash

    $ /opt/etherpad/draw/bin/run.sh

Give it a minute to set up its dependencies, and then navigate to
``https://<serveraddress>:9002`` to make sure it works.

..  NOTE:: We only got this far due to a bug. Once it works, we'll need to
    add this to the autostart script for Etherpad.

Backups
======================================

With everything set up, we now need to configure regular backups to our
external drive ``/mnt/backup``.

..  NOTE:: The way we have configured everything, if this drive is absent
    for any reason, the system will refuse to boot until it is either attached
    or removed from fstab.

First, we'll install the programs we'll be using.

..  code-block:: bash

    $ sudo apt install duplicity automysqlbackup

Configuring MySQL Backups
---------------------------------------

First, we need a place for our MySQL backups.

..  code-block:: bash

    $ cd /mnt/backup
    $ sudo mkdir sqlbackup
    $ sudo chown root sqlbackup
    $ sudo chgrp root sqlbackup
    $ sudo chmod 0600 sqlbackup

Now we need to adjust the settings for ``automysqlbackup``.

..  code-block:: bash

    $ sudo vim /etc/default/automysqlbackup

Change the following values::

    BACKUPDIR="/mnt/backup/sqlbackup"
    DOWEEKLY=4
    PREBACKUP="/opt/scripts/root-scripts/takedown"
    POSTBACKUP="/opt/scripts/root-scripts/startup"

This will store our backups on the external drive, and perform weekly backups
on THURSDAY (4). Save and close.

Obviously, those scripts don't yet exist. We'll create them now.

..  code-block:: bash

    $ sudo vim /opt/scripts/root_scripts/takedown

This script will shut off everything.

..  code-block:: bash

    #!/bin/bash
    /opt/phab/phabricator/bin/phd stop --force
    /opt/phab/phabricator/bin/aphlict stop
    systemctl stop jenkins
    systemctl stop apache2

Save and close, and then craete the next file.

..  code-block:: bash

    $ sudo vim /opt/scripts/root_scripts/startup

This script will start up everything that ``takedown`` shut off.

..  code-block:: bash

    #!/bin/bash
    systemctl apache2 start
    systemctl jenkins start
    /opt/scripts/phab/phd_start

Save and close. Now, modify permissions on those scripts.

..  code-block:: bash

    $ sudo chmod u=rwx,g=rx,o=r takedown
    $ sudo chmod u=rwx,g=rx,o=r startup

Finally, we'll add the backup process to our cronjob.

..  code-block:: bash

    $ sudo crontab -e

We'll add the following line::

    45 6 * * * /usr/sbin/automysqlbackup

This will run the backup every 6:35am. Save and close.

..  NOTE:: Make sure the server is set to auto-boot at 6:30am.

We can now test the configuration. Note that this may take a few minutes.

..  code-block:: bash

    $ sudo automysqlbackup

As ``root``, navigate to ``/mnt/backup/sqlbackup`` to verify that the backup
completed (it's probably in `daily`).

Configuring Duplicity
--------------------------------

If we're setting up a new backup, we must first create the keys we'll use.
Otherwise, restore the GPG keys from backup.

..  code-block:: bash

    $ sudo su
    # gpg --gen-key

Use the default values. Make sure you specify and save your password.

..  code-block:: bash

    # cp -r /root/.gnupg /mnt/backup/.gnupg
    # gpg --list-keys

Make sure you note the public key. Then, we need to start a new backup. Because
of how we set up our system, we can just backup ``/opt``. In the following
command, replace PUBLICKEY with the public key, and specify the correct
passphrase.

..  code-block:: bash

    # PASSPHRASE='thepasswordforgpg' duplicity --encrypt-key PUBLICKEY /opt file:///mnt/backup/xenial_backup/
    # PASSPHRASE='thepasswordforgpg' duplicity --encrypt-key PUBLICKEY /mnt/stash file:///mnt/backup/stash_backup/

Give those commands some time to run. They're creating a full backups for the
first time.

Finally, let's set up a cron job to run daily backups. Note, we are still
running as root.

..  code-block:: bash

    # cd /root
    # vim .passphrase

In that file, put the passphrase we use for Duplicity, using the same format
as in the command. This enables us to avoid putting the password directly
in our cron.::

    PASSPHRASE='thepasswordforgpg'

Save and close. Make it only readable by root, and then create the daily
incremental backup cron script.

..  code-block:: bash

    # chmod 700 /root/.passphrase
    # cd /etc/cron.daily
    # vim duplicity_inc

..  NOTE: The filename must only contain letters and underscores, or it will never run!

Set the contents of that file to...

..  code-block:: bash

    #!/bin/sh

    test -x $(which duplicity) || exit 0
    . /root/.passphrase

    export PASSPHRASE
    $(which duplicity) --encrypt-key PUBLICKEY /opt file:///mnt/backup/xenial_backup/
    $(which duplicity) --encrypt-key PUBLICKEY /mnt/stash file:///mnt/backup/stash_backup/

Save and close, and then make the script executable. After that, we'll create
a cron script to run a weekly full backup.

..  code-block:: bash

    # chmod 755 duplicity.inc
    # cd /etc/cron.weekly/
    # vim duplicity_full

Set the contents of the weekly file to...

..  code-block:: bash

    #!/bin/sh

    test -x $(which duplicity) || exit 0
    . /root/.passphrase

    export PASSPHRASE
    $(which duplicity) full --encrypt-key PUBLICKEY /opt file:///mnt/backup/xenial_backup/
    $(which duplicity) full --encrypt-key PUBLICKEY /mnt/stash file:///mnt/backup/stash_backup/

    #Clear old backups.
    $(which duplicity) remove-all-but-n-full 3 file:///mnt/backup/xenial_backup/
    $(which duplicity) remove-all-but-n-full 3 file:///mnt/backup/stash_backup/

Save and close, and fix that file's permissions.

..  code-block:: bash

    # chmod 755 duplicity_full

You will want to test both by running...

..  code-block:: bash

    # /etc/cron.daily/duplicity_inc
    # /etc/cron.weekly/duplicity_full

`SOURCE: How To Use Duplicity With GPG (DigitalOcean) <https://www.digitalocean.com/community/tutorials/how-to-use-duplicity-with-gpg-to-securely-automate-backups-on-ubuntu>`_

Finally, let's create a script for verifying our backups, which we should do
fairly frequently.

..  code-block:: bash

    # cp /opt/scripts/root_scripts
    # vim verify_backup

To ensure verification works, this will first perform an incremental backup,
and then use the verify command to ensure the source and backup match.

Set the contents of that file to...

..  code-block:: bash

    #!/bin/bash

    test -x $(which duplicity) || exit 0
    . /root/.passphrase

    export PASSPHRASE
    $(which duplicity) --encrypt-key PUBLICKEY /opt file:///mnt/backup/xenial_backup/
    $(which duplicity) --encrypt-key PUBLICKEY /mtn/stash file:///mnt/backup/stash_backup/
    export PASSPHRASE
    $(which duplicity) verify --encrypt-key PUBLICKEY file:///mnt/backup/xenial_backup/ /opt
    $(which duplicity) verify --encrypt-key PUBLICKEY file:///mnt/backup/stash_backup/ /mnt/stash

Save and close. Make it executable, and then run it.

..  code-block:: bash

    # chmod 755 verify_backup
    # exit
    $ sudo ./verify_backup

Updates and Maintainance
==============================

Updating Collabora
------------------------------

..  code-block:: bash

    $ docker pull collabora/code
    $ docker ps
    # Find the processid from the preceeding.
    $ docker stop processid
    $ docker rm processid
    # Be sure to subtitute the password you want for Collabora!
    $ docker run --privileged -t -d -p 127.0.0.1:9980:9980 -e 'domain=nextcloud\\.mousepawmedia\\.net|ajc\\.mousepawmedia\\.net|ibp\\.mousepawmedia\\.net' -e 'username=admin' -e 'password=CollaboraPassword' --restart always --cap-add MKNOD collabora/code

Cleaning Cruft
-------------------------------

You can check the current disk space with...

..  code-block:: bash

    $ df -l

You can also find what files are taking up the most space within a directory
by running the following command. For convenience, you may want to put this
into a script.

..  code-block:: bash

    du -hsx * | sort -rh | head -10

Docker will typically take up a lot of space over time. To remove old files
in Docker, run...

..  code-block:: bash

    $ sudo docker system prune -a --force

Another common place with lots of excess files is your ``/opt/minecraft``
directories, specifically the backups for each server. Be sure to regularly
clean these up.

Lastly, keep an eye on Jenkins for particularly large orphaned builds. If the
build cannot be viewed (and thus deleted) on the Jenkins web interface, you
may safely delete it from ``/var/lib/jenkins``.
