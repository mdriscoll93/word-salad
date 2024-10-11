---
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
---

# Using Git - an Example

## Overview

In this section, we will work through a complete example of:

* Starting a Git-based project
* Adding files and resources to it
* Making changes to the working copy of the software
* Incorporating them in the repository, and thereby making them available to other developers

In succeeding sections, we will consider all these steps in detail.

### Objectives

By the end of this chapter, you should be able to:

* Know how to obtain a list of basic Git commands and help on their usage
* Work through a simple, yet thorough example which creates a project, adds components to it, modifies them and updates the repository in which the project is contained
* View the status and history of the project

### master vs main

Historically, a new repository was always created with an initial branch called **master**. However, many projects have deprecated this name and replaced it with **main**.

Reasons for this change and other changes of terminology, such as deprecation of the use of the words blacklist and whitelist, are explained in the [_"Word Replacement List"_](https://inclusivenaming.org/word-lists/overview/) article created by the Inclusive Naming Initiative.

Technically, this main branch could have any name, as there is nothing structurally special about this authoritative branch. However, Git has to have a default name for new repositories, and some web hosts have requirements that are hard to avoid.

GitHub has strongly recommended this naming convention change and has given [instructions](https://github.com/github/renaming) on how to create new repositories as well as rename old ones to have main as the main branch.

If you are creating a new repository the easiest way to do this from the outset is to do something like:

```bash
$ git init
$ git checkout -b main
```

For existing repositories you can rename both the local branch and the remote branch on the server with:

```bash
$ git checkout master
# Change the local name
$ git branch -m master main

# Change the remote name
$ git push -u origin main
$ git symbolic-ref refs/remotes/origin/HEAD refs/remotes/origin/main

# Confirm the names!
$ git branch -a
```

Depending on the setup of the remote server (such as GitHub) this may or may not work. An easier approach is to not delete the master branch but just copy it to main and work from there from now on, as in:

```bash
$ git checkout master
$ git branch main
$ git checkout main
$ git push -u origin main
```

and then just ignore master from now on.

### simple example

Let's get a feel for how git works and how easy it is to use. For now, we will just make our own local project.

First, we create a working directory and then initialize Git to work with it:

```bash
​$ mkdir git-test
$ cd git-test
$ git init
```

Initializing the project creates a **.git** directory which will contain all the version control information. The main directories included in the project remain untouched. The initial contents of this directory look like:

```bash
$ ls -l .git

total 40
drwxrwxr-x 7 coop coop 4096 Dec 30 13:59 ./
drwxrwxr-x 3 coop coop 4096 Dec 30 13:59 ../
drwxrwxr-x 2 coop coop 4096 Dec 30 13:59 branches/
-rw-rw-r-- 1 coop coop   92 Dec 30 13:59 config
-rw-rw-r-- 1 coop coop   58 Dec 30 13:59 description
-rw-rw-r-- 1 coop coop   23 Dec 30 13:59 HEAD
drwxrwxr-x 2 coop coop 4096 Dec 30 13:59 hooks/
drwxrwxr-x 2 coop coop 4096 Dec 30 13:59 info/
drwxrwxr-x 4 coop coop 4096 Dec 30 13:59 objects/
drwxrwxr-x 4 coop coop 4096 Dec 30 13:59 refs/
```

Later, we will describe the contents of this directory and its subdirectories; for the most part, they start out empty.

Next, we create a file and add it to the project:

```bash
​$ echo some junk > somejunkfile
$ git add somejunkfile
```

We can see the current status of our project with:

```bash
​$ git status

# On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#
#       new file: somejunkfile


```

Notice it is telling us that our file is _staged_ (added), but not yet _committed_.

Let's tell git who is responsible for this repository:

```bash
$ git config user.name "Another Genius"
$ git config user.email "a_genius@linux.com"
```

This must be done for each new project, unless you have it predefined in a global configuration file.

Now lets modify the file and then see the history differences

```bash
​$ echo another line >> somejunkfile
$ git diff

diff --git a/somejunkfile b/somejunkfile
index 9638122..6023331 100644
--- a/somejunkfile
+++ b/somejunkfile
@@ -1 +1,2 @@
some junk
+another line
```

To actually commit the changes to the repository, we do:

```bash
$ git add somejunkfile
$ git commit -m "My initial commit"

Created initial commit eafad66: My initial commit
1 files changed, 1 insertions(+), 0 deletions(-)
create mode 100644 somejunkfile
```

If you do not specify an identifying message to accompany the commit with the **-m** option, you will jump into an editor to put some content in. You must do this, or the commit will be rejected. The editor chosen will be what is set in your **EDITOR** environment variable, which can be superseded with setting **GIT\_EDITOR**.

You can see your history with:

```bash
$ git log

​commit eafad66304ebbcd6acfe69843d246de3d8f6b9cc
Author: A Genius <a_genius@linux.com>
Date: Wed Dec 30 11:07:19 2009 -0600

My initial commit
```

and you can see the information you got in there. You will note the long hexadecimal string which is the commit number. It is a 160-bit, 40-digit unique identifier which we will discuss later. git cares about these beasts, not about the file names.

**Signing Off on Commits**:

It is important to know who is responsible for all changes to the repository, in order to track history and to know who is guaranteeing that the code included is being done so with proper licensing and ownership.

This is done most easily by adding a Signed-off-by line to the commit with the **-s** option. So we could have done:

**`$ git commit -s -m "My initial commit"`**&#x20;

and then we would get:

```bash
commit eafad66304ebbcd6acfe69843d246de3d8f6b9cc
Author: A Genius <a_genius@linux.com>
Date: Wed Dec 30 11:07:19 2009 -0600
    My initial commit
    Signed-off-by: Another Genius <a_genius@linux.com>
```

Note that the person who does the signing need not be the author of the changes; they could be a maintainer or another reviewer, but they do take on any responsibilities required. Furthermore, there can be more than one person signing off on any given change.

You are now free to modify the already existing file and add new files with **git add**. But the changes are not actually placed fully in the repository until you do another **git commit**.

Now, that was not so bad. But we have only scratched the surface.
