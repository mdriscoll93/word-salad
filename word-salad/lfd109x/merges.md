---
icon: code-merge
description: >-
  Merging is Git's way of putting a forked history back together again. merge
  lets you take the independent lines of development created by git branch and
  integrate them into a single branch
---

# Merges

## Learning Objectives

* Understand what it means to merge one developer's work into a specific branch of a repository
* Explain how merging is the inverse of branching
* Understand the process of resolving conflicts when merging
* Know the basic merge commands
* Know what rebasing does
* Explain the important difference between merging and rebasing

## Overview

All but the most primitive revision control systems have to deal with merging. This happens when more than one developer has been working on the project simultaneously and needs to merge their changes into a unified code base.

If the changes do not conflict with each other directly, i.e., they work on different files or various parts of the same files, merging the work is not essentially difficult.

For example, if there are two change sets, you could apply one and then the other; _the order should not matter_. The only technical refinement is that after one set of changes, _there may be a shift of lines where the second set of changes has to work due to insertions or deletions made by the first one_. Any sensible revision control system or patch manager can handle such simple offsets.

Even in this simple case of non-overlapping change sets, more subtle problems can arise that only humans are likely to notice. Complications can easily be introduced, for example, if two distinct change sets both try to fix the same bug but do it in different places with different methods.

In such cases, the **Git methodology** of having many **small patch sets** and **commits**, combined with tools like **bisection**, can help resolve such subtle problems quickly.

In the case of conflicting change sets, Git has been designed from the outset to have strong tools for conflict resolution. This session will focus on how to use such tools.

<mark style="color:blue;">**Merging can be seen as the inverse process of branching**</mark>. We have already discussed how Git handles opening up a new branch. Without an efficient automated process for rejoining, we would have a much weaker revision control system.

### Merge Commands (1)

Merging the `devel` branch into the current `main` branch is as simple as the following:

```bash
$ git checkout main
$ git merge devel
```

The first step is only necessary if you are not already working on the `main` branch.

It is best practice to "tidy up" before merging. This means committing any changes  that have been staged, getting rid of junk, etc. All of this will help minimize later confusion. You can always check your current state in this regard with the `git status` command. The following is a brief overview of a simple preparation prior to merging:

```bash
$ git status
$ git checkout main
$ git fetch
$ git pull
```

* Ensure that `HEAD` is pointing to the correct merge-receiving branch using `git status`.
* If needed, execute `git checkout` to switch to the receiving branch
* Ensure the receiving branch and the merging branch are up-to-date with the latest remote changes using the `git fetch` command - this pulls the latest remote commits
* Once the fetch has completed, ensure `main` has the latest updates by executing `git pull`

If you had no conflicts the preceding `merge` command will have given a happy report, and your main branch now includes everything that was in the devel branch; it has been synced up with the ongoing development. However, let's see what happens if the merge senses a conflict.

To examine this, we create `main` and `devel` branches that are identical except that in the `main` branch we create **`file1`** which has one line, saying **`file901`**. In the **devel** branch, this file has the line **`file701`**. Following the above merge procedure we get the result:

```bash
$ git merge devel

Auto-merged file1
CONFLICT (content): Merge conflict in file1
Automatic merge failed; fix conflicts and then commit the result.
```

Listing the files in the working branch can be done via:

```bash
$ git ls-files

file1
file1
file1
file2
file3
```

We see the odd result of **`file1`** being listed three times. If we look at the contents of this file after the attempted merge, we see the curious result:

```bash
$ cat file1

<<<<<<< HEAD:file1
file901
=======
file701
>>>>>>> devel:file1
```

Above is a three-way `diff` showing the problems.

Note that all other changes resulting from the merge have gone through successfully. The `merge` command will tell you specifically about each problem.

There are two basic approaches you can take to fixing these problems:

1. Revert the merge with `git reset`, work on the conflicts in either of the two branches until no conflict is expected to result from a merge, and try again:
   1. If you have made a mess of things with a premature or accidental merge, it is easy to revert back to where you were with:

```bash
$ git reset --hard main
```

2. Work on the attempted merge file - the third copy that has all the funny markers in it. You can edit however you like to reflect what should be the product of the merge, and then simply commit via:

```bash
$ git add file1
$ git commit -s -m "A message for the merge"
$ git ls-files

file1
file2
file3
```

Note the odd appearance of three versions of **`file1`** are gone and the merge is now complete. The approach you take is a judgment call that depends on the size of the merge set, the number of conflicts that develop, etc., and it is totally up to you. The end result should be the same.

### Rebasing (1)

Suppose you establish a development branch at some point in time and the main branch that you began from continues to evolve at the same time you are making changes to your development branch.

Eventually, you intend to merge your work with the parallel development branches, but suppose your work is not quite ready for prime time, but you want to get the benefit of the other working going on in the parallel branch.

There are two basic methods that may seem similar, but are actually quite different: merging and rebasing

Merging simply means bringing in the other (probably mainline) evolving repository from time to time. Any conflicts will need to be resolved as usual.

Rebasing is quite different. When you follow this procedure, all your changes since your initial branch off the other line of development are reverted, the branch is brought up to date to its present state, and then your changes are reworked so they fit on the current state.

To give a concrete example - suppose you are working on a new feature for the Linux Kernel and you began working when the kernel release status was version 4.16. While you were working, the mainline kernel continued to evolve as well, and version 4.17 is released.

* If you rebase, your changes and your patch set are rewritten to apply to the 4.17 code rather than the 4.16 code - a hopefully simpler situation to deal with in the future. In fact, you may rebase further as time goes on.

The steps to a rebase are straightforward. Suppose in the past you executed the following:

```bash
$ git checkout -b devel origin
```

where `origin` is a remote tracking branch as we will discuss later (you could also use a local branch of course, but that is less realistic). Then you go about and make a series of changes to project files and make commits in your `devel` branch. Meanwhile, the `origin` branch continues to evolve.

The rebase operation begins like so:

```bash
$ git checkout devel
$ git rebase main devel
```

When you do this, each commit done since the original branch point is removed, but is saved in `.git/rebase-apply`. The `devel` branch is then moved forward to the latest version of `origin`, and then each of the changes is applied to the updated branch.

Obviously, conflicts may emerge and if any are found you will be asked to resolve them as you did when you were merging. As you fix the conflicts, you will have to do `git add` to update the index, and when you are done fixing conflicts, instead of doing a commit you do the following:

```bash
$ git rebase --continue
```

If things get disastrous somewhere along, you can revert back to where you were before the attempted rebase with:

```bash
$ git rebase --abort
```

There are several potential problems associated with rebasing, some of which arise from its [Orwellian nature](#user-content-fn-1)[^1]. When you perform a rebase, you change the history of commits, because the changes are temporarily removed and then all put back in.&#x20;

Rebasing Issues:

* Presumably you have been testing your work as you have been progressing it and the code branch you were testing against came from the earlier branch point. There is no guarantee that just because things worked before when you tested they still will. Indeed, problems that arise may be very subtle.
* You may have done your work in a series of small commits, which is a good practice when trying to locate where problems may have been introduced, for instance when using bisection. But now, you have collapsed matters into a larger commit.
* If anyone else has been using your work, for instance, has been pulling changes from your tree, you have just pulled the rug out from under their feet. In this case, many developers consider rebasing to be a terrible sin.

If you are doing merging rather than rebasing other problems can arise, especially if you do it often or not at major stable development points. Once again, history can be confusing in your project. Whatever strategy you choose, think things through and only do merging or rebasing at good, well-defined stages of development to minimize problems.

<table data-full-width="true"><thead><tr><th>Command</th><th>Source Files</th><th>Index</th><th>Commit Chain</th><th>References</th></tr></thead><tbody><tr><td><code>git rebase</code></td><td>Unchanged</td><td>Unchanged</td><td>Parent branch commit moved to a different commit</td><td>Unchanged</td></tr></tbody></table>



[^1]: The description of git rebasing as having an "Orwellian nature" is a metaphorical way of expressing how rebasing can alter the history of a Git repository. This reference to George Orwell's works, particularly "1984," highlights the concept of changing past events—akin to how history is manipulated in the novel.
