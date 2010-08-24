---
title: Source Control Notes
layout: default

---

# CVS

To safely see what updates would be:

	$ cvs -n up

	# or even better

	$ cvs -nq up -d

To update and get new directories

	$ cvs up -d

To prune old stuff out

	$ cvs uo -P

To change a comment

    	$ cvs admin -m 1.7:"New message"

To import a new project

	$ cvs import -m "My Project (import)" sitename.com jeremy start

See a log of checkins for specific dates or by a specific user

	$ cvs log -d "2003-10-01 < 2003-12-31" -S -N -wjeremy

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


# Git basics

essentials

	git clone git://url	# checkout a copy
	git branch -a			# lists branches	
	git checkout origin/branch_name # checkout specific branch
	git diff				# show local changes
	git log					# list recent changes

to fix the previous log message (any you didnt push yet)
	
	$ git commit --amend

automatically assume a branch for 'git push' and 'git pull':

	# edit .git/config:

	[branch "master"]
	remote = origin
	merge = refs/heads/master

do garbage collection (for when you've been tracking big files)

	$ git gc
	
remove a file totally, from history too

	$ git rm --cached /some/dir/or/file

list remotes

	$ git remote -v
	

	