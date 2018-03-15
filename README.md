# Git Collaboration

## Objectives

1. Make a new branch for your repository with `git branch`.
2. Checkout a branch with `git checkout`.
3. Create and checkout a new branch with `git checkout -b`.
4. Create commits within a branch.
5. Merge branches with `git merge`.
6. Update branches from remotes with `git fetch`.
7. Merge updated remote branches with `git merge`.
8. Update and merge remote branches with `git pull`.

## Overview

A key to collaborating with git is to keep work on individual features for a project separate from the rest of the project. Consider the following scenario:

You start work on a big feature, making a few commits that don't entirely finish the feature. Your git log (a list of all commits on the repository – accessed with `git log` on the command line) might look like this:

```
512bec5 Still broken, working on new-feature (aviflombaum, 2 hours ago)
62d840 Almost done with new-feature (aviflombaum, 1 day ago)
fbee832 Started new-feature (aviflombaum, 2 days ago)
```

Two days ago we started working on our new-feature. Yesterday we were almost done. Today we made progress, but it's still broken. In our current state, if we had to deploy the latest version of our code to production, our users would see a half-finished, currently broken version of 'new-feature'. That's no good.

But no big deal, right? We can just wait until we're done with 'new-feature' to deploy our code. Here's what could happen, though: a big bug appears that is breaking the application for all users. It's an easy fix– one simple change to our code can make everything work again.

Unfortunately, once we've fixed the bug and committed our code, we have a problem. Let's take a look at the git log:

```
r4212d1 Fix to application breaking bug (aviflombaum, just now)
512bec5 Still broken, working on new-feature (aviflombaum, 2 hours ago)
62d840 Almost done with new-feature (aviflombaum, 1 day ago)
fbee832 Started new-feature (aviflombaum, 2 days ago)
```

See, we can't push our bug fix, because that would push all those commits– including our broken feature!

Wouldn't it have been great if we could have isolated our work on 'new-feature' into its own copy of our code? That way, until it's done we could have deployed our bug fix completely independently. Thankfully, We can do exactly this using a feature in git called branches.

## Making a branch with `git branch`

Let's quickly create a repository that we can use to experiment with the collaborative features of git. You'll be able to understand the concepts from the reading but if you'd like, but if you'd like to follow along you can copy and paste these commands locally in your command line.

From our home directory we're going to make a new directory for our mission-critical-application.

**Note: In documentation for terminal commands you will often see a dollar sign ($) before each command– this is NOT part of the code. Its purpose is to show which commands you, the user, will input.**

```
$ mkdir mission-critical-application
$ cd mission-critical-application
$ git init
$ touch application.rb
$ git add application.rb
$ git commit -m "First working version of application.rb"
```

1. We made a new directory with `mkdir mission-critical-application`.
2. We moved into that directory with `cd mission-critical-application`.
3. We turned that directory into a git repository with `git init`.
4. We created our application `touch application.rb`.
5. We programmed an entire working first version in `application.rb` (*not reflected in the commands above – our actual file is just blank, but we can pretend it's something amazing*).
6. We added our `application.rb` to git with `git add application.rb`.
7. We committed the first working version of our application with `git commit -m "First working version of application.rb"`.
8. We deployed our application to production (*this is also not reflected in the commands above*).

With our application online and customers rolling in, we notice a bug and quickly add a fix in the form of a file, `first-bug-fix.rb` (*this is just an example*).

```
$ touch first-bug-fix.rb
$ git add first-bug-fix.rb
$ git commit -m "First bug fix"
```

Right now our git log could be visualized as a timeline composed of two commits:

![First Two Commits](https://dl.dropboxusercontent.com/s/ikorf1qvvp4tay0/2015-11-02%20at%2011.15%20AM.png)

### About `master` branch.

Notice that these commits are occurring in a linear sequence of events, almost like a timeline? We call this timeline a branch. Whenever you are working on commits in git, you are adding them to a branch. The default branch when you start any repository is called master.

![Master Branch](https://dl.dropboxusercontent.com/s/v75as2cf6xr8n8a/2015-11-02%20at%2011.17%20AM.png)

`git status` will always tell you what branch you are on:

```
$ git status
On branch master
nothing to commit, working directory clean
```

One of the responsible ways to use git is to make sure that the `master` branch doesn't contain any broken code. That way if we ever need to add a bug fix it can be implemented and deployed immediately. Master should always have working code– often referred to as being 'clean'– so that it can always be deployed.

### Starting a new feature with `git branch new-feature`

To keep master clean, when start a new feature we should do it in an isolated feature branch. Our timeline will look as follows:

![Feature Branch](https://dl.dropboxusercontent.com/s/d61r0fxyriaf5oj/2015-11-02%20at%2011.52%20AM.png)

After commit 2, we will branch out of master and create a new timeline for commits specifically related to the new feature. The master timeline remains unchanged and clean. Now that we've covered the idea of the new-feature branch, let's actually make it.

To make a new branch simply type: `git branch <branch name>`. Let's pretend we were going to add a video chat functionality to our program. We'd name the branch `video-chat` like so:

```
$ git branch video-chat
```

To see a list of our branches we can type: `git branch -a`

```
$ git branch -a
* master
  video-chat
```

The `*` in front of the branch `master` indicates that `master` is currently our working branch and git tells us that we also have a branch called `video-chat`. If we made a commit right now, that commit would still be applied to our `master` branch.

### Switching branches with `git checkout`

We need to switch to our `video-chat` branch so that git knows that all commits made apply to only that branch. We can move between branches with `git checkout <branch name>`.

```
$ git status
On branch master
nothing to commit, working directory clean
$ git checkout video-chat
Switched to branch 'video-chat'
$ git status
On branch video-chat
nothing to commit, working directory clean
```

We started on `master` and then checked out our `video-chat` branch with `git checkout video-chat`, thereby moving into that timeline.

**Note: You can create and checkout a new branch in one command using: `git checkout -b new-branch-name`. That will both create the branch `new-branch-name` and move into it by checking it out.**

Let's make a commit in this `video-chat` branch and get the feature started by making a new file, `video-chat-file` to represent the code for the new feature.

```
$ touch video-chat-file
$ git add video-chat-file
$ git commit -m "Started new feature"
[video-chat 332a618] Started new feature
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 video-chat-file
```

You can see the commit we made was made in the context of the `video-chat` branch.

Imagine that right as we got started on that feature we got another bug report and had to move back into master to fix the bug. How do we move from `video-chat` branch back to `master`? What will our code look like when we move back to `master`– will we see the remnants of the `video-chat` branch and code represented by the `video-chat-file`?

#### Moving back to `master` with `git checkout master`

You can always move between branches with `git checkout`. Since we are currently on `video-chat`, we can move back to master with `git checkout master`.

```
$ git checkout master
Switched to branch 'master'
```

And we could move back to `video-chat` with `git checkout video-chat`

```
$ git checkout video-chat
Switched to branch 'video-chat'
```

And back again with:

```
$ git checkout master
Switched to branch 'master'
```

![Switching between branches](https://dl.dropboxusercontent.com/s/qzajqsd9f6njauc/2015-11-02%20at%2012.12%20PM.png)

From master, one thing you'll notice is that the code you wrote on `video-chat`, namely the file, `video-chat-file`, is not present in the current directory:

```
$ ls
application.rb first-bug-fix.rb
```

The master branch only has the code from the most recent commit relative to the master branch. The code from our `video-chat` is tucked away in its own branch, completely independent of the code in `master`.

Once you're on master you are free to make a commit to fix the bug, which we'll represent with a new file, `second-bug-fix.rb`.

```
$ touch second-bug-fix.rb
$ git add second-bug-fix.rb
$ git commit -m "Second bug fix"
```

Let's look at our timeline now.

![Commit on Master](https://dl.dropboxusercontent.com/s/9ipgkog7yv8hrok/2015-11-02%20at%2012.18%20PM.png)

We were able to update the timeline in master with the bug fix without touching any of the code in `video-chat`. The second bug fix commit occurred in master AFTER `video-chat` was created. `video-chat` is only aware of the commits at the moment when the branch was created. You could describe `master` as being one commit ahead of the `video-chat` branch.

Let's go back into `video-chat` to complete the feature and commit it. Remember how to move from `master` back to `video-chat`?

```
$ git status
On branch master
nothing to commit, working directory clean
$ git checkout video-chat
Switched to branch 'video-chat'
```

Let's rename `video-chat-file` to `video-chat` to mark it as a completed feature and commit this change. We can rename a file with `mv <original filename> <new filename>`.

```
$ mv video-chat-file video-chat
$ git add video-chat
$ git commit -m "Finished feature"
[video-chat bfe50fc] Finished feature
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 video-chat
```

Let's look at our timeline now.

![Completed Feature Branch](https://dl.dropboxusercontent.com/s/xtoehu7tv5zim6v/2015-11-02%20at%2012.31%20PM.png)

The final step is to figure out how to merge the `video-chat` branch into the master.

## Merging branches with `git merge`

Our goal is to bring the timeline of commits that occurred on the `video-chat` branch into the `master` so that at the end of the operation, our `master` timeline looks like:

![Merged Timeline](https://dl.dropboxusercontent.com/s/bf0cktf3ag549z2/2015-11-02%20at%201.15%20PM.png)

By merging the timelines, `master` will have all of the commits from the `video-chat` branch as though those events occurred on `master`.

When we merge a branch with `git merge`, it's important to be one the branch where you would like all of the commits to be consolidated. The first step for our `video-chat` merge is to checkout `master`:

```
$ git checkout master
Switched to branch 'master'
```

Now type: `git merge <branch name>` where `<branch name>` is the branch we want to merge into the current branch. In this case, `git merge video-chat` will do the trick:

```
$ git merge video-chat
Updating e5830af..bfe50fc
Fast-forward
 video-chat      | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 video-chat
```

Now the branches have been merged and if you use the `ls` command, you'll see the `video-chat` file from the `video-chat` branch in your current working directory that is on the `master` branch.

## Working with remote branches with `git fetch` and `git pull`

Your local branches can attach to remote branches that live on the internet, generally on GitHub, that your team members might contribute to and you can download locally.

Whenever you want to update your local copy with all the branches that might have been added to the GitHub remote, you can type `git fetch`.

```
$ git fetch
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 4 (delta 3), reused 3 (delta 2), pack-reused 0
Unpacking objects: 100% (4/4), done.
From github.com:aviflombaum/mission-critical-application
   bfe50fc..0ae1da2  master     -> origin/master
 * [new branch]      remote-feature-branch -> origin/remote-feature-branch
```

From within `master` (though technically what branch I was in when I typed `git fetch` does not matter), I executed `git fetch`. The last 3 lines of output are really important, let's take a closer look:

```
From github.com:aviflombaum/mission-critical-application
   bfe50fc..0ae1da2  master     -> origin/master
 * [new branch]      remote-feature-branch -> origin/remote-feature-branch
```

The first line, `From github.com:aviflombaum/mission-critical-application` is informing us which remote our `git fetch` updated from, namely, the remote repository located at: https://github.com/aviflombaum/mission-critical-application

When we `fetch` with git, we are asking to copy all changes on the remote to our local git repository, but not actually integrate any. The next line, `bfe50fc..0ae1da2  master     -> origin/master` is telling us that a new commit was found in `origin/master`. `origin/master` means the GitHub version of `master`. Even though git fetched a new commit from `origin/master`, it did not merge it into the local master.

![Fetch without integration](https://dl.dropboxusercontent.com/s/iy2jovft8ykrxbd/2015-11-02%20at%202.08%20PM.png)

Our remote copy on GitHub has a file, `remote-bug-fix`, presumably some code that another developer pushed up to our remote version of the `master` branch to fix a bug. Even after we fetched, our local copy still doesn't appear to have that file.

After you fetch, you have access to the remote code but you still have to merge it. How do you merge a change fetched from `origin/master` into your current master? From within your local master branch, type: `git merge origin/master`, referring to the branch's full path, `remote/branch`, or `origin/master`.

```
$ git merge origin/master
Updating bfe50fc..0ae1da2
Fast-forward
 remote-bug-fix | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 remote-bug-fix
$ ls
 application.rb		new-feature-file    new-feature		
 remote-bug-fix
```

The commits fetched via `git fetch` are now merged from the `origin/master` branch into our local `master` branch. And now `ls` reveals that the file present on the remote, `remote-bug-fix` is integrated into our local copy of `master` as well.

When we fetched, git also outputted: `* [new branch]      remote-feature-branch -> origin/remote-feature-branch`. Similarly, git fetched a new branch and if we want to check it out or merge it we can using `git checkout` or `git merge`. Let's checkout what code is on `remote-feature-branch`, a branch another developer made for another feature and pushed up to GitHub so they can share it with us.

```
$ git checkout remote-feature-branch
Branch remote-feature-branch set up to track remote branch remote-feature-branch from origin.
Switched to a new branch 'remote-feature-branch'
```

When we checkout a remote branch fetched, git will create a local branch to track that remote and switch to that branch. We can now do work, push it back up to GitHub, and another developer can fetch those changes down.

`git fetch` is a pretty low-level git command we don't use that much because it always requires two steps, first `git fetch` and then `git merge` to actually integrate those changes into your working branch. Generally, if you are in `master` you want to immediately `fetch` and `merge` any changes to the remote master.

### Combining `git fetch` with `git merge` by using `git pull`

If you want to both fetch and merge, which is what you want to do 99% of the time, just type `git pull`. `git pull` is literally the combination of both `git fetch` and `git merge`.

When you `git pull` the following things will occur:

1. You will `git fetch` all remote changes, including those on the current branch, existing branches, and new branches.
2. Any changes that are on a remote branch which is being tracked by your local branch, that is to say, if you are on `master` and there is a change to `origin/master`, those changes will be automatically merged.

## Conclusion

Git is complex, and collaborating with people in this matter is just hard - there's no easy way to allow 100s of people to all work on the same code base. These workflows are just being introduced to you.  You'll have lots of time to practice them and memorize what each command does. Don't try to learn it all at once; instead just start to get an understanding of what's what.

![XKCD Git](http://imgs.xkcd.com/comics/git.png)

<a href='https://learn.co/lessons/git-collaboration-readme' data-visibility='hidden'>View this lesson on Learn.co</a>

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/git-collaboration-readme'>Git Collaboration</a> on Learn.co and start learning to code for free.</p>

<p class='util--hide'>View <a href='https://learn.co/lessons/git-collaboration-readme'>Git Collaboration</a> on Learn.co and start learning to code for free.</p>
