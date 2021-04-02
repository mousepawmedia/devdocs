Server Configuration
##############################################

We've documented most details relating to how we built our servers. This
serves (a) as a reference for ongoing maintenance, (b) a training
resource for anyone wanting to learn more about LAMP servers, and (c) a
way for others to provide feedback on our server architectures.

..  NOTE:: Of course, these guides are provided without warranty.

If you find errors, omissions, typos, or other problems, or otherwise want
to make a suggestion, please file a bug report on Phabricator.

Server Config Index
========================================

..  toctree::
    :maxdepth: 2

    hawksnest
    linode

Credits
==========================

Staff
---------------------------

- Bryan Anders: Jenkins & Tomcat setup.
- Jeff "Hawk" Clark: Building the server machine.
- Chris "Fox" Frasier: 2014 build support, 2016 build support.
- Jared Hutton: Penetration testing.
- Jason C. McDonald: Setup lead, server maintenance.

Thank Yous
-----------------------------

We've been helped by a whole lot of lovely people in configuring the servers
and solving problems.

- AtomicSpark (#ubuntu-offtopic): Unexpected security advice, help with HTML5,
  CSS, server configuration.
- avivey (#phabricator): Phabricator and SSL troubleshooting.
- big_t (#ubuntu-offtopic): Referred us to Linode.
- csutherl (#tomcat): eHour & Tomcat setup. (No longer in use.)
- kerframil (#bash): A critical piece of Perl code for the OpenDKIM renewal
  script.
- noteirak (#vbox): Jenkins VM build agent setup, Apache troubleshooting.
- Peng (#letsencrypt): SSL certificate technical support.
- phunyguy (#ubuntu-offtopic): Postfix/Dovecot advice.
- rfleming (#ubuntu-offtopic): For tireless debugging help in getting
  Postfix/Dovecot working.
- SCHAPiE (#ubuntu-offtopic): For fixing the OpenDKIM configuration.
- whitenoise (#letsencrypt): SSL certificate setup; originally pointing us to
  Let's Encrypt for certs.

"Very special thanks to Kevin at `PC Parts and Service <http://pcpartsandservices.com/>`_
for his stellar, ongoing technical advice! You've saved my hide more times
than I can count." -Jason
