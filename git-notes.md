---
title: Git Notes
layout: default

---

# GIT

Git is a powerful source-control system that lets you manage your projects, have a backup and history on hand at all times, and share and collaborate with others. Git is not just about [Github](http://github.com), you can use it locally or privately to track your own code, documents, or anything really.

## Essentials

Working with an existing repo:

    git clone git://url # checkout a copy
    git branch -a     # lists branches  
    git checkout origin/branch_name # checkout specific branch
    git diff        # show local changes
    git log         # list recent changes

Add changes/new file(s), commit, then push:

    git add .
    git commit -am 'my log message'
    git push

after you add a files, you can do a diff of staging area vs HEAD, before you commit:

    $ git diff --cached


## Dealing with problems

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

or in a really messed up situation, with lots of untracked files or changes, you may need to:

    $ git clean -f -d

to get rid of a bunch of changes

    $ git stash
    $ git stash drop

to delete a branch

    $ git branch -d branch-name
    $ git push origin :branch_to_delete  # @ github!

after a rejected push, do:

    $ git remote update && git rebase origin/master

to revert a merge or large commit that was NOT yet pushed:

    $ git co master
    Switched to branch 'master'
    Your branch is ahead of 'origin/master' by 396 commits.
    
    $ git reset --soft HEAD~396
    
    $ git st
    # On branch master
    # Your branch is behind 'origin/master' by 648 commits, and can be fast-forwarded.
    #
    # Changes to be committed:
    # ... (lots of stuff)...
    
    $ git reset --hard
    HEAD is now at xxxx etc

    $ git st
    # On branch master
    # Your branch is behind 'origin/master' by 648 commits, and can be fast-forwarded.
    # ...
    
    $ git pull
    # ... (lots of stuff)...
    
    $ git st
    # ... (clean!)...
    
## Tips

To automatically choose a remote branch for `git push` and `git pull`, edit `.git/config`:

    [branch "master"]
    remote = origin
    merge = refs/heads/master

To do a rebase by default with `git pull`, add this to the branch in `.git/config`:

    [branch "master"]
    rebase = true

do garbage collection, save disk space (for when you've been tracking big files):

    $ git gc

list remotes

    $ git remote -v

to see the diff of previous commits vs working copy:

    $ git diff @{1}     # <-- last commit
    $ git diff @{2}     # <-- last 2 commits  
    $ git diff @{10}    # <-- last 10 commits 

    $ git diff eb54dc40   # <-- part of the commit SHA 

