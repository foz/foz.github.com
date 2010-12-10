---
title: Postfix Notes
layout: default

---

# Postfix on Debian

These notes are geared for using it as a simple relay, for instance on a web server that needs to send out mail notices to registered users. 

In this setup, the web server will not talk directly to other domains, or accept incoming connections. It will instead use am established remote SMTP server to relay mail (a company-run or ISP mail server, or even GMail) that supports authentication and SSL. We don't want problems with blacklists, talking to AOL/Earthlink, open relays and other headaches associated with running a mail server. 

In general, I find Postfix easier to understand and configure than EXIM (the Debian default for ages), and QMail (which I used for years, and still use for primary mail servers).

### Install

	$ sudo aptitude install postfix libsasl2 libsasl2-modules
	
When the install runs you will be asked how to configure the host. Select `local only`. You will then be asked for the hostname.

### Configuration: 

The main configuration file is in `/etc/postfix/main.cf`. These are the important settings to configure a local relay. We don't want the web server to accept outside mail or keep mailboxes. Outgoing messages are delivered to another mail server, via SSL. Notice that __relayhost__ is _blank_.

	myhostname = webserver.mycompany.net
	mynetworks = 127.0.0.0/8
	mailbox_size_limit = 0
	recipient_delimiter = +               
	inet_protocols = all
	relayhost = 
	inet_interfaces = 127.0.0.1

Make sure the following defaults are commented out:

	# default_transport = error
	# relay_transport = error

### Sasl setup

Sasl is useful if you are running a relay and want to connect to an SSL-based SMTP server. First you need to edit `/etc/postfix/sasl_passwd` and put in your relay info. It might look like this:

	$ cat /etc/postfix/sasl_passwd
	smtp.mycompany.net      web_user@mycompany.net:passwordxxx
	
Add this to `/etc/postfix/main.cf`:

	smtp_sasl_security_options = noanonymous 
	smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd 
	smtp_sasl_auth_enable = no

Then you have to ensure the permissions are correct for that file, and rebuild the "hash":	

	$ sudo chown root:root /etc/postfix/sasl_passwd 
	$ sudo chmod 600 /etc/postfix/sasl_passwd 
	$ sudo postmap hash:/etc/postfix/sasl_passwd 

### To delete a bunch of messages to/from a person in the queue

It's possible that messages may get backed up, maybe from a code error or something. Here's how to clear them out.

	$ mailq | tail +2 | awk 'BEGIN { RS = "" } / user@somedomain.com { print $1 } ' | tr -d '*!' | postsuper -d -


### postconf

The postconf utility lets you change settings via the command-line:

	sudo postconf -e 'inet_interfaces = 127.0.0.1'

### Reloading

	After changing things, here's the proper way to restart:

	$ sudo /etc/init.d/postfix reload

### Rails and Ruby Enterprise Edition

If you have problems sending mail and get a `OpenSSL::SSL::SSLError: hostname was not match with the server certificate` error, try turning off tls in `/etc/postfix/main.cf`:

	smtpd_use_tls=no
	
### Test script

Here's a Ruby test script that can be run at the webserver to test that Postfix and local relay is working correctly. If the messages comes through ok, examine the mail headers to make sure your relay host was used. 

	#!/usr/bin/ruby
	require 'net/smtp'
	FROM='me@localhost'

	def send_to(to)
	  message = <<MESSAGE_END
	  From: Foz <#{FROM}>
	  To: Myself <#{to}>
	  Subject: SMTP e-mail test

	  This is a test e-mail message. It Worked!
	MESSAGE_END

	  Net::SMTP.start('localhost') do |smtp|
	    smtp.send_message message, FROM, to
	  end
	end

	send_to('my_account@gmail.com')
	