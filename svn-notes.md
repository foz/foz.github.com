---
title: SVN Notes
layout: default

---

# SVN

Create a new repository and import:

	$ svnadmin create /opt/svn/finances
	$ svn import -m 'initial import' finances file:///opt/svn/finances

Finding code
	
	check out the great "ack" package, installed via CPAN via package App::Ack

Ignore rails directories 

	$ svn rm log/* --force
	$ svn ps svn:ignore "*.log" log
	$ svn ps svn:ignore "*" tmp/cache tmp/pids 

Change the log message on a committed rev:

	svnadmin setlog --bypass-hooks /svn/path -r 1234 newlog.txt

To see what changes are pending:

    svn stat -u

To back up a rev

	svn merge -rHEAD:PREV filename
	
To convert from BDB to FSFS:

	- Make a new repo, svnadmin create /svn/myreposfsfs --fs-type fsfs.
	- svnadmin dump /svn/myrepos -q | svnadmin load /svn/myreposfsfs
	- rename repos and restart server

To copy over a new release from somewhere and overwrite your local changes:

	rsync -avC --delete [path to release] [working dir]
	svn st | grep '^!' | cut -c2-100 | xargs svn rm
	svn st | grep '^\?' | cut -c2-100 | xargs svn add
	svn ci -m 'update to [whatever] release'

View a colored diff in Textmate

	svn diff | mate

Find and replace code in a project:

	$ rak -al boo_u app test | xargs perl -pi -e 's/boo_u/boo_sun/g'

To convert a working copy between svn versions (1.5 to 1.6 for instance):

	$ /opt/local/share/subversion/tools/client-side/change-svn-wc-format.py . 1.6 --verbose

To make subversion ignore Mac files that get automatically created for icons and Finder settings, add the following line to ~/.subversion/config :

	global-ignores = ._* .DS_Store

