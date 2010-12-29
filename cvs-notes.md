---
title: CVS Notes
layout: default

---

Thankfully, I don't really use CVS much, but it was the first version control I ever used. Here are some old notes I recorded for it.

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
