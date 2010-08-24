---
title: Postfix Notes
layout: default

---

Postfix on Debian

### install

	$ sudo aptitude install libsasl2 libsasl2-modules postfix

### show configuration: 

	$ postconf

### restart

	$ /etc/init.d/postfix reload

### Sasl setup

	$ sudo chown root:root /etc/postfix/sasl_passwd 
	$ sudo chmod 600 /etc/postfix/sasl_passwd 
	$ sudo postmap hash:/etc/postfix/sasl_passwd 

### to delete a bunch of messages to/from a person in the queue

	$ mailq | tail +2 | awk 'BEGIN { RS = "" } / user@somedomain.com { print $1 } ' | tr -d '*!' | postsuper -d -


### config for normal relay

	  mynetworks = 127.0.0.0/8
	  mailbox_size_limit = 0
	  recipient_delimiter = +               
	  inet_protocols = all
	  relayhost = 
	  ### inet_interfaces = loopback-only
	  inet_interfaces = all

### lockdown for localhost

	sudo postconf -e 'inet_interfaces = 127.0.0.1'
	sudo /etc/init.d/postfix restartc
