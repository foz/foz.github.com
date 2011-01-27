---
title: Debian Rails Setup
layout: default

---
# Debian Rails setup

This rough guide is based on the process taken to set up Rails hosting at Linode several times. The basic setup is always using:

* Linode Debian 5.0 (Lenny) VM
* Ruby on Rails
* Passenger/Nginx
* Ruby Enterprise Edition
* MySQL 5.0
* ImageMagick
* Bundler, Capistrano

The guide uses some generics for things:

* `mybox`: the server's short name
* `mybox.company.com`: the fully qualified domain name (FQDN) of the server
* `myapp`: the name of your rails app
* `myapp.com`: the FQDN of your rails app
* `jeremy`: the account name used by the developer (or server admin)
* `deployer`: the account named used for deploying the app
* `1.2.3.4`: the public IP address of the server
* `xxxxxxxxx`: some password you generated
* `/sites/myapp`: the path where the rails app lives on the server

## Set up DNS etc

Make sure DNS is working, so `mybox.company.com` goes to 1.2.3.4.

Add `mybox` and it's IP address to your local /etc/hosts file for easy access:

	1.2.3.4 mybox

## Start Server

Login to Linode, create server, boot, login as root.

	$ ssh root@mybox

## Install Base Packages

Edit your Apt sources in `/etc/apt/source.list`

	## main & security repositories
	
	deb http://ftp.us.debian.org/debian/ lenny main
	deb-src http://ftp.us.debian.org/debian/ lenny main
	deb http://security.debian.org/ lenny/updates main
	deb-src http://security.debian.org/ lenny/updates main

install aptitude

	# apt-get install aptitude
	
Update

	# aptitude update

Upgrade

	# aptitude dist-upgrade

This selection of packages covers most of the needed utilities and libs needed:

	# aptitude install autoconf build-essential git-core module-assistant subversion \
		vim vim-scripts dnsutils lsof htop rcconf tofrodos zip unzip openssh-client \
		openssh-server openssh-blacklist-extra iftop libgcrypt11-dev libmysqlclient15-dev \
		libpcre3-dev libreadline5-dev libssl-dev mysql-client-5.0 mysql-server-5.0 \
		locales libcurl4-openssl-dev libmagick++9-dev imagemagick wget sudo rsync \
		munin munin-node ntp

Note: You will be asked to pick a root password for mysql-server. Write it down!

## Configure

Set the hostname of the machine. Edit `/etc/hostname` and put in your server's short name ("mybox or whatever"). Then run the script:

	# /etc/init.d/hostname.sh

Edit `/etc/hosts` and put in the short name and FQDN:

	127.0.0.1 mybox localhost
	1.2.3.4 mybox.company.com  # whatever the primary public dns name is

Set the locale. Pick en-US to install and make it the default.

	# dpkg-reconfigure locales

Set the timezone. Run this and choose `None of the above` and finally `UTC`:

	# dpkg-reconfigure tzdata


# Create users

create your user account

	# adduser jeremy
	# adduser jeremy staff
	# adduser jeremy www-data

create an account for your deploy user	

	# adduser deployer
	# adduser deployer www-data

The deploy user will need some commands for setup and deploying Rails stuff. run `sudo visudo` to edit `/etc/sudoers` as root and, add:

	jeremy    ALL=(ALL) ALL # developer
	Cmnd_Alias RAILS_DEPLOY=/bin/cp, /bin/mv, /bin/ln, /bin/mkdir, /bin/chown, /bin/chmod, /bin/chgrp, /bin/rm, /etc/init.d/nginx
	deployer ALL=NOPASSWD: RAILS_DEPLOY
	
	# Also handy:

	Defaults env_keep="EDITOR"  # let me keep my damn editor
	Defaults: jeremy timestamp_timout=60  # cache password for 60m

Create a local tmp directory for the deploy user:

	$ sudo su - deployer 
	deployer@mybox $ mkdir tmp

## Connect and setup SSH Keys

Copy your SSH public key to the new box, and test to make sure you can log in:

	$ ssh-copy-id jeremy@mybox
	$ ssh jeremy@mybox uname -a
	
	$ ssh-copy-id deployer@mybox
	$ ssh deployer@mybox uname -a
	
Now set up your shell kit :)

# Ruby, Rails and MySQL
	
## Ruby Enterprise Edition

Get Ruby Enterprise edition at http://www.rubyenterpriseedition.com/download.html

	$ wget http://rubyforge.org/frs/download.php/71096/ruby-enterprise-1.8.7-2010.02.tar.gz
	
Expand, install. When it asks for the installation directory, accept the default path. Helpfully, it will also install rubygems, rake and some other things for you.
	
	$ tar -xvf ruby-enterprise-1.8.7-2010.02.tar.gz
	$ cd ruby-enterprise-1.8.7-2010.02
	$ sudo ./install

Add the REE bin path to your environment, add this line to .bashrc (for you, the deploy user, and for root):

	PATH="$PATH:/opt/ruby-enterprise/bin"

For running system gem installs, link the gem executable so it's in the sudo path:

	$ sudo ln -s /opt/ruby-enterprise/bin/gem /usr/local/bin/gem
	
And do the same for the passenger utils:

	$ sudo ln -s /opt/ruby-enterprise/bin/passenger* /usr/local/bin

## Install system-wide gems:

Bundler:

	$ sudo gem install bundler

MySQL gem:

	$ sudo gem install mysql -- --with-mysql-config=/usr/bin/mysql_config

Install rmagick:

	$ sudo gem install rmagick -v 2.12.2

## MySQL

Create a .my.cnf config file for your account:

	$ touch ~/.my.cnf
	
Edit it and add the MySQL root user info so you can manage databases easily:

	[client]
	user=root
	password=xxxxxxxxxxx

## Install nginx/passenger

Passenger will install nginx automatically:

	$ sudo /opt/ruby-enterprise/bin/passenger-install-nginx-module

Create an nginx startup script in /etc/init.d/nginx :

	#!/bin/bash
	#
	# Nginx init script
	#
	NGINX=/opt/nginx/sbin/nginx
	PID=/opt/nginx/logs/nginx.pid

	case "$1" in
	  start)
	        echo "Starting nginx..."
	        $NGINX
	        echo "."
	        ;;
	  stop)
	        echo "Shutting down nginx"
	        kill -15 `cat $PID`
	        sleep 1
	        echo "."
	        ;;
	  restart)
	        echo "Restarting nginx..."
	        $0 stop
	        echo "sleeping 2 seconds..."
	        sleep 2
	        $0 start
	        ;;
	  reload)
	        echo "Reloading nginx configuration..."
	        kill -1 `cat $PID`
	        ;;
	  conftest)
	        echo "Nginx config test..."
	        $NGINX -t
	        ;;
	  *)
	        echo "Usage: $0 {start|stop|restart|reload|conftest)"
	        exit 1
	        ;;
	esac

	exit 0
	
And make it executable:

	$ sudo chmod +x /etc/init.d/nginx
	
And make it start automatically on boot:

	$ update-rc.d nginx defaults
	
## Create the app db

Create the MySQL database and user needed by your app:
	
	$ mysql
	
	mysql> create database myapp_production;
	Query OK, 1 row affected (0.00 sec)

	mysql> grant all on myapp_production.* to myapp@localhost identified by 'xxxxxxxxxxx';
	Query OK, 0 rows affected (0.01 sec)

	mysql> flush privileges;
	Query OK, 0 rows affected (0.02 sec)
	
	mysql> exit

Then, edit the deploy user's `~/.my.cnf` to give them access.

	[client]
	user=myapp
	password=xxxxxxxxxx
	[mysql]
	database=myapp_production
	
## Setup Rails Deploy

Log in to the box as the deploy user:
	
	$ ssh deployer@mybox 
	
Verify the host keys for GitHub. Type `yes` when asked:
	
	deployer@mybox:-$ ssh git@github.com
	The authenticity of host 'github.com (207.97.227.239)' can't be established.
	RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
	Are you sure you want to continue connecting (yes/no)? yes
	...
	You've successfully authenticated ...
	... Connection to github.com closed.
	
	deployer@mybox:-$ exit
	
Using your dev account, set up the rails app directory:

	$ sudo mkdir -p /sites/myapp.com
	$ sudo chgrp www-data /sites/myapp.com
	$ sudo chmod 2775 /sites/myapp.com

From your local working copy of the app, try to run the Capistrano setup task and check the results. Both should work without errors. If there are problems, you should see them in this step and be able to fix them. 

	$ cap deploy:setup
	$ cap deploy:check
	
On the server, add a directory `shared/config` for putting config files:

	$ cd /sites/myapp.com
	$ sudo mkdir shared/config
	$ sudo chgrp www-data shared/config
	$ sudo chmod 2775 shared/*
	
Make a database.yml file in `shared/config/database.yml`, which should get copied into place after each deploy:

	production:
	  adapter: mysql
	  encoding: utf8
	  host: localhost
	  database: myapp_production
	  username: myapp
	  password: xxxxxxxxxx	

# Nginx configuration

Create an nginx config files and check into your git repo (in `config/server/nginx.conf` for example). Here's a sample:

	server {
		listen		1.2.3.4:80;
		server_name www.myapp.com;

		root		/sites/myapp.com/current/public;

		passenger_enabled on;
		passenger_use_global_queue on;
		rails_env production;

		access_log 	/sites/myapp.com.com/shared/log/nginx.log main;

		# allow big uploads
		client_max_body_size 20M;

		error_page 500 502 503 504  /500.html;

		if (-f $document_root/system/maintenance.html) {
		    rewrite ^(.*)$ /system/maintenance.html break;
		}
		if (-f $request_filename.html) {
		  rewrite (.*) $1.html break;
		}
	}

	# redirect bare url to www:
	server {
		listen          1.2.3.4:80;
		server_name 	myapp.com;
		rewrite ^(.*) 	http://www.myapp.com/;
	}
	

Edit `/opt/nginx/conf/nginx.conf` to adjust some things:

	worker_processes  4;
	error_log  logs/error.log;
	
	# uncomment: 
	log_format main ...
	
	tcp_nopush on;
	tcp_nodelay off;
	
	gzip  on;
    gzip_comp_level  2;
    gzip_min_length  1100;
    gzip_buffers     16 8k; 
    gzip_proxied     any;
    gzip_types       text/plain text/css application/x-javascript text/xml
                application/xml application/xml+rss text/javascript;

	server {
		server_name: mybox.company.com; # this is your default site
	}
	
At the end of the `nginx.conf` file, be sure to include you rails app config(s):
	
	include '/sites/myapp.com/shared/config/nginx.conf';	

# Deploy! 

Make sure your deploy script is set up for the new server:

* hostname/git branch/paths are set
* any secondary bits are deploying correctly, like NewRelic, Hoptoad, whenever, delayed_job, etc.
* after deploy, the links nginx into `shared/config` and copies `shared/config/database.yml` to `#{release_path}/config`
	
Try deploying:
	
	$ cap deploy

If everything works ok, then congrats :)
	
# Post-Install

Wait, there's still a number of things related to security and upkeep to install!

## Security Stuff

Edit /etc/ssh/sshd_config. Restrict ssh logins to certain users only.

	PermitRootLogin no
	AllowUsers jeremy deployer

Restart ssh:

	$ sudo /etc/init.d/ssh restart

Install fail2ban and denyhosts, to help keep the script-kiddies away.

	$ sudo aptitude install fail2ban denyhosts

Edit `/etc/sysctl.conf` and enable basic protection settings by reading the comments. More details and suggestions are [here](http://www.cyberciti.biz/faq/linux-kernel-etcsysctl-conf-security-hardening/).

## Set Up Mail

See my notes about [installing Postfix as a sending-only relay mailserver](postfix-notes.html).

## Epylog

I like [Epylog](https://fedorahosted.org/epylog/), which will send you an email every day summarizing any  user logins and unusual activity from the logs. Edit the config file in `/etc/epylog/epylog.conf`, and customize which messages are weeded out in `/etc/epylog/weed_local.cf`.

	$ sudo aptitude install epylog

## NTP

NTP is essential for keeping the server time correct. Just install, nothing to configure really:

	$ sudo aptitude install ntp
	
To check if NTP is connected to any servers, run:

	$ ntpq -c peers localhost

## Logrotate

logrotate is handy for keeping the web logs from ballooning:

	$ sudo aptitude install logrotate

Create `/etc/logrotate.d/nginx` and add this:

	/sites/*/shared/log/*log {
		missingok
		notifempty
		sharedscripts
		postrotate 
			test ! -f /var/run/nginx.pid || kill -USR1 `cat /var/run/nginx.pid`
		endscript
	}

Test that it will work ok (no changes, just verification):

	$ sudo logrotate -d /etc/logrotate.conf

## /etc with git

I like to put `/etc` under version control with git. Just in case I need to review a change, or roll back something. It's not a true security measure (no checksums etc) but its helpful. Sudo is required to do checkins.

	$ cd /etc
	$ sudo git init
	$ sudo git add .
	$ sudo git commit -am 'first checkin, new server installed'

## Backups

For backups, you probably want to take database snapshots, and make a copy of all user-uploaded files. These will be moved to another server for safekeeping. You may also want to back up config files.

For the database snapshots, a mysql backup script and cron job to run it every night works well. For example, [I use this one](https://gist.github.com/549062), which will make a dated dumpfile of each database and put them in a common directory on the web server, while removing the old ones.

On a separate backup server, set up SSH keys and a cron job to rsync files/db dumps from the web server. You might also consider using Amazon S3 for your off-site backups and pushing files to a bucket. Sensitive data always needs to be encrypted.
