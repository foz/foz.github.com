---
title: Git Notes
layout: default

---

# GIT

## Essentials

Working with an existing repo

	git clone git://url	# checkout a copy
	git branch -a			# lists branches	
	git checkout origin/branch_name # checkout specific branch
	git diff				# show local changes
	git log					# list recent changes

To automatically assume a branch for 'git push' and 'git pull':	

	# edit .git/config:

	[branch "master"]
	remote = origin
	merge = refs/heads/master

## tips

to fix the previous log message (any you didnt push yet)
	
	$ git commit --amend

To remove a file that is staged (ready for commit), use --cached. Note this does not remove the actual file on disk, it just removes it from the staging area.

	$ git rm --cached file

Note that although `git add` is recursive, `git rm` is not, so to remove a whole directory:

	$ git rm -r --cached some/directory/here

to undo a `git add` that was not yet committed:
	
	$ git reset .

or just reset everything back to the last commit:

	$ git reset --hard

do garbage collection (for when you've been tracking big files)

	$ git gc
	
list remotes

	$ git remote -v
	
after you add a files, you can do a diff of staging area vs HEAD, before you commit:

	$ git diff --cached

to see the diff of previous commits vs working copy:

	$ git diff @{1}     # <-- last commit
	$ git diff @{2}     # <-- last 2 commits	
	$ git diff @{10}    # <-- last 10 commits	
	
	$ git diff eb54dc40   # <-- part of the commit SHA 
	
to get rid of a bunch of changes

	$ git stash
	$ git stash drop
	
after a rejected push, do:

	$ git remote update && git rebase origin/master
	
	