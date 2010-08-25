---
title: MacOSX Stuff
layout: default

---

# Mac OSX stuff

## MacPorts

MacPorts gives you a command-line tool "port" that will install countless open source projects on your Mac and keep them up-to-date. Download at [macports.org](http://macports.org). For a Mac-based developer, it's incredibly useful to have. Here are some example commands:

	sudo port -d selfupdate   # update port system and sync
	sudo port upgrade outdated   # upgrade old ports
	sudo port livecheck [package] # for bleeding edge
	port installed   # show what we have
	port variants [package] # what +options can I add?
    port show [package]
	sudo port uninstall --follow-dependents all # DELETE ALL!!! 

I find MacPorts easier to use than the [Fink](http://www.finkproject.org/). There's an interesting alternative called [homebrew](http://mxcl.github.com/homebrew/) that uses Ruby and Git.

### Useful packages to install:
------------------------------

For development:

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


Shell Tricks
--------------

There's a ton of these at [Secrets](http://secrets.blacktree.com/).

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

Set the default screenshot format as JPEG (instead of the default, PNG):

	$ defaults write com.apple.screencapture type -string JPG

Change the location of screenshots to a different folder (the default is the Desktop)

  	$ defaults write com.apple.screencapture location ~/Desktop/screenshots

Enable debug mode in Safari

	$ defaults write com.apple.Safari WebKitDeveloperExtras -bool true


Snow Leopard, Ruby and MySQL
----------------------------

I was finally able to get the mysql 2.8.1 gem after a snow leopard upgrade. After trying many different builds, 64 vs. 32, etc, I ended up:

- removing the existing mysql bundle (usually only needed after upgrading from 10.5)

  	$ sudo rm /Library/Ruby/Site/1.8/universal-darwin10.0/mysql.bundle

- installing 64-bit mysql 5.0/5.1

	Go to mysql.com, download "Mac OS X 10.6 (x86_64)" as .dmg package

- build the MySQL gem with this command:

  	$ sudo env ARCHFLAGS="-arch x86_64" gem install -V mysql -- --with-mysql-config=/usr/local/mysql/bin/mysql_config


# MPlayer/mencoder

Again, macports helps here:

 	$ sudo port install mplayer-devel +mencoder_extras

To combine a bunch of movies:

 	$ mencoder -oac copy -ovc copy -o "joined.avi" "1.avi" "2.avi"


# Remote screen sharing, using SSH as tunnel


You have a mac that's on a remote network, but not publicly accessible. However, a Linux box on that network is.

On your local Mac, you can SSH to the remote box with port forwarding. In this example _192.168.1.10_ is the Mac on the remote network, while _host.box.com_ is the remote gateway (for example, a linux host):

 	$ ssh -L 1202:192.168.1.10:5900 user@host.box.com

This will open an ssh session, and the correct VNC ports are opened on your local machine. On your local Mac, use Command-K to connect to server. Enter this as the address:

 	vnc://localhost:1202



