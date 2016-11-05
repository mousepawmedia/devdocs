Server Configuration
##############################################

This documentation outlines how the Hawksnest server was built.

Initial Installation
================================================

Installed Ubuntu 16.04 LTS x64 Server from DVD.
- Hostname: `hawksnest-server`
- Full name for user: `Hawksnest`
- Username: `hawksnest`
- Do **NOT** encrypt home folder.
- Set time zone to Pacific time.
- Partition Disks: **Guided - use entire disk** (not using LVM)
- No HTTP proxy.
- Selected `Install secure updates automatically`.
- Selected the following default packages:
-- `OpenSSH Server`
-- `LAMP server`
-- `Manual package selection`
- Install GRUB bootloader? `Yes`

..  NOTE:: Set passwords for both the `hawksnest` user and the MySQL root user.

Updates
===================================================

..  code-block:: bash

    # Install initial updates.
    $ sudo apt update
    $ sudo apt dist-upgrade

FSTAB
====================================================

Because we have another hard drive for overflow and file storage, we need to
add it to FSTAB. In our case, the partition is `/dev/sda5`, although it is
liable to change later when we've finished our setup, ensured everything is
moved over, and could reformat to use the *entire* disk.

We're also going to mount our backup drive.

First, we get the info about the attached drives.

..  code-block:: bash

    # NOTE: The argument is lowercase "L", NOT the numeral one.
    $ sudo fdisk -l

This will show us the names, sizes, and types for the drives. The `Linux`
type is generally `ext4`. We'll substitute the appropriate values in the
commands below.

..  code-block:: bash

    $ sudo mkdir /mnt/stash
    $ sudo mkdir /mnt/backup
    $ sudo mount -t ext4 /dev/sda5 /mnt/stash
    $ sudo mount -t ext4 /dev/sdc1 /mnt/backup

Now navigate to `/mnt/stash` and `/mnt/backup`, confirming that each
mounted correctly. If so, we can unmount and edit `fstab` so that the
mounts will be performed at startup.

..  code-block:: bash

    $ sudo umount /mnt/stash
    $ sudo umount /mnt/backup
    $ sudo vim /etc/fstab

Add the following lines, substituting the values from the earlier `mount`
commands as appropirate...::

    /dev/sda5 /mnt/stash ext4 defaults 0 2
    /dev/sdc1 /mnt/backup ext4 defaults 0 2

Save and close. Finally, run the following to remount using fstab. This
command will automatically skip over any drives already mounted, such as
your base file system, so it's safe to run.

..  code-block:: bash

    $ sudo mount -a

Double check `/mnt/stash` and `/mnt/media` to ensure they mounted correctly.

Logs and Scripts
===================================================

Let's define a folder in `/opt` for our own logs.

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

LAMP Server
===================================================

PHP5
----------------------------------------------------

Because Phabricator cannot work with PHP7.0, we have to downgrade.
We are using the `ondrej` PPA.

..  code-block:: bash

    # Remove php7 from the system
    $ sudo apt purge `dpkg -l | grep php| awk '{print $2}' |tr "\n " "`

    $ sudo add-apt-repository ppa:ondrej/php
    $ sudo add-apt-repository ppa:ondrej/php5-compat
    $ sudo apt update
    $ sudo apt dist-upgrade
    $ sudo apt install php5

    # Verify that PHP is on version 5.6
    $ sudo php -v

    # Install the libapache module.
    $ sudo apt install libapache2-mod-php5

    # Install the needed PHP packages.
    $ sudo apt install php5-cli php5-common php5-curl php5-dev php5-gd php-gettext php5-json php5.6-mbstring php5-mysql php5.6-opcache php5-readline

    # Finally, update the alternatives.
    $ sudo update-alternatives --config php
    # Select the option for php5.6

`SOURCE <http://askubuntu.com/a/109544/23786>`_

..  NOTE:: We are not installing APC because it is not supported on PHP5.6
    or above. `php5-opcache` handles that now.

SSH
------------------------------------------

..  NOTE:: For security reasons, the SSH port we changed to has been
    scrubbed to `123`.

The necessary packages for SSH were installed on installation. We need to
change a few settings and start the service.

..  code-block:: bash

    # Start the service to make sure it works.
    $ sudo systemctl start ssh

    # Make a backup of the default SSH configuration.
    $ sudo cp /etc/ssh/sshd_config{,.bak}

    # Edit the SSH configuration.
    $ sudo vim /etc/ssh/sshd_config

Change `Port 22` to `Port 123` and `PermitRootLogin prohibit-password` to
`PermitRootLogin no`. Also add the line `DebianBanner no` (you can put it under
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

`SOURCE <https://www.digitalocean.com/community/tutorials/how-to-use-ssh-to-connect-to-a-remote-server-in-ubuntu>`_

Java
-----------------------------------------

We're going to need Java for a few things, so let's install that now.

..  code-block:: bash

    sudo apt install default-jdk

Server Hardening
===========================================

Let's improve our system security before continuing.

`SOURCE <https://www.thefanclub.co.za/how-to/how-secure-ubuntu-1604-lts-server-part-1-basics>`_

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

At the bottom of the file, add the lines...::

    # Secure shared memory
    tmpfs /run/shm tmpfs defaults,noexec,nosuid 0 0

Save and close the file, and then restart the computer.

Lock Down `sudo` Privilege
--------------------------------------------------

We'll limit `sudo` privileges to only users in the `admin` group.

..  code-block:: bash

    sudo groupadd admin
    sudo usermod -a -G admin <YOUR ADMIN USERNAME>
    sudo dpkg-statoverride --update --add root admin 4750 /bin/su

Harden Network with `sysctl` Settings
------------------------------------------------------

..  code-block:: bash

    $ sudo vi /etc/sysctl.conf

Edit the file, uncommenting or adding the following lines.::

    # IP Spoofing protection
    net.ipv4.conf.all.rp_filter = 1de.li.cious
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

Finally, reload `sysctl`. If there are any errors, fix the associated lines.

..  code-block:: bash

    sudo sysctl -p

Prevent IP Spoofing
-------------------------------------------

To prevent IP spoofing, we edit `/etc/hosts`.

..  code-block:: bash

    sudo vim /etc/host.conf

Add or edit the following lines.

..  code-block:: apache

    order bind,hosts
    nospoof on

Harden PHP
---------------------------------------------

..  code-block:: bash

    sudo vim /etc/php/5.6/apache2/php.ini

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

    sudo vim /etc/apache2/conf-available/security.conf

Change or add the following lines...::

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

    sudo apt install libxml2 libxml2-dev libxml2-utils libaprutil1 libaprutil1-dev
    sudo ln -s /usr/lib/x86_64-linux-gnu/libxml2.so.2 /usr/lib/libxml2.so.2
    sudo apt install libapache2-mod-security2

Now we'll copy the default configuration and edit it.

..  code-block:: bash

    sudo mv /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
    sudo vim /etc/modsecurity/modsecurity.conf

Add and edit the lines...::

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

Add the following line just below the other `IncludeOptional` directive.

..  code-block:: apache

    IncludeOptional /etc/modsecurity/activated_rules/*.conf

Enable the modules and restart Apache2, ensuring that it still works.

..  code-block:: bash

    $ sudo a2enmod headers
    $ sudo a2enmod security2
    $ sudo systemctl restart apache2

Setup ModEvasive
--------------------------------------------

To harden against DDoS attacks, we'll install ModEvasive.

..  code-block:: bash

    $ sudo apt install libapache2-mod-evasive

For the `Postfix Configuration`, select `Local Only` and use the default
FQDN (`hawksnest-server.netgear.com`).

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
       DOSPageCount  5
       DOSSiteCount  50
       DOSPageInterval 1
       DOSSiteInterval  1
       DOSBlockingPeriod  10

       DOSLogDir   /var/log/mod_evasive
       DOSEmailNotify  hawksnest@hawksnest-server.netgear.com
       DOSWhitelist   127.0.0.1
       DOSWhitelist   192.168.254.*
    </ifmodule>

There is also a bug reported for Ubuntu 12.04 regarding email. I don't know
if it's fixed, but the workaround doesn't hurt anything anyway.

..  code-block:: bash

    sudo ln -s /etc/alternatives/mail /bin/mail/

Enable the modules and restart Apache2, ensuring that it still works.

..  code-block:: bash

    $ sudo a2enmod evasive
    $ sudo systemctl restart apache2

..  NOTE:: With DOSPageCount 2 and local network NOT whitelisted, Evasive
    locked out when I used PHPldapadmin.

`Read the Docs <https://www.linode.com/docs/websites/apache-tips-and-tricks/modevasive-on-apache/>`_

Setup DenyHosts
--------------------------------------------

DenyHosts blocks SSH attacks and tracks suspicious IPs.

..  code-block:: bash

    $ sudo apt install denyhosts
    $ sudo vim /etc/denyhosts.conf

Edit the following lines. I have this using the `hawksnest@mousepawgames.com`
account for both sending and receiving (because, why be redundant?)::

    ADMIN_EMAIL = hawksnest@mousepawgames.com
    SMTP_HOST = gator3102.hostgator.com
    SMTP_PORT = 587
    SMTP_USERNAME=hawksnest@mousepawgames.com
    SMTP_PASSWORD=(You Would Like To Know, Wouldn't You?)
    SMTP_FROM = DenyHosts <hawksnest@mousepawgames.com>
    #SYSLOG_REPORT=YES

Setup Fail2Ban
-----------------------------------------------

Fail2Ban does much the same things as DenyHosts, but its coverage includes
Apache, FTP, and other things.

..  code-block:: bash

    $ sudo apt install fail2ban
    $ sudo vim /etc/fail2ban/jail.conf

To turn on various "jails", scroll down to the `# JAILS` section. Place
`enabled = true` under each jail name you want turned on. This is the list
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

Restart the fail2ban process.

..  code-block:: bash

    $ sudo systemctl restart fail2ban

Setup PSAD
------------------------------------------

..  code-block:: bash

    $ sudo apt install psad
    $ sudo vim /etc/psad/psad.conf

Change "EMAIL_ADDRESS" to `hawksnest@localhost` and "HOSTNAME" to
`hawksnest-server`.

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

    sudo apt install nmap logwatch libdate-manip-perl apparmor apparmor-profiles tiger clamav

    # Ensure apparmor is working.
    sudo apparmor_status

To use logwatch, run...

..  code-block:: bash

    sudo logwatch | less

To scan for vulnerabilites with Tiger, run...

..  code-block:: bash

    sudo tiger
    sudo less /var/log/tiger/security.report.*

Server Controls
============================================

PHPMyAdmin
---------------------------------------------

..  code-block:: bash

    $ sudo apt-get update
    $ sudo apt-get install phpmyadmin

On the configuration dialog, select `apache2` by selecting it and tapping
:kbd:`Space`. Enter an application password (different from the MySQL root
password) and confirm it.

Now enable two necessary PHP modules and restart Apache2.

..  code-block:: bash

    $ sudo phpenmod mcrypt
    $ sudo phpenmod mbstring
    $ sudo systemctl restart apache2

Test Apache2 again, as always.

Next, we'll lock PHPMyAdmin down so it can only be used by anyone accessing
on the local network. This can be accomplished by modifying the Apache2
configuration for PHPMyAdmin.

..  code-block:: bash

    $ sudo vim /etc/apache2/conf-available/phpmyadmin.conf

Adjust the `<Directory /usr/share/phpmyadmin>` section to look like this.

..  code-block:: apache

    Options FollowSymLinks
    DirectoryIndex index.php
    AllowOverride none

    Order deny,allow
    Deny from all # Deny from everyone!!!!
    Allow from 127.0.0.1 # Allow from localhost
    Allow from 192.168.254.0/24 # Allow from local network

Restart the Apache2 server...

..  code-block:: bash

    $ sudo systemctl restart apache2

And then validate that you can `http://<serveraddress>/phpmyadmin`.

..  WARNING:: You may need to disable the Apache2 module `security2`
    before you can access PHPMyAdmin. Otherwise, it throws an internal 404.
    We're not sure why. To fix the problem, run `sudo a2dismod security2` and
    restart the Apache2 service.

DDClient
---------------------------------------

In addition to installing `ddclient`, we'll install a couple more tools we'll
be using later.

..  code-block:: bash

    $ sudo apt-get install iptraf ddclient screen
    $ sudo vim /etc/ddclient.conf

In the DDClient configuration file we just opened, set the following lines.::

    use=web. web='http://ip1.dynupdate.no-ip.com/8245/'
    protocol=noip
    ssl=yes
    login='theemailaddressfornoip@example.com'
    password='youknowwhatgoeswherewiseguy'

Also, at the end of the file, add...::

    hawksnest.ddns.net,hawksnest.serveftp.com,sparrowsgate.serveminecraft.net

Save and close. Next, we need to change how `ddclient` runs.

..  code-block:: bash

    $ sudo vim /etc/default/ddclient

Change the following settings to match the following...::

    run_dhclient="true"
    run_ipup="true"
    run_daemon="true"
    daemon_interval="300"

Save and quit. Finally, we restart `ddclient`.

..  code-block:: bash

    $ sudo systemctl restart ddclient

Let's Encrypt Certificates
============================================

We'll install the Let's Encrypt Certbot, and then create our server
certificates. While we can *technically* install the `letsencrypt` package,
it's out of date compared to `certbot-auto`.

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
    $ sudo /opt/certbot/certbot-auto certonly -a webroot --webroot-path /var/www/html -d mousepawmedia.net -d nextcloud.mousepawmedia.net -d phabricator.mousepawmedia.net -d ehour.mousepawmedia.net -d jenkins.mousepawmedia.net -d secure.mousepawmedia.net -d files.mousepawmedia.net

Of course, we would change the `hawksnest.ddns.net` part to match the domain
name we're getting the certificate for.

Follow the instructions on the screen to complete the process of getting the
certificates. If successful, they can be found (visible only as root) in
/etc/letsencrypt/live/hawksnest.ddns.net (change the folder name to match
the domain, of course).

Next, we need to create symbolic links to the certificates so Apache can see
them. We'll be `sudo`ing up to root after creating the directory.

Note we're only doing this for the fallback DNs. We'll need to
do something else for the main `mousepawmedia.net` certificate
(see `Post-Renew Script`).

..  code-block:: bash

    $ sudo mkdir /etc/apache2/ssl
    $ sudo su
    $ cd /etc/apache2/ssl
    $ mkdir /etc/apache2/ssl/filecert
    $ ln -s /etc/letsencrypt/live/hawksnest.ddns.net/cert.pem hawksnest/cert.pem
    $ ln -s /etc/letsencrypt/live/hawksnest.ddns.net/chain.pem hawksnest/chain.pem
    $ ln -s /etc/letsencrypt/live/hawksnest.ddns.net/fullchain.pem hawksnest/fullchain.pem
    $ ln -s /etc/letsencrypt/live/hawksnest.ddns.net/privkey.pem hawksnest/privkey.pem
    $ ln -s /etc/letsencrypt/live/hawksnest.serveftp.com/cert.pem filecert/cert.pem
    $ ln -s /etc/letsencrypt/live/hawksnest.serveftp.com/chain.pem filecert/chain.pem
    $ ln -s /etc/letsencrypt/live/hawksnest.serveftp.com/fullchain.pem filecert/fullchain.pem
    $ ln -s /etc/letsencrypt/live/hawksnest.serveftp.com/privkey.pem filecert/privkey.pem
    $ exit

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

    sudo groupadd certs

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

    a2dissite 000-redirect
    a2dissite ehour
    a2dissite jenkins
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
    a2ensite 000-redirect
    a2ensite ehour
    a2ensite jenkins
    systemctl restart apache2
    #systemctl restart jenkins
    #systemctl restart tomcat

Save and close. Change the script permissions so it can only be read, accessed,
and run by its owner and group (both root).

..  code-block:: bash

    sudo chmod u=rwx,g=rwx,o= renewcert_pre
    sudo chmod u=rwx,g=rwx,o= renewcert_post

Finally, we'll test the configuration.

..  code-block:: bash

    sudo /opt/certbot/certbot-auto renew --dry-run --pre-hook "/etc/apache2/ssl/mousepawmedia.net/renewcert_pre" --post-hook "/etc/apache2/ssl/mousepawmedia.net/renewcert_post"

Scheduling Auto-Renewal
------------------------------------------

Now we need to schedule the autorenewal task.

..  code-block:: bash

    sudo crontab -e

Add the following line to the end.::

    57 6 * * * /opt/certbot/certbot-auto renew --pre-hook "/etc/apache2/ssl/mousepawmedia.net/renewcert_pre" --post-hook "/etc/apache2/ssl/mousepawmedia.net/renewcert_post"

This will run the renewal script once a day at 11:42am. (Let's Encrypt asks
that a random time be used by each user, to spread out server load.)

LDAP Server
===============================================

Installation
------------------------------------

..  code-block:: bash

    $ sudo apt install slapd ldap-utils phpldapadmin
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

PHPldapadmin Config
-----------------------------------

Now we need to adjust PHPldapadmin's configuration.

..  code-block:: bash

    $ sudo vim /etc/phpldapadmin/config.php

Modify the following lines in the file.::

    $servers->setValue('server','name','MousePaw Games LDAP');
    $servers->setValue('server','host','hawksnest.ddns.net');
    $servers->setValue('server','base',array('dc=ldap,dc=mousepawmedia,dc=net'));
    $servers->setValue('login','bind_id','cn=admin,dc=ldap,dc=mousepawmedia,dc=net');
    $config->custom->appearance['hide_template_warning'] = true;

..  NOTE:: Genius moment alert. Make sure you modified the actual versions of
    the second and third lines, not a comment thereof. As to the fourth line,
    uncomment it and THEN make the changes.

Secure PHPldapadmin
-----------------------------------------------------------

Now, open up the Apache configuration for PHPldapadmin...

..  code-block:: bash

    $ sudo vim /etc/apache2/conf-available/phpldapadmin.conf

Adjust the `<Directory /usr/share/phpldapadmin/htdocs/>` section to look like
this.

..  code-block:: apache

    DirectoryIndex index.php
    Options +FollowSymLinks
    AllowOverride none

    Order deny,allow
    Deny from all # Deny from everyone!!!!
    Allow from 127.0.0.1 # Allow from localhost
    Allow from 192.168.254.0/24 # Allow from local network

Restart Apache (you geniuses should know how to do that by now) and check
`http://<serveraddress>/phpldapadmin`.

`SOURCE <https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-a-basic-ldap-server-on-an-ubuntu-12-04-vps>`_

Configuring LDAP Schema
--------------------------------

We will create two Organisational Units: `Groups` and `Users`. Under `Groups`,
add a `staff` and an `admin` Posix Group.

Next, add each staff member under `Users`. Remember to include the `Email`
field, and use `cn` for their MousePaw Games username.


eHour
=================================================

Installing Tomcat
-------------------------------------------------

..  code-block:: bash

    $ sudo groupadd tomcat
    $ sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
    $ cd /tmp
    $ curl -O http://apache.mirrors.ionfish.org/tomcat/tomcat-8/v8.5.5/bin/apache-tomcat-8.5.5.tar.gz
    $ sudo mkdir /opt/tomcat
    $ sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
    $ cd /opt/tomcat
    $ sudo chgrp -R tomcat /opt/tomcat
    $ sudo chmod -R g+r conf
    $ sudo chmod g+x conf
    $ sudo chown -R tomcat webapps/ work/ temp/ logs/
    $ sudo update-java-alternatives -l

..  NOTE:: For those of you following along at home, the JAVA_HOME is
    `/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre`!

Configuring Tomcat
------------------------------------------------------

We have to create the service file for Tomcat manually.

..  code-block:: bash

    $ sudo vim /etc/systemd/system/tomcat.service

Enter the following into the file::

    [Unit]
    Description=Apache Tomcat Web Application Container
    After=network.target

    [Service]
    Type=forking

    Environment=JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre
    Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
    Environment=CATALINA_HOME=/opt/tomcat
    Environment=CATALINA_BASE=/opt/tomcat
    Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
    Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'

    ExecStart=/opt/tomcat/bin/startup.sh
    ExecStop=/opt/tomcat/bin/shutdown.sh

    User=tomcat
    Group=tomcat
    UMask=0007
    RestartSec=10
    Restart=always

    [Install]
    WantedBy=multi-user.target

Now we can start up Tomcat.

..  code-block:: bash

    $ sudo systemctl daemon-reload
    $ sudo systemctl enable tomcat
    $ sudo systemctl start tomcat
    $ sudo systemctl tomcat status

If the status shows up all right, we're good!

Adjust Tomcat Port
---------------------------------------------------

We don't want to run Tomcat on 8080, but rather 8441. To change this...

..  code-block:: bash

    $ sudo vim /opt/tomcat/conf/server.xml

Find the connector for port="8080", and replace it with...::

    <!--
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    -->
    <!-- Define a SSL Coyote HTTP/1.1 Connector on port 8443 -->
    <Connector
           protocol="org.apache.coyote.http11.Http11NioProtocol"
           port="8441" maxThreads="200"
           scheme="https" secure="true" SSLEnabled="true"
           keystoreFile="/etc/apache2/ssl/mousepawmedia.net/keys.jks" keystorePass="thepassword"
           clientAuth="false" sslProtocol="TLS"/>

Make sure the password matches the one specified in the `renewcert_post` script
we defined earlier.

Save and close. Restart Tomcat, and open the appropriate port in the firewall!

..  code-block:: bash

    $ sudo ufw allow 8441
    $ sudo systemctl restart tomcat

Test Tomcat by going to `http://<serveraddress>:8441`.

Add Control Panel Users
--------------------------------------

Add users by editing this file...

..  code-block:: bash

    $ sudo vim /opt/tomcat/conf/tomcat-users.xml

Add the following lines to the file where appropriate.::

    <user username="admin" password="password" roles="manager-gui,admin-gui"/>

(Obviously, you should have replaced password with something intelligent.)

Next, modify each of the following two files with the same changes listed
below...

..  code-block:: bash

    $ sudo vim /opt/tomcat/webapps/manager/META-INF/context.xml
    $ sudo vim /opt/tomcat/webapps/host-manager/META-INF/context.xml

The change should be...::

    <Context antiResourceLocking="false" privileged="true" >
      <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
             allow="192\.168\.254\.\d+|::1|0:0:0:0:0:0:0:1" />-->
    </Context>

This locks the control panel to only be accessible from the local network.

Shutting Off Excess Stuff
------------------------------------------

There are a number of default Tomcat applications (`.war`s) that we don't
want running. Also, we want to replace the landing page.

..  code-block:: bash

    cd /opt/tomcat
    sudo su
    mkdir webapps-disabled
    mv webapps/docs webapps-disabled/
    mv webapps/examples webapps-disabled/
    mv webapps/ROOT webapps-disabled

We'll be putting eHour in place of the Tomcat root.

Installing eHour
------------------------------------

If you're performing a fresh installation of eHour, download the `.war` from
their website. However, since we're coming from a previous installation on
another machine, we'll just copy over the `.war` file and `$EHOUR_HOME`
directory.

In our case, the `$EHOUR_HOME` directory is `/opt/ehour-dist`.

Next, we need to create the database.

..  code-block:: bash

    $ sudo mysqladmin create ehour -u root -p

Enter the password for the MySQL root.

Next, we can either create a new, fresh eHour database, or import our old one
(which was exported to `ehour.sql` file on the old server and saved to the
`IMPORTED` folder on the new server's home directory.).

Both steps are shown below. Choose the one you want.

..  code-block:: bash

    # Create a new database...
    $ sudo mysql ehour -u root -p < /opt/ehour-dist/sql/mysql/install/fresh.mysql.sql
    #
    # OR
    #
    # Import old database...
    $ sudo mysql ehour -u root -p < /home/hawksnest/IMPORTED/ehour.sql

Now we'll configure Apache Tomcat to work with eHour.

..  NOTE:: On this server, the Tomcat `bin` directory is at `/opt/tomcat/bin`.

..  code-block:: bash

    sudo su
    cd /opt/tomcat/bin
    vim setenv.sh

Add the contents...::

    export EHOUR_HOME="/opt/ehour"

Save and close. Then we'll make that file executable.

..  code-block:: bash

    chmod +x setenv.sh
    exit

Next, we create a new user account for `ehour` on PHPMyAdmin. Give this
user privileges on the `ehour` database.

Next, we modify the configuration file for eHour.

..  code-block:: bash

    sudo vim ~/ehour-dist/conf/ehour.properties

Uncomment and modify the following lines as necessary...::

    # for mysql uncomment the following lines (and make sure postgresql lines below are commented out)
    ehour.database.driver=com.mysql.jdbc.Driver
    ehour.database.url=jdbc:mysql://127.0.0.1:3306/ehour?zeroDateTimeBehavior=convertToNull&useOldAliasMetadataBehavior=true
    ehour.database.username=ehour
    ehour.database.password=thepasswordforehour

Save and close.

Finally, copy the `.war` file from `/home/hawksnest/IMPORTED` where we first
put it, to `/opt/tomcat/webapps`, changing the file name to `ehour.war` so our
web address is prettier.

..  code-block:: bash

    sudo cp /home/hawksnest/IMPORTED/ehour-1.4.3.war /opt/tomcat/webapps/ROOT.war

Navigate to `http://<serveraddress>:8441/` to test the installation.

Apache2 Proxy
---------------------------------------

Let's set up a nice little proxy, so we can access port 8441 via port 443 or
port 80 on the eHour subdomain.

..  code-block:: bash

    sudo vim /etc/apache2/sites-available/ehour.conf

Set the contents of that file to...

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName ehour.mousepawmedia.net
            ServerAdmin hawksnest@mousepawgames.com

            SSLProxyEngine on
            ProxyPreserveHost On
            ProxyPass         /  https://ehour.mousepawmedia.net:8441/
            ProxyPassReverse  /  https://ehour.mousepawmedia.net:8441/
            ProxyRequests     Off
            AllowEncodedSlashes NoDecode

            SSLEngine on
            SSLCertificateFile  /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem
        </VirtualHost>

        <VirtualHost *:80>
            Servername ehour.mousepawmedia.net
            ServerAdmin hawksnest@mousepawgames.com

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            RewriteEngine On
            RewriteCond %{HTTPS} off
            RewriteRule ^/(.*)$ https://ehour.mousepawmedia.net/$1
        </VirtualHost>

        <VirtualHost *:8441>
            ServerName hawksnest.ddns.net
            RedirectMatch ^/(.*)$ https://ehour.mousepawmedia.net/$1

            SSLEngine on
            SSLCertificateFile /etc/apache2/ssl/hawksnest/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/hawksnest/privkey.pem
        </VirtualHost>
    </IfModule>

Save and close. Then, enable the needed mods and the site, and restart Apache2.

..  code-block:: bash

    $ sudo a2enmod proxy
    $ sudo a2enmod proxy_http
    $ sudo a2enmod ehour
    $ sudo systemctl apache2 restart

Test to ensure `http://ehour.<serveraddress>/` and
`https://ehour.<serveraddress>/` work.

..  IMPORTANT::

Phabricator
===========================================

Setting Up System Group and Users
--------------------------------------------

We'll add a group to control who can access Phabricator's stuff. For ease of
use, we'll add our login user to this group. We will also create a new
user called `phabdaemon` for Phabricator-based daemons.

..  code-block:: bash

    $ sudo groupadd phab
    $ sudo useradd -G phab phabdaemon
    $ sudo usermod -a -G phab hawksnest
    $ sudo usermod -a -G phab www-data

Now we need to modify the `phabdaemon` user.

..  code-block::

    sudo vim /etc/passwd

Look for the `phabdaemon` entry and set the last field to `/usr/sbin/nologin`.
Save and close. Then...

..  code-block::

    sudo vim /etc/shadow

Look for the `phabdaemon` entry again, and set the second field to `*`. Save
and close.

Migrating
--------------------------------------------

We already had the `phab`, `phabfiles` and `phabrepo` folders on the old
installation, so we can move those over to `/opt`. (See Phabricator's official
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

On the *new* server, we copy that backup to our `IMPORTED` directory, and then
run the following to move it into the new copy of MySQL.

..  code-block:: bash

    $ gunzip -c /home/hawksnest/IMPORTED/backup.sql.gz | mysql -u root -p

Enter the password, and then wait. You might take this opportunity to set
up a chess board and talk about playing badly. Don't count on actually
starting a game.

Configuring Apache
--------------------------------------------

We need to modify a few files to get this working. First, modify `apache2.conf`.

..  code-block:: bash

    $ sudo vim /etc/apache2/apache2.conf

Near the other `Directory` sections, add the section...

..  code-block:: apache

    <Directory "/opt/phab/phabricator/webroot">
            Require all granted
    </Directory>

Next, modify `ports.conf`...

..  code-block:: bash

    $ sudo vim /etc/apache2/ports.conf

Modify the file so it looks something like this (we only added the `Port 8446`
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

Finally, add a new site under `sites-available`.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/phab.conf

Copy and paste the following into that file.

..  code-block:: apache

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
                ServerName phabricator.mousepawmedia.net
                ServerAdmin hawksnest@mousepawgames.com

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
            RedirectMatch ^/(.*)$ https://phabricator.mousepawmedia.net/$1

            SSLEngine on
            SSLCertificateFile /etc/apache2/ssl/hawksnest/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/hawksnest/privkey.pem
        </VirtualHost>
    </IfModule>

Save and close the file. Finally, load them up.

..  code-block:: bash

    $ sudo a2ensite phab
    $ sudo a2enmod ssl
    $ sudo a2enmod php-5.6
    $ sudo a2enmod rewrite
    $ sudo ufw allow 8446
    $ sudo systemctl restart apache2

Now see if `https://<siteaddress>:8446/` works.

Getting Phabricator Running
-----------------------------------------------------

Next, we need to make some modifications to `php.ini` for Phabricator to work.

..  code-block:: bash

    $ sudo vim /etc/php/5.6/apache2/php.ini

Make these changes...

- Comment out `disable_functions`.

Next, we'll add a new user to MySQL using PHPMyAdmin, and give it
all privileges for the Phabricator databases.

Once we have these changes made, we need to adjust Phabricator's
configuration to access the database.

..  code-block:: bash

    $ ./bin/config set mysql.host localhost
    $ ./bin/config set mysql.user phab
    $ ./bin/config set mysql.pass thepasswordyouset

Set Log Locations
---------------------------------------------------

We need to set up the location for logging. We'll create a special folder
in `/opt` for this purpose, set its permissions, and tell Phabricator where
to find it.

..  WARNING:: This is critical! If you forget this, you'll have a plethora of
    `500 Internal Server Error` messages, an unhandled exception at the bottom
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
you can see these at `https://<serveraddress>:8446/config/issues`.

View each and fix as prescribed. Here are a few fixes we did...

- We made a few changes to `/etc/php/5.6/apache2/php.ini`.
- We had to make several changes to the MySQL configuration. If you're looking
  for the MySQL configuration file, it's spread out across multiple `.cnf`
  files in `/etc/mysql`. Chances are, you want
  `/etc/mysql/mysql.conf.d/mysqld.cnf`.
- We set the `php.ini` value `date.timezone = ` to `America/Los_Angeles`.
- We also needed to install the packages `python3-pygments` and
  `python-pygments`.
- We installed the package `php-apcu`. Don't worry about the `php-7`
  dependencies it dragged in. Apache2 is still using PHP5.6 because of our
  earlier settings changes. You can verify via `sudo php -v`.
- We installed the packages `subversion` and `imagemagik`.

Recaptcha
-------------------------------------------------

Sign up for Recaptcha on `their website <https://www.google.com/recaptcha/admin#list>`_
(I'm using indeliblebluepen@gmail.com to admin that). Then, substitute
`PRIVATEKEY` and `PUBLICKEY` in the following commands for the keys you get
from that website.

..  code-block:: bash

    $ ./bin/config set recaptcha.enabled true
    $ ./bin/config set recaptcha.public-key PUBLICKEY
    $ ./bin/config set recaptcha.private-key PRIVATEKEY

Setting Up Alternative File Domain
-------------------------------------------------

Our DNS service and certificates are already set up to provide a second
domain name - `hawksnest.serveftp.com` - which we can use as Phabricator's
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
            ServerAdmin hawksnest@mousepawgames.com
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

Go to `https://<filedomainname>:8446`. You **should** see an error on the page
saying "Unhandled Exception ("AphrontMalformedRequestException")"
This means you're on the right track!

Next, we'll configure Phabricator to use this domain name for file serving.

..  code-block:: bash

    $ cd /opt/phab/phabricator
    $ ./bin/config set security.alternate-file-domain https://files.mousepawmedia.net/

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

At the bottom, add the line...::

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
    `/var/tmp/phd`, just CAREFULLY run `sudo rm -r /var/tmp/phd`.

Phabricator Aphlict Notification Server
-------------------------------------------------------

Let's get the notification server for Phabricator running.

We need Node.JS for Aphlict to work. We can install it from the main
package repositories.

..  code-block:: bash

    $ sudo apt install nodejs npm
    $ cd /opt/phab/phabricator/support/aphlict/server/
    $ npm install ws

You can safely ignore the warning messages from `npm`.

Next, we'll add the `phabdaemon` user to the group that can view
the SSL certificates.

..  code-block:: bash

    $ sudo usermod -a -G certs phabdaemon

Now we need to adjust the Aphlict configuration, or it won't start.

..  code-block:: bash

    $ cd /opt/phab/phabricator/conf/aphlict
    $ cp aphlict.default.json aphlict.custom.json
    $ vim aphlict.custom.json

The file should look like this...::

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
Config→All Settings (`https://<serveraddress>:8446/config/all`). Look for
`notification.servers`. Enter the following in the field...::

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
(`https://<serveraddress>/config/cluster/notifications/`) to ensure
the system is running correctly.

If all's well, let's add the Aphlict startup to our PHD start script.

..  code-block:: bash

    $ sudo vim /opt/scripts/phab/phd_start

Add the line...

..  code-block:: bash

    sudo -u phabdaemon /opt/phab/phabricator/bin/aphlict start > /opt/log/phab/phd_start.log

Save and close.

`SOURCE <https://secure.phabricator.com/book/phabricator/article/notifications/>`_

Phabricator Git SSH
---------------------------------------------

The system already has a `www-data` user, and we set up a `phabdaemon` user
earlier. We'll use both of those for use for this. We also need to add a `git`
user, and then give these users appropriate sudo permissions.

..  code-block:: bash

    $ sudo useradd -m git
    $ /opt/phab/phabricator/bin/config set diffusion.ssh-user git
    $ sudo visudo

Add these lines to that file...::

    # Configuration for Phabricator VCS
    www-data ALL=(phabdaemon) SETENV: NOPASSWD: /usr/bin/git, /usr/lib/git-core/git-http-backend
    git ALL=(phabdaemon) SETENV: NOPASSWD: /usr/bin/git, /usr/lib/git-core/git-upload-pack, /usr/lib/git-core/git-receive

Also ensure that if there is the line `Defaults    requiretty`, it is commented
out. If it's not there, we're good.

Save and close.

Now, we need to edit a couple other files.

..  code-block:: bash

    $ sudo vim /etc/shadow

Find the line for `git` and change the `!` in the second field to `NP`. Save
and close.

Next, run...

..  code-block:: bash

    $ sudo vim /etc/passwd

Find the line for `git` and set (or change) the last field to `/bin/sh`.
Save and close.

Let's also add the `git` user to our `phab` group, so it can write to logfile
locations.

..  code-block:: bash

    $ sudo usermod -a -G phab git

Now let's configure the ports and SSH settings.

..  code-block:: bash

    $ /opt/phab/phabricator/bin/config set diffusion.ssh-port 2222
    $ sudo ufw allow 2222

Now we need to copy the SSH hook script to our scripts directory. We will
need to create a special subdirectory that is owned by root and has permissions
`755`, otherwise it won't start.

..  code-block:: bash

    $ cd /opt/scripts
    $ sudo mkdir root_scripts
    $ sudo chmod 755 root_scripts
    $ cd root_scripts
    $ sudo cp /opt/phab/phabricator/resources/sshd/phabricator-ssh-hook.sh ./phabricator-ssh-hook
    $ sudo chmod 755 ./phabricator-ssh-hook
    $ sudo vim ./phabricator-ssh-hook

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

    $ sudo cp /opt/phab/phabricator/resources/sshd/sshd_config.phabricator.example /etc/ssh/sshd_config.phabricator
    $ sudo vim /etc/ssh/sshd_config.phabricator

In that file, set the following lines...::

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

    echo {} | ssh git@hawksnest.ddns.net -p 2222 conduit conduit.ping

After all is said and done, it should print out something like
`{"result":"hawksnest-server","error_code":null,"error_info":null}`.

..  NOTE:: If it gives the message "Could not chdir to home directory
    /home/git: No such file or directory", that means you didn't create
    the `git` user with a home directory. If that's the case, you can add
    one by running `$ sudo mkhomedir_helper git` (on the server).

Once you're assured of this working, run...

..  code-block:: bash

    sudo /usr/sbin/sshd -f /etc/ssh/sshd_config.phabricator

Double-check functionality by re-running the earlier command on the
computer you SSH from. Run this two or three times to be certain.

..  code-block:: bash

    echo {} | ssh git@hawksnest.ddns.net -p 2222 conduit conduit.ping

If it works, then all's well! Add the sshd start command to the system cron.

..  code-block:: bash

    sudo crontab -e

On that file, add the line...::

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
    $ ./config set phabricator.allowed-uris '["https://hawksnest.ddns.net:8446/"]'
    $ ./config set phabricator.base-uri https://phabricator.mousepawmedia.net/

Then, revisit the other steps to ensure everything's working on the correct
domain names.


Jenkins
=================================================

Installation
----------------------------------------------------

We first need to install Jenkins and VirtualBox, both of which must be
ready to go before we can migrate the old Jenkins installation over.

..  code-block::

    $ sudo su
    $ wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | apt-key add -
    $ echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list
    $ wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
    $ echo deb http://download.virtualbox.org/virtualbox/debian xenial contrib > /etc/apt/sources.list.d/virtualbox.list
    $ exit
    $ sudo apt update
    $ sudo apt install jenkins virtualbox-5.1

Wait for the installation to complete. Next, we need to download the Extension
Pack. Find the latest download link from `their download page <https://www.virtualbox.org/wiki/Downloads>`_.

..  code-block::

    $ cd /tmp
    $ sudo wget http://download.virtualbox.org/virtualbox/5.1.8/Oracle_VM_VirtualBox_Extension_Pack-5.1.8-111374.vbox-extpack
    $ sudo vboxmanage extpack install --replace *.vbox-extpack
    $ sudo rm *.vbox-extpack

Now we're ready for migrating the old install.

If you're setting up Jenkins fresh, follow `this tutorial <http://www.indeliblebluepen.com/?p=901>`_
to get Jenkins and VirtualBox working together.

Migration
---------------------------------------------------

We copied the old `$JENKINS_HOME` folder to the new server, via...

..  code-block::

    $ sudo mv /var/lib/jenkins /var/lib/jenkins_new
    $ sudo rsync -av /mnt/stash/var/lib/jenkins/ /var/lib/jenkins
    $ sudo chown -R jenkins /var/lib/jenkins
    $ sudo chgrp -R jenkins /var/lib/jenkins

..  NOTE:: I originally couldn't start Jenkins, until I realized that
    I hadn't updated the owner and group.

We'll put the VirtualBox in `/opt`.

..  code-block::

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
where it looks for it's virtual hard drive (`.vdi`).

..  code-block:: bash

    $ VBoxManage registervm /opt/virtualbox/LittleXenial/LittleXenial.vbox
    $ VBoxManage list hdds

Get the UUID of the HDD you want to remove, and then substitute it for `UUID`
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

Add the following line...::

    @reboot VBoxManage startvm LittleXenial --type headless > /opt/log/vm.log

Configuration
--------------------------------------------------

Next, we need to modify Jenkins' configuration.

..  code-block::

    sudo vim /etc/default/jenkins

Change `HTTP_PORT=8080` to `HTTP_PORT=8449`. Then, place the following
at the bottom of the file, replacing the last line.::

    # Old HTTP mode (turned off)
    #JENKINS_ARGS="--webroot=/var/cache/$NAME/war --httpPort=$HTTP_PORT"

    # HTTPS mode. Make sure the password matches the PASS arg defined in /etc/apache2/ssl/mousepawmedia.net/renewcert_post
    # We also open port 8459 for HTTP, to allow Phabricator in. Have everyone use 8449 instead.
    JENKINS_ARGS="--webroot=/var/cache/$NAME/war --httpsPort=$HTTP_PORT --httpPort=8459 --httpsKeyStore=/etc/apache2/ssl/mousepawmedia.net/keys.jks --httpsKeyStorePassword=a674dRnZ15A6a4ByQ"

..  NOTE:: The password specified on the last line, by the
    `--httpsKeyStorePassword=`, must MATCH the password supplied when we
    set up the `renewcert_post` script under the Let's Encrypt section.

Finally, open the port for Jenkins...

..  code-block:: bash

    $ sudo ufw allow 8449
    $ sudo ufw allow 8459

Navigate to the Jenkins HTTPS URL on the server (`https://<serveraddress>:8449/`)
to test it out. Also, check the HTTP version that Phabricator uses
(`http://<serveraddress>:8459/`).

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
            ServerAdmin hawksnest@mousepawgames.com

            SSLProxyEngine on
            ProxyPreserveHost On
            ProxyPass         /  https://jenkins.mousepawmedia.net:8449/
            ProxyPassReverse  /  https://jenkins.mousepawmedia.net:8449/
            ProxyRequests     Off
            AllowEncodedSlashes NoDecode

            SSLEngine on
            SSLCertificateFile  /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem
        </VirtualHost>

        <VirtualHost *:80>
            Servername jenkins.mousepawmedia.net
            ServerAdmin hawksnest@mousepawgames.com

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            RewriteEngine On
            RewriteCond %{HTTPS} off
            RewriteRule ^/(.*)$ https://jenkins.mousepawmedia.net/$1
        </VirtualHost>
    </IfModule>

Save and close. Make sure the needed modules are enabled, enable the site,
and restart Apache2.

..  code-block:: bash

    $ sudo a2enmod proxy
    $ sudo a2enmod proxy_http
    $ sudo a2enmod jenkins
    $ sudo systemctl apache2 restart

Navigate to `http://jenkins.<serveraddress>/` and
`https://jenkins.<serveraddress>/`. It should quietly proxy over to the HTTPS
version of Jenkins (proxy 8449).

Test to ensure `http://jenkins.<serveraddress>:8459/` still works over HTTP,
without redirecting.

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

Now we need to adjust Apache2. First, modify `apache2.conf`.

..  code-block:: bash

    $ sudo vim /etc/apache2/apache2.conf

Add the following `<Directory>` entries below the ones that are already there.

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

Next, modify `ports.conf`...

..  code-block:: bash

    $ sudo vim /etc/apache2/ports.conf

Modify the file so it looks something like this (we only added the `Port 8442`
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

This file should look like this...::

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName mousepawmedia.net

            ServerAdmin hawksnest@mousepawgames.com
            DocumentRoot /opt/html/landing

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            SSLEngine on
            SSLCertificateFile     /etc/apache2/ssl/mousepawmedia.net/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/mousepawmedia.net/privkey.pem

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
            RedirectMatch ^/(.*)$ https://mousepawmedia.net/$1
        </VirtualHost>
    </IfModule>


Save and close. Open up the next.

..  code-block:: bash

    $ sudo vim /etc/apache2/sites-available/protected.conf

This file should look like this...::

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ServerName secure.mousepawmedia.net
            ServerAdmin hawksnest@mousepawgames.com

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

            #SSLCertificateFile     /etc/apache2/ssl/hawksnest/cert.pem
            SSLCertificateFile      /etc/apache2/ssl/hawksnest/fullchain.pem
            SSLCertificateKeyFile /etc/apache2/ssl/hawksnest/privkey.pem
            #SSLCertificateChainFile /etc/apache2/ssl/hawksnest/chain.pem

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

            RedirectMatch ^/(.*)$ https://secure.mousepawmedia.net/$1
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

Navigate to `http://<serveraddress>` and `https://secure.<serveraddress>` to test
the `landing` and `protected` sites respectively.

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

Save and close. Load the site, make sure `mod_rewrite` is enabled, and then
restart Apache2.

..  code-block:: bash

    $ sudo a2ensite 000-redirect
    $ sudo a2enmod rewrite
    $ sudo systemctl restart apache2

Navigating to `http://<serveraddress>` should now redirect properly to
Navigate to `https://<serveraddress>`. The same will apply for any subdirectory
thereof, such as `http://<serveraddress>/docs`.

..  NOTE:: Apache2 sites are loaded in alphabetical order. Addresses and ports
    are first come, first served, so the first site that defines on a port
    gets all addresses on that port, unless something else snatches away
    a specific address.

NextCloud
===========================

Installation
----------------------------

Let's install the other PHP packages we need for this. Most of these are
probably already installed, but we're putting them here to be certain.

..  code-block:: bash

    $ sudo apt install php5.6-bz2 php5.6-intl php5.6-xml php5.6-zip php5.6-curl php5.6-gd php-imagick php5.6-mbstring php5.6-ldap

Now we can install NextCloud itself.

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

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

            <Directory "/opt/nextcloud">
                Options +FollowSymLinks
                AllowOverride All

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

Add the following below the other `<Directory>` entries...

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

We need to create a `nextcloud` database and a `nextcloud` user in MySQL.
This can be done through PHPmyadmin.

nextCloud Configuration
------------------------------

On the nextcloud page, specify an admin account.

Click `Storage and Database`, set the Data folder to `/opt/nextcloud/data`.
Select `MySQL` for the database, and provide the database user, password,
and database name. The fourth field should be `localhost`.

Click `Finish setup`.

..  NOTE:: If you have problems logging into the database on this screen,
    check PHPmyadmin → `nextcloud` (database) → Privileges. The `nextcloud`
    user should be listed, with `Grant Yes`.

`SOURCE <https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-nextcloud-on-ubuntu-16-04>`_

Configuring Memory Caching
-----------------------------

To improve performance, we'll enable memory caching. We are using APCu (since
we're using PHP 5.6), so we simply need to enable this for NextCloud.

..  code-block:: bash

    $ sudo vim /opt/nextcloud/config/config.php

Add the following line before the end...::

    'memcache.local' => '\OC\Memcache\APCu',

Save and close, and then restart Apache2.

Set Up Cronjob
----------------------------

It is recommended to use Cron for background tasks. We will set this up now.

..  code-block:: bash

    $ sudo crontab -u www-data -e

Add the following line...::

    */15  *  *  *  * php -f /opt/nextcloud/cron.php

Save and close.

Finally, in the NextCloud Admin pane, go to `Cron` and select the `Cron` option.

`SOURCE <https://docs.nextcloud.com/server/10/admin_manual/configuration_server/background_jobs_configuration.html>`_

LDAP Authentication
--------------------------------

In NextCloud, go to `Apps` and enable LDAP. Then, go to `Admin` and `LDAP`.

Set the following options:

* Server
** Host: `localhost`
** Port: `389`
** Base DN: `ou=Users, dc=ldap, dc=mousepawmedia, dc=net`

Click `Test Base DN`, and then `Continue`.

Set `Only these object classes:` to just `inetOrgPerson`, and click
`Verify settings and count users`, and then `Continue`.

Check `LDAP/AD Username` and `LDAP/AD Email Address`, and then click
`Advanced.` Set...

* Directory Settings
** User Display Name Field: `cn`
** 2nd User Display Name Field: `sn`
** Base User Tree: `ou=Users, dc=ldap, dc=mousepawmedia, dc=net`
** Group Display Name Field: `cn`
** Base Group Tree: `ou=Groups, dc=ldap, dc=mousepawmedia, dc=net`
* Special Attributes
** Email field: `mail`

Click `Test Configuration`.

The settings are automatically saved. Log in as an LDAP user to test.

..  TODO:: Set up LDAP Avatar Integration.

`SOURCE <https://docs.nextcloud.com/server/9/admin_manual/configuration_user/user_auth_ldap.html>`_

Backups
======================================

With everything set up, we now need to configure regular backups to our
external drive `/mnt/backup`.

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

Now we need to adjust the settings for `automysqlbackup`.

..  code-block:: bash

    $ sudo vim /etc/default/automysqlbackup

Change the following values...::

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

This script will start up everything that `takedown` shut off.

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

We'll add the following line...::

    45 6 * * * /usr/sbin/automysqlbackup

This will run the backup every 6:35am. Save and close.

..  NOTE:: Make sure the server is set to auto-boot at 6:30am.

We can now test the configuration. Note that this may take a few minutes.

..  code-block:: bash

    $ sudo automysqlbackup

As `root`, navigate to `/mnt/backup/sqlbackup` to verify that the backup
completed (it's probably in `daily`).

Configuring Duplicity
--------------------------------
