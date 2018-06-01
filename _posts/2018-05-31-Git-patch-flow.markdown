---
layout: post
title: "Git patch flow"
date: 2018-05-31 19:15
categories: blog
tags: Git patch flow 
---

Git is a very flexible distributed source control system, so flexible in fact, that can be used in a variety of ways or flows. In this post, I describe a flow that I've been doing while working remotely and not having access to the central code repository.

### Getting the repo
As said, while not having [network] access to the repo implies getting it through other means, such as sneakernet, in my case, a shared flash drive.
The files in the flash drive are in a bare repo format, without a working copy.
I copy the entire repo to a special folder on my system called ‘Upstream’, where I keep all the bare repos’ copies. I use rsync to copy the folders from the flash drive to my hard drive copying only the deltas:

    $ rsync -avz /path/to/flash_drive/WorkFiles ~/Work/Upstream

### Cloning
Once the bare repo is on my system, cloning it is trivial:

    $ cd ~/Work/
    $ git clone ~/Work/Upstream/repo.git
    Cloning into 'repo'...
    done.

### Committing
Before I start making any changes, I create a new branch and name it according to the feature being worked on. This will prove useful later on.

    $ git branch new-feature

Now this is where all the changes are done to the files and committed, in one or more commits. The local repo gets all the commits; the *Upstream* repo is not touched.
For the purposes of this example, let's say there's 3 new commits done that we will need to send back to our remote repo.

    $ git log --oneline
    b67943b (HEAD -> new-feature) Add Fedora 28 tweaks post
    b275b34 Set tags to be listed horizontally in index.html
    13fc44d Fix open tag error in footer.html
    1689d56 (origin/master, origin/HEAD) Fix a few CSS validation errors
    ...

### Making a patch set
Now our feature is ready to be sent to the central repo, in the form of a patch set. We'll create these patches using the following command and using the last commit that came with the repo, ie. usually the 'master' branch.

    $ git format-patch 1689d56
    0001-Fix-open-tag-error-in-footer.html.patch
    0002-Set-tags-to-be-listed-horizontally-in-index.html.patch
    0003-Add-Fedora-28-tweaks-post.patch

Note the patches are made in the opposite order as listed by git-log: they are in chronological order, from older to newer. This is the order in which they will be applied.

### Sending the patch set
At this point we just need to send the patches in whichever way we can, sneakernet, email, etc. I normally use email, with the caveat that sometimes email servers change the file format of the patches, changing their line ending from Unix to DOS. To avoid this, I simply *zip* the patches into one container zipfile.

    $ zip patches.zip *.patch
    adding: 0001-Fix-open-tag-error-in-footer.html.patch (deflated 40%)
    adding: 0002-Set-tags-to-be-listed-horizontally-in-index.html.patch (deflated 19%)
    adding: 0003-Add-Fedora-28-tweaks-post.patch (deflated 32%)

Once the zipped file is ready, it's simply sent over email to the repo maintainer.

### Applying the changes
Well, this isn't part of my flow because applying the patches is not under my control: the Git repo maintainer receives my patches, reviews them and applies them if all works out. If something needs to be changed, it's sent back for more modifications, usually requiring an amended set of patches.
After *unzip*ing the patches, the maintainer applies them:

    $ git apply *.patch

### Getting a new update
Now all the changes are in the repo, including other changes done by the remote party. An update brings those updates (ie. commits) plus my commits as applied by the maintainer.
This requires updating my local bare repos as usually done and shown above using *rsync*. After the update, there is new commits in my *Upstream* repo that are not in my local repo. We need to synchronize them, and for that we use the *git-fetch* command. Git fetch updates the remote branches (ie. origin/master) but doesn't *merge* them.
This is when having a branch before starting a change comes in handy. Now we switch or *checkout* branch so we incorporate the changes from Upstream into our local repo's *master* branch.

    $ git branch -a
    * new-feature
    master
    remotes/origin/HEAD
    remotes/origin/master
    $ git checkout master

And we *merge* the changes from the Upstream branch, but doing a *fast-forward* merge:

    $ git pull --ff-only origin master

This updated *master* branch is the remote repo including the patches sent previously and applied by the maintainer. The old *new-feature* branch is now stale and eventually Git will *garbage-collect* it, or we can delete it manually with:

    $ git branch -d new-feature

### Conclusion
The Git patch flow works when network access to the remote repo is not available. It's pretty simple to work with and requires minimal changes to a regular flow.

