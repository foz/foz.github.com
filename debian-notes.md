---
title: Debian Notes
layout: default

---

# Debian notes 
> _(many apply to Ubuntu and other distributions as well)_




### Package Management

I like to use [Aptitude](http://wiki.debian.org/Aptitude) which handles dependencies and helps me from doing really stupid things.

	$ aptitude update
	$ aptitude safe-upgrade
	$ aptitude dist-upgrade

list installed packages

	$ apt-show-versions
		# or...
	$ dpkg -l

To make sure we see important bugs and such, install (can also be annoying tho)

	$ aptitude install apt-listbugs

### Networking

	# show current config
	$ ip addr show

	# re-detect and display network interfaces
    $ ifconfig -a 

	# Make debian forget about a MAC address (useful in a virutal machine, for example:)
	$ rm -rf /etc/udev/rules.d/z25_persistent-net.rules


### Raid

	2.6 kernels currently default to using up to 200M/s for resync.  This
	causes seriously painful disk access while this is taking place.

	I personally always drop it to 10 or 15M/sec so that I can still work on
	the system while it syncs the raid.  This is done like this:

	$ echo 10000 > /proc/sys/dev/raid/speed_limit_max

	System becomes responsive again and the resync only takes about twice as
	long or so.  It certainly takes longer.

	[ http://lists.debian.org/debian-amd64/2005/10/msg00043.html ]

### When booting from Compact Flash card, a loop can occur that hangs the system

	This bug is still not solved. Adding the following "magic" line in
	persistant.rules solves the problem for me:
	
	BUS=="ide", SYSFS{block/removable}=="1", DRIVER!="ide-cdrom", \
	  GOTO="no_volume_id"

	debian/unstable, i386, udev 0.087-1, linux-image-2.6.15-1-686 2.6.15-8


### Apache2-related commands

	a2ensite
		Will create the correct symlinks in sites-enabled to allow the 
		site configured in sitefilename to be served
	a2dissite
		Will remove the symlinks from sites-enabled so that the site 
		configured in sitefilename will not be served
	a2enmod
		e.g. a2enmod php4 will create the correct symlinks in mods-enabled 
		to allow the module to be used. In this example it will link both 
		php4.conf and php4.load for the user
	a2dismod
		Will remove the symlinks from mods-enabled so that the module 
		cannot be used

### Samba hacks
	
Old versions of Samba (2.x) would encode file names using Code Page 850. 
The new Samba releases (3.x) now correctly use utf-8. 

The 'convmv' utility can convert a whole file system so the old crappy characters
work with the new code pages.
	
	# test conversion
	$ convmv -r -f cp850 -t utf8 -i --nfc .

	# actually do it
	$ convmv -r -f cp850 -t utf8 --notest --nfc .
	
### useful packages to install 

	modconf: manages kernel module installation and selections
	m-a: manages building and installing of third-party patches and modules
	dnsmasq: combined dns abd dhcp server, uses /etc/hosts and has simple configuration

### Boot Time

	update-grub: will write your changes to the /boot partition and process menu.lst macros

### Debian packages one may want to have around


	# dev tools
	autoconf
	build-essential
	git-core
	module-assistant
	subversion
	vim
	vim-scripts    # fancy extensions
	
	# utils
	sudo
	dnsutils
	lsof
	htop
	pciutils
	rcconf			# manage init scripts/services
	smartmontools
	tofrodos		# dos2unix and other tools
	unzip
	zip

	# networking
	dnsmasq			# dhcp and dns proxy
	mtr-tiny
	openssh-client
	openssh-server
	openssh-blacklist-extra
	iftop
	privoxy || tinyproxy
	netcat
	nload

    # Libraries
	libgcrypt11-dev 
	libmysqlclient15-dev 
	libpcre3-dev 
	libreadline5-dev 
	libssl-dev    
	mysql-client-5.0 
	
	
### how to mount a usb disk automatically when using udev

From http://www.kernel.org/pub/linux/utils/kernel/hotplug/udev-FAQ ...

	Q: Can I use udev to automount a USB device when I connect it?
	A: Technically, yes, but udev is not intended for this. All major distributions
	   use HAL (http://freedesktop.org/wiki/Software_2fhal) for this, which also
	   watches devices with removable media and integrates into the desktop software.

	   Alternatively, it is easy to add the following to fstab:
		 /dev/disk/by-label/PENDRIVE /media/PENDRIVE  vfat user,noauto 0 0

	   This means that users can access the device with:
		 $mount /media/PENDRIVE
	   and doen't have to be root, but will get full permissions on the device.
	   Using the persistent disk links (label, uuid) will always catch the
	   same device regardless of the actual kernel name.

	---

So to set up a new 250g usb drive, I connect a new usb drive and set the label:

	$ sudo e2fslabel /dev/sdx1 new250
	$ mkdir /media/new250
	
Then, edit fstab and add :

	/dev/disk/by-label/new250 /media/new250 

Now, unmount the drive and power cycle it. It can always be mounted with:

	$ mount /media/new250

### Set the label of an MSDOS (vfat) drive:

	$ sudo aptitude install mtools

edit /etc/mtools.conf:

	mlabel x:newname
	
x = the devic

### To see what partitions the system knows about, do :

	$ cat /proc/partitions

	$ fdisk -l

### To update the initial ramdisk (for kernel 2.6) :

	$ update-initramfs -u -t -k `uname -r`

### Promise controller has problems with Kernel 2.6.x and DMA, solution was to lower DMA:

	$ hdparm -X udma1 /dev/hdX

### To prevent fsck from being run on a disk:

	$ tune2fs -c 0 -i 0 /dev/sda1

### Check your BIOS rev from linux
	
	$ sudo dmidecode -s bios-version

### Diversions

	$ sudo aptitude install pacman4console cmatrix figlet

### add missing keyrings

    $ apt-get install debian-keyring debian-archive-keyring
    $ apt-key update
    
### Run apache2 using worker module (instead of the default, prefork):

	$ sudo aptitude install apache2-mpm-worker

### Non-interactive logins not using bashrc and PATH?

	the default ~/.bashrc file usually starts with something like this:
	# If not running interactively, don't do anything
	[ -z "$PS1" ] && return
	... comment out this line :)

	In /etc/ssh/sshd_config, you may need to add:
	PermitUserEnvironment yes

### Use Google public DNS with DHCP 

	# via http://code.google.com/speed/public-dns/docs/using.html
	sudo vi /etc/dhcp3/dhclient.conf
	prepend domain-name-servers 8.8.8.8, 8.8.4.4;


### boot up in better screen res (good for virtualbox)

	edit /boot/grub/menu.lst
	add "vga=314" to default_options= line
	$ update-grub
	reboot

### Copy a disk image from one server to another ###

    # dd if=/dev/xvda | gzip --fast -c | ssh user@target-server /bin/dd of=/backup/server_disk.img.gz
    
### use a variable name dynamically in bash ###

		$ var="what"
		$ what="cool!"
		$ echo ${!var}
		cool!

### List files by atime

You might want to see when files were **accessed** rather than modified:

		$ ls -lu

### Install debian kernel headers ###

    # aptitude install linux-headers-$(uname -r)

 
