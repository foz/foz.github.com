---
title: MacOSX Stuff
layout: default

---

Mac OSX stuff
=============

Darwin Ports
------------

see http://macports.org

	sudo port -d selfupdate   # update port system and sync
	sudo port upgrade outdated   # upgrade old ports
	sudo port livecheck [package] # for bleeding edge
	port installed   # show what we have
	port variants [package] # what +options can I add?
    port show [package]
	sudo port uninstall --follow-dependents all # DELETE ALL!!! 

Useful setup ports to install:
------------------------------

For osx development:

	ImageMagick
	git-core +svn +bash_completion
	openssh
	subversion +tools
	tidy

Media Libs

	GraphicsMagick
	flac
	freeimage
	freetype
	id3lib
	libid3tag
	libxml
	libmp4v2
	libogg
	libvorbis
	wavpack

    sudo port install ffmpeg +lame +libogg +vorbis +faac \
        +faad +xvid +x264 +a52

Other useful apps:

	bash-completion
	dos2unix
	gnupg
	htop
	ripmime
	wget

Network Utils:

	mtr
	ntop
	rdesktop
	iftop
	autobench
	netcat

Fun:

	cmatrix
	figlet


Mac Droppings (.DS_Store etc)
-----------------------------

To make subversion ignore mac files that get written to remote volumes:

  add the following line to ~/.subversion/config :

	global-ignores = ._* .DS_Store
	

Cron
----

- On OSX 10.4, cron files live in /var/cron/tabs
- On OSX 10.5, they are in /usr/lib/cron/tabs

Shell Tricks
--------------

To start the screensaver on the desktop:

	$ /System/Library/Frameworks/ScreenSaver.framework\
	/Resources/ScreenSaverEngine.app/Contents/MacOS/\
	ScreenSaverEngine -background &

Enable time machine over samba:

  	$ defaults write com.apple.systempreferences TMShowUnsupportedNetworkVolumes 1

To make hidden apps in the doc appear transparent (not reversable, but looks great)

  	$ defaults write com.apple.Dock showhidden -bool YES
  	$ killall Dock

Allow widgets to be dragged onto the desktop

  	$ defaults write com.apple.dashboard devmode YES
  	$ killall Dock

iTunes link arrows: use my library instead of linking to itunes store

  	$ defaults write com.apple.iTunes invertStoreLinks -bool YES

Set expanded save dialogs as default 
  	
	$ defaults write -g NSNavPanelExpandedStateForSaveMode -bool TRUE

Change the location of screenshots

  	$ defaults write com.apple.screencapture location /Full/Path/To/Folder

Enable debug mode in Safari

	$ defaults write com.apple.Safari WebKitDeveloperExtras -bool true


Snow Leopard, Ruby and MySQL
----------------------------

I was finally able to get the mysql 2.8.1 gem after a snow leopard upgrade. After trying many different builds, 64 vs. 32, etc, I ended up:

- removing the existing mysql bundle

  	$ sudo rm /Library/Ruby/Site/1.8/universal-darwin10.0/mysql.bundle

- installing 64-bit mysql 5.0/5.1

	Go to mysql.com, download "Mac OS X 10.6 (x86_64)" as .dmg installer

- building the gem with this command:

  	$ sudo env ARCHFLAGS="-arch x86_64" gem install -V mysql -- --with-mysql-config=/usr/local/mysql/bin/mysql_config


MPlayer/mencoder
----------------

Again, macports helps here:

 	$ sudo port install mplayer-devel +mencoder_extras

To combine a bunch of movies:

 	$ mencoder -oac copy -ovc copy -o "joined.avi" "1.avi" "2.avi"


Remote screen sharing, with SSH in the middle as tunnel
-------------------------------------------------------

You have a mac that's on a remote network, but not publically
accessible. However, a Linux box on that network is.

For example, the remote ssh host (the Linux box) is w.x.y.z.
192.168.1.10 is the mac's ip address on the remote network.

On you mac, ssh to the remote box with port forwarding:

 	$ ssh -L 1202:192.168.1.10:5900 user@w.x.y.z

Then, use Command-K to connect to server. Enter this address:

 	vnc://localhost:1202


