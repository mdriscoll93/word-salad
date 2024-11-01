---
description: Chapter 7.
icon: code-commit
---

# Commits

## Chapter Overview

This section describes some essential commands and procedures when using Git; even a casual user should master these steps.

When it is time to make a change in a project's contents, a developer using Git commits one or more modified files. Each commit is identified with a 40-character hexadecimal hash. These can be specified with much shorter abbreviations for convenience. One can also specify tags at any point. These are simple character strings which are more descriptive and easier to reference.&#x20;

One can revert to any previous commit or tag with the `git checkout` command. It is easy to examine the history of a project and compare any two commits or tags with `git diff`. If necessary, one can revert to any earlier stage with a one line command, and one can also abandon a bad commit.&#x20;

Blaming lets one see exactly when specific files and lines have been modified, and both when and by who. Finally, using Git's bisection technology lets one identify exactly which commit was the one that caused problems.

## Learning Objectives

By the end of this chapter, you should be able to:

* Understand what a commit is and what different strategies can be used in making them
* Explain the role of hexadecimal identifiers and using convenient tags
* View the commit history of a project
* Know how to revert to an earlier state and how to reset a commit
* Clean up a repository and reduce its size without losing information
* Examine files and lines in files to see when they were added or modified and by whom
* Use bisection to discover when a bug was introduced rapidly

## Making a Commitment

The commit process is familiar to users of other version control systems. However, Git handles this process in a very particular way.

When a commit is made, a commit object is created from the files in the index and placed in the object store. The files themselves are already in the object store when they are placed in the index.

Any new files since the last commit result in new blobs, and any new directories result in new trees; any unchanged object is re-used. Thus, minimal new storage is required. Furthermore, this process is swift because blobs do not have to be compared directly. All Git has to do is compare their hexadecimal identifiers to see if they are identical. In particular, if the hash describing a directory has not changed, nothing in any subdirectory has changed.

The commits are connected in an ancestral tree. Later, when we discuss branching and merging, we will see it can be more complicated than a straightforward descent through the generations.

Your decision about how often you make commits is your decision. You should pick well-defined points, but your choice is whether you do many small commits or fewer larger ones. Note, however, that Git does handle large numbers of small commits very efficiently, and when one uses the bisection tool we will discuss, it can speed up finding points where bugs and regressions were introduced.

Committing can be done in several ways.

Let's say you have modified several files (i.e., they are staged), but you only want to commit the changes to one file:

```bash
$ git commit -s file1
```

If you want to commit all changes, any of these forms will do so:

```bash
$ git commit -s
$ git commit ./ -s
$ git commit -a -s
```

Here is a table that shows how the commit step works:

<table data-full-width="true"><thead><tr><th>Command</th><th>Source Files</th><th>Index</th><th>Commit Chain</th><th>References</th></tr></thead><tbody><tr><td><code>git commit</code></td><td>Unchanged</td><td>Unchanged</td><td>A new commit object is created from the index and added to the top of the commit chain</td><td><strong>HEAD</strong> in the current branch points to new commit object​</td></tr></tbody></table>

```bash
git diff
```

will show all the differences between your staged working directories and what has been previously committed. After you do the commit, it will show nothing differing.

## Identifiers and Tags

Every time you do a commit, Git assigns a unique 160-bit 40-character hexadecimal hash value to it. While you can refer to those commits with these values, it is unwieldy. For instance, taking an example from the Linux kernel source repository, we can see the history of commits with:

```bash
$ git log | grep "^commit" | head -10

commit 56b24d1bbcff213dc9e1625eea5b8e13bb50feb8
commit 5a45a5a881aeb82ce31dd1886afe04146965df23
commit ecade114250555720aa8a6232b09a2ce2e47ea99
commit 2c4ea6e28dbf15ab93632c5c189f3948366b8885
commit 106e4da60209b508894956b6adf4688f84c1766d
commit 4b050f22b5c68fab3f96641249a364ebfe354493
commit 84c37c168c0e49a412d7021cda3183a72adac0d0
commit 0acf611997d9d05dbfb559c3c6e379c861eb5957
commit 434fd6353b4c83938029ca6ea7dfa4fc82d602bd
commit 85298808617299fe713ed3e03114058883ce3d8a
```

If you want to refer to or revert a specific commit, typing in such a long string is a pain. You can, however, create a tag and use that instead:

```bash
$ git tag ver_10 08d869aa8683703c4a60fdc574dd0809f9b073cd
```

Or even better:

```bash
$ git tag ver_10 08d869
```

If you have used a long enough part of the 40-character string in the second example to make the reference unique, then the short version will suffice.

`git tag` Creates a tag or annotated tag (a text string referencing a commit object). The tag is placed in `.git/refs/tags` unless it is an annotated tag, in which case, the tag is created as an object in the object store:

<table data-full-width="true"><thead><tr><th>Command</th><th>Source Files</th><th>Index</th><th>Commit Chain</th><th>References</th></tr></thead><tbody><tr><td><code>git tag</code></td><td>Unchanged</td><td>Unchanged</td><td>Unchanged</td><td>A new tag is created</td></tr></tbody></table>

We will talk about checking things out later, but all you would have to do to revert to the development point labeled by **`ver_10`** would be:

```bash
$ git checkout ver_10
```

## Viewing the Commit History (1)

It is easy to display the history of commits with Git using the command `git log`. For example, consider the following script, which sets up a repository and then adds some files, modifies them, and introduces four commits along the way:

```bash
#!/bin/bash

rm -rf git-test
mkdir git-test

cd git-test
git init

git config user.name "A Smart Guy"
git config user.email "asmartguy@linux.com"

echo file1 > file1
git add file1
git commit -s -m "This is the first commit"

echo file2 > file2
git add file2
git commit . -s -m "This is the second commit"

echo file3 > file3
echo another line for file3 >> file3
git add .
git commit . -s -m "This is the third commit"

echo another line for file2 >> file2
git add .
git commit -a -s -m "This is the fourth commit"
```

If we then ask to see the log, we will see:

```bash
$ git log

commit 4b4bf2c5aa95b6746f56f9dfce0e4ec6bddad407
Author: A Smart Guy <asmartguy@linux.com>
Date: Thu Dec 31 13:50:15 2009 -0600

This is the fourth commit

commit 55eceacc9ab2b4fc1c806b26e79eca4429d8b52a
Author: A Smart Guy <asmartguy@linux.com>
Date: Thu Dec 31 13:50:15 2009 -0600

This is the third commit

commit f60c0c21764676beca75b7edc2f5f5e51b5dd404
Author: A Smart Guy <asmartguy@linux.com>
Date: Thu Dec 31 13:50:15 2009 -0600

This is the second commit

commit 712cbafa7ee0aaef03861b049ddc7865220b4e2c
Author: A Smart Guy <asmartguy@linux.com>
Date: Thu Dec 31 13:50:15 2009 -0600

This is the first commit
```

The commits are shown in reverse order in the introduction. Even shorter, one can do the following:

```bash
$ git log --pretty=oneline

4b4bf2c5aa95b6746f56f9dfce0e4ec6bddad407 This is the fourth commit
55eceacc9ab2b4fc1c806b26e79eca4429d8b52a This is the third commit
f60c0c21764676beca75b7edc2f5f5e51b5dd404 This is the second commit
712cbafa7ee0aaef03861b049ddc7865220b4e2c This is the first commit
```

One can also see the actual patches made with the **-p** option and can view only part of the history by specifying a particular commit, as in:

```bash
$ git log -p f60c

commit f60c0c21764676beca75b7edc2f5f5e51b5dd404
Author: A Smart Guy <asmartguy@linux.com>
Date: Thu Dec 31 13:50:15 2009 -0600

This is the second commit

diff --git a/file2 b/file2
new file mode 100644
index 0000000..6c493ff
--- /dev/null
+++ b/file2
@@ -0,0 +1 @@
+file2

commit 712cbafa7ee0aaef03861b049ddc7865220b4e2c
Author: A Smart Guy <asmartguy@linux.com>
Date: Thu Dec 31 13:50:15 2009 -0600

This is the first commit

diff --git a/file1 b/file1
new file mode 100644
index 0000000..e212970
--- /dev/null
+++ b/file1
@@ -0,0 +1 @@
+file1
```

There are many other options to `git log` which you can view by doing either `git help log` or `man git log`.

## Reverting and Resetting Commits

Occasionally, you may realize that you have made unwise changes. You may have made changes you never should have or committed changes prematurely.

You can back out a particular commit with:

```bash
$ git revert commit_name
```

where `commit_name` can be specified in several ways and need not be the most recent.

**Commits** can be delineated with:

* `HEAD`: most recent commit
* `HEAD~`: previous commit (the parent of **HEAD**)
* `HEAD~~` or
* `HEAD~2`: the grandparent commit of **HEAD**
* `{hash number}`: specific commit by complete or partial sha1 hash number
* `{tag name}`: a name for a commit.

Note that `git revert` puts in a new commit set of changes, i.e. a reversed patch as an additional commit. This is the appropriate thing to do if someone else has downloaded a tree containing the changes that have been reversed. This will change your working copy of source files.

In short, `git revert` builds and adds a new commit object, sets **HEAD** to it, and updates the working directory:

<table data-full-width="true"><thead><tr><th>Command</th><th>Source Files</th><th>Index</th><th>Commit Chain</th><th>References</th></tr></thead><tbody><tr><td><code>git revert</code></td><td>Changed to reflect reversion</td><td>Uncommitted changes discarded</td><td>New commit created No actual commits removed</td><td><code>HEAD</code> of current branch points to new commit</td></tr></tbody></table>

In the case where you are the only one that has seen the updated repository, the **git reset** command is more appropriate. For example, if you want to pull out the last two commits you can do:

```bash
$ git reset HEAD~2
```

This will not change your working copy of source files. It just reverses the commits and makes the index match the specified commit.

<table data-full-width="true"><thead><tr><th>Command</th><th>Source Files</th><th>Index</th><th>Commit Chain</th><th>References</th></tr></thead><tbody><tr><td><code>git reset</code></td><td>Unchanged</td><td>Discard uncommitted changes</td><td>Unchanged</td><td>Unchanged (unless form as used below; the <code>HEAD</code> of current branch moves to a prior commit)</td></tr></tbody></table>

When using any one of the options: `--soft`, `--mixed`, `--hard`, `--merge` or `--keep`, the behavior is different. In this case, the `HEAD` reference in the current branch is also set to the specified commit, optionally modifying the index and the source files to match:

* `--soft`: moves the current branch to the prior commit object (index unchanged in this case)
* `--mixed` (default): also updates the index to match the new head (un-stages everything)
* `--hard`: same as `--mixed`, but also updates the working directory to match the new head (un-edits your files).

Suppose you have been furiously coding and realize that your last three commits are still a work in progress, but everything before that should be made available to others. Then, it would be best if you created a new working branch while resetting the main branch back three commits as in:

```bash
$ git branch work
$ git reset --hard HEAD~3
$ git checkout work
```

Which restores the **`main`** Branch to its previous state while leaving the speculative work in **work**, where you will continue to play. We will discuss branches in detail later.

## Tidying Repositories

s your project grows through a series of commits, your repository may grow large. You can optimize and compact your repository by issuing `git gc` the following:

```bash
$ du -shc .git

47M .
47M total

$ git gc

Counting objects: 8, done.
Compressing objects: 100% (8/8), done.
Writing objects: 100% (8/8), done.
Total 8 (delta 2), reused 0 (delta 0)

$ du -shc .git

29M .git
29M total
```

**`gc`** stands for garbage collection.

You can also check your repository for certain kinds of errors with the command `git fsck`. The most likely and harmless errors it will find will be dangling objects; while these are sometimes useful for recovering corrupted repositories, they can generally be safely removed with `git prune` as in the following:

```bash
$ git prune -n
$ git prune
```

The first command checks to see what would be done, and if you are happy with it, you can issue the second pruning command.

## Who is to Blame?

It is possible to assign blame for whom is responsible for a given set of lines in a file.

For example, using `file2` the previous example:

```bash
​$ git blame file2

f60c0c21 (A Smart Guy 2009-12-31 13:50:15 -0600 1) file2
4b4bf2c5 (A Smart Guy 2009-12-31 13:50:15 -0600 2) another line for file2
```

shows the responsible commit and author. Specifying a range of lines and other parameters for the search is possible.

For example, for a more complicated file in the Linux kernel source:

```bash
c7:/usr/src/linux> git blame -L 3107,3121 kernel/sched/core.c​
e220d2dc kernel/sched.c (Peter Zijlstra 2009-05-23 18:28:55 +0200 3107)
e418e1c2 kernel/sched.c (Christoph Lameter 2006-12-10 02:20:23 -0800 3108) #ifdef CONFIG_SMP
6eb57e0d kernel/sched.c (Suresh Siddha 2011-10-03 15:09:01 -0700 3109) rq->idle_balance = idle_cpu(cpu);
7caff66f kernel/sched/core.c (Daniel Lezcano 2014-01-06 12:34:38 +0100 3110) trigger_load_balance(rq);
e418e1c2 kernel/sched.c (Christoph Lameter 2006-12-10 02:20:23 -0800 3111) #endif
265f22a9 kernel/sched/core.c (Frederic Weisbecker 2013-05-03 03:39:05 +0200 3112) rq_last_tick_reset(rq);
^1da177e kernel/sched.c (Linus Torvalds 2005-04-16 15:20:36 -0700 3113) }
^1da177e kernel/sched.c (Linus Torvalds 2005-04-16 15:20:36 -0700 3114)
265f22a9 kernel/sched/core.c (Frederic Weisbecker 2013-05-03 03:39:05 +0200 3115) #ifdef CONFIG_NO_HZ_FULL
265f22a9 kernel/sched/core.c (Frederic Weisbecker 2013-05-03 03:39:05 +0200 3116) /**
265f22a9 kernel/sched/core.c (Frederic Weisbecker 2013-05-03 03:39:05 +0200 3117) * scheduler_tick_max_deferment
265f22a9 kernel/sched/core.c (Frederic Weisbecker 2013-05-03 03:39:05 +0200 3118) *
265f22a9 kernel/sched/core.c (Frederic Weisbecker 2013-05-03 03:39:05 +0200 3119) * Keep at least one tick per second when
265f22a9 kernel/sched/core.c (Frederic Weisbecker 2013-05-03 03:39:05 +0200 3120) * active task is running because the sch
265f22a9 kernel/sched/core.c (Frederic Weisbecker 2013-05-03 03:39:05 +0200 3121) * yet completely support full dynticks
```

## Bisecting

Suppose you had a version of your code that worked, and now, many versions (and commits) later, you have found out that it is no longer working.

Git can bisect to rapidly find the change set that screwed things up. Then, the number of steps is no more than the logarithm to the base 2 of the number of commits, which is much faster than a brute force approach. in other words, if a bad change has been done somewhere in the hast 1024 commits, you can find it in no more than 10 bisection steps.

First, you need to do the following:

```bash
$ git bisect start
$ git bisect bad
$ git bisect good V_10
```

Where it is assumed that the current commit is bad and version V\_10 is known to be good, Git will then leave you at a commit halfway in between. You then test the code to see if the bug is still there.

If the bug is still present, type:

```bash
$ git bisect bad
```

If the code does not have the bug yet, type:

<pre class="language-bash"><code class="lang-bash"><strong>$ git bisect good
</strong></code></pre>

You continue this iteratively until you find the bug.

Then you type:

```bash
$ git bisect reset
```

to get back to your current working state.

***

For a working example, let's try the Linux kernel repository:

```bash
$ git bisect start
$ git bisect bad
$ git bisect good v2.6.30

Bisecting: 16539 revisions left to test after this
[b4f3fda5d475931d596d5cf599a193f42b857594] Staging: hv: coding style cleanup of include/HvVpApi.h

$ git bisect bad

Bisecting: 8270 revisions left to test after this
[0de4adfb8c9674fa1572b0ff1371acc94b0be901] Blackfin: fix accidental reset in some boot modes

$ git bisect good

Bisecting: 4136 revisions left to test after this
[b9caaabb995c6ff103e2457b9a36930b9699de7c] Merge branch 'main' of
git://git.kernel.org/pub/scm/linux/kernel/git/holtmann/bluetooth-next-2.6
.........

$ git bisect good

Bisecting: 60 revisions left to test after this
[5d48a1c20268871395299672dce5c1989c9c94e4] Staging: hv: check return value of device_register()
........

/usr/src/GIT/work>git bisect bad
Bisecting: 3 revisions left to test after this
[b57a68dcd9030515763133f79c5a4a7c572e45d6] Staging: hv: blkvsc: fix up driver_data usage

$ git bisect good

Bisecting: 1 revisions left to test after this
[511bda8fe1607ab2e4b2f3b008b7cfbffc2720b1] Staging: hv: add the Hyper-V virtual network driver

$ git bisect good

Bisecting: 0 revisions left to test after this
[621d7fb7597e8cc2e24e6b0ca67118b452675d90] Staging: hv: netvsc: fix up driver_data usage

$ git bisect reset
```

If a script can be constructed to test the current version to see if the bug is present, the process becomes even more accessible.

Suppose you have written a script `bisect_test.sh`that returns **0** if the current version is good, and any value between **1** and **127** if it is bad.&#x20;

```bash
#!/bin/bash

# Attempt to build the project. You can replace `make` with whatever build command your project uses.
make clean && make

# Capture the return status of the make command
status=$?

# If the build is successful, the status will be 0 (indicating a "good" commit).
# If the build fails, we return a non-zero value (indicating a "bad" commit).
if [ $status -eq 0 ]; then
    exit 0  # good
else
    exit 1  # bad
fi
```

Then, after initializing the bisection with a good and bad version, you can do the following:

```bash
$ git bisect run ./bisect_test.sh
```

and the process will terminate when it locates the bug.

You can replay the bisection history with `git bisect log` or `git bisect` **visualize**.

If you do small incremental changesets, bugs can quickly be found with bisection. Commits with many changes will mean quite a few places that may have to be examined even after you identify the last working version and the first faulty one.

### Review Lab

{% tabs %}
{% tab title="7.1 - question" %}
1. First, initialize and configure a repository with name, email address, etc.
2. Then, make a significant number of commits (say 64). Each commit should add a file. In one of the commits, have the fine include the string `BAD`. We will interpret this as the bug introduction
3. Start a `git bisect` procedure. Designate the last commit with:

```bash
$ git bisect bad

# and the first one with:

$ git bisect good
```

4. You can do this manually or use the `git bisect run` procedure with a script to automate it.
5. When done, check the history of your bisection with `git bisect log`
{% endtab %}

{% tab title="7.1 - answer" %}
```bash
# 1. 
# Initialize the repository and put our name and email in the .config file.

echo -e "\n\n************* CREATING THE REPOSITORY AND CONFIGURING IT\n\n"

rm -rf git-test ; mkdir git-test ; cd git-test
git init
git config user.name "A Smart Guy"
git config user.email "asmartguy@linux.com"
```

```bash
# 2. 
echo -e "\n\n************* CREATING A NUMBER OF FILES AND ADDING"
echo -e " THEM TO THE PROJECT AND COMMITTING\n\n"

n=0
while [ $n -lt 64 ] ; do
    n=$(($n+1))
    file=file$n
    echo file > $file
    if [ "$n" == "19" ] ; then
        echo BAD >> $file
    fi
    git add $file
    git commit $file -s -m"$file"
    git tag $file
    echo I added and committed $file
done

echo -e "\n\n************* I PUT THE BAD LINE IN file19\n\n"
```

```bash
# 3.
echo -e "\n\n************* STARTING THE BISECTION\n\n"

git bisect start
git bisect bad
git bisect good file1

echo -e "\n\n************* SEARCHING FOR THE BAD FILE\n\n"

echo -e "\n\n************* DOING TH BISECTION MANUALLY\n\n"

over=0
while [ "$over" == "0" ] ; do
    if [ "$(grep BAD file*)" == "" ] ; then
        git bisect good | tee gitout
    else
        git bisect bad | tee gitout
    fi

    if [ "$(grep 'revisions left' gitout)" == "" ] ; then
        over=1
            echo "****************** FOUND THE BUG!"
    fi
done
```

```bash
# 4.
echo -e "\n\n*************** SETTING UP A TESTING SCRIPT\n\n"

cat < my_script.sh
#!/bin/bash

if [ "\$(grep BAD file*)" == "" ] ; then
    exit 0
fi
exit 1
EOF
chmod +x my_script.sh

# reset to the original state

git reset

git bisect start
git bisect bad file64
git bisect good file1

# do automated script

git bisect run ./my_script.sh
```

```bash
# 5.
# Check log

git bisect log

```
{% endtab %}
{% endtabs %}

You can download a script with the above steps from `s_07/lab_bisect.sh` in your **`SOLUTIONS`** file. If you have not yet downloaded the **`SOLUTIONS`** file, please see the _Lab Exercises - Files to Download_ page in the _Welcome_ chapter for instructions.

### Review Questions

1. What does the command `git revert c87e6ae4` do?
   * [x] Removes the changes associated with the commit that starts with `c87e6ae4`
   * [ ] Places the repository where it was after the commit `c87e6ae4`
   * [ ] Removes all changes and history after commit `c87e6ae4`
   * [ ] Has no effect
2. To see which files have changed and what the exact changes are, do the following:
   * [ ] `git log`
   * [ ] `git log --numstat`
   * [x] `git log -p`
   * [ ] `git long --pretty=oneline`
3. &#x20;In the command git gc, what does gc stand for?
   * [ ] GNU cleanup
   * [x] garbage collection
   * [ ] generic concentration
   * [ ] garbage corruption
