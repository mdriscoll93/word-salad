---
description: Chapter 5.
---

# Git Concepts and Architecture

## Learning Objectives

By the end of this chapter, you should be able to:

* Understand the basic concepts and design features that Git was designed for and employs
* Discuss how Git differs from earlier source control systems, particularly due to its design from the beginning to facilitate massively distributed development with many, even thousands, of contributors
* Understand how the Git repository contains the complete history of a project and can be rewound to any earlier state
* Explain how Git dispenses with a need for one centralized repository; all contributors have the full copy locally, and which repository is the authoritative one is a socio-political decision, not a technical one
* Explain the role of objects and index
* Explain how Git is based on content, and not path and file names
* Explain the difference between committing and publishing
* Describe the difference between upstream and downstream repositories, and explain the forking process

## Concepts

Many basic Git commands resemble those of other version control systems. Operations like adding files, committing changes, differencing with earlier versions, and logging the history are common to any serious source control system.

However, underneath the hood, Git is quite different than many of its antecedents. For example, in Git a file is not an essential object. Common operations which may be somewhat cumbersome in other systems, such as renaming a file, are remarkably simple when using Git.

The long hexadecimal numbers associated with commits are also somewhat strange. They are more than identifiers. They incorporate checksums that are computed from the contents of the repositories and the changes that are made.

## Design Features

Git grew out of the Linux kernel development community and was designed to meet its particular needs. As time goes on, it has been adopted by additional projects, but its basic structure and motivation shows its roots. Here we list the desired features, many of which could not be found in pre-existing version control systems, which inspired the development of Git.

* Facilitate distributed development\
  Developers had to be able to work in parallel without constant re-synchronization and without needing to constantly contact a central repository.
* Scale to handle large numbers of developers\
  The Linux kernel community has literally thousands of developers. This had to be handled both efficiently and reliably.
* Attain high speed and maximal efficiency\
  It is important to avoid copying unnecessary information, use compression, not bottle up networks, and be able to handle varying network latencies.
* Maintain strong integrity and trust\
  Security demands that no unauthorized alterations can be inserted, and that repositories are authentic, not impostors. Cryptographic hash functions are used for this purpose.
* Keep everyone accountable\
  All changes must be documented and ascribed to whomever did them. There is always a trail left behind that can be displayed.
* Keep immutable data in the repository\
  Information, such as the history of the project, cannot be changed.
* Make atomic transactions\
  When changes are made, they all must go through, or none do. This avoids leaving the repository in an uncertain or corrupted state.
* Support branching and merging\
  Git supports parallel branches of development, and has very robust methods for merging branches when it is time.
* Make each repository independent\
  Each repository has all the history in it. There is never a need to consult a central repository.
* Operate under a free unencumbered license\
  Git is covered by the GPL, Version 2.

## Repositories

The **repository** is a database. It contains every bit of information required to store a project, manage revisions, and display its history.

The Git repository contains not just the complete working copy of all the files that comprise the project's contents; it also contains a copy of the repository itself.

Each repository also contains a set of configuration parameters, such as the author's name and email address. For example, if we look at the file **.git/config** from the simple example in the preceding section we see:

```bash
$ cat .git/config

[core]
       repositoryformatversion = 0
       filemode = true
       bare = false
       logallrefupdates = true
[user]
       name = Another Genius
       email = a_genius@linux.com
```

If you clone a Git repository (make another copy of it for someone else or even yourself), this configuration information is not carried forth. Such information is instead maintained on a per-site, per-user, per-repository basis.

There are two important data structures that are maintained within a repository:

* The **object store** contains the guts of the project, a set of discrete binary objects.
* The **index** is a binary file that changes dynamically as the project morphs. It contains a picture of the overall project structure at any given time.

We will discuss these structures in some detail.

## Objects and Index

Git places four types of objects in its object store. All together, they contain everything known about the project and how to construct both its current state and its previous history.

* Blobs (Binary Large Object): This is an opaque construct that contains a version of a file's content. It does not contain the file's name or any other metadata, just the content.
* Trees: These record blob identifiers, pathnames, file metadata, etc., for files in a directory. They can also refer to subdirectories and objects within them.
* Commits: Every time a change is made to the repository, a commit object is created containing the metadata that fully describes the change. Each commit points to a tree object that gives a complete snapshot of the project. Commits have parents and children; except for the initial (or root) commit, which is parent-less.
* Tags: These assign human-friendly names to those horrendously long hexadecimal numbers used internally by Git.

The index contains a complete picture of the state of a project at a given time. It is temporary and dynamic. It changes as objects are added or modifications are made.

Changes such as adding, deleting, renaming or moving files are reflected in modifications to the index. These changes are maintained until you actually commit them in the repository.

The index plays a vital part in merging repository branches.\


## Content vs Pathnames

Unlike most other version control systems, Git tracks content, not files. It does not store information based on the file or directory names, or directory structure and file layout. This is associated information, not directly stored in the blobs.

For instance, suppose you have two files in two directories with different names. Git stores only one binary blob associated with the content. The content is associated with a unique hexadecimal string.

If either file is changed, its identifier changes, and now Git associates a new blob with the new content. Comparisons are very fast because Git only compares the 160-bit identifiers, rather than actually compare the blobs, much less the actual files.

As files change, new, uniquely identified binary blobs are created for each version. Other version control systems often keep a copy of a file at some point in time together with revision histories and have to play back (or forward) to reconstruct a given version of the file. Git can do all this more quickly because it always has knowledge of the complete content of any version of a file.

In other words, in Git's strange way of doing things, a file's history can be computed by tracking changes in content, or blobs, not files.

Filenames are treated as metadata distinct from file contents, and stored in tree objects and indexes. The contents of the **.git** subdirectory tree look nothing at all like the working directories they represent. This is totally different from the way older revision systems like RCS or CVS are set up.

Let's see what this means in practice.

Git commits tend to be all the changes for logical grouping (a feature, a bug fix, etc.) including changes in all necessary files.

For example, with Revision Control System (RCS) you might do:

```bash
$ rcs co src_slct.c
$ rcs co src_slct.dlg
$ rcs co src_slct.hlp
$ rcs co product_manual.doc
```

to check out the files to revise. Then you would edit them to get the bug fixed or feature implemented. Finally, you can check them back in with:

```bash
$ rcs ci srv_slct.c
$ rcs ci srv_slct.dlg
$ rcs ci srv_slct.hlp
$ rcs ci product_manual.doc
```

Tags must be used in RCS or other similar version control systems to group these changes together. If someone is in the middle of the check-in process and a system build begins, the build may fail. Backing out changes requires finding all the files with the same tag. If someone mis-tags one of the updates, backing out the changes is near impossible.

With Git, by comparison, you can edit the files to get the bug fixed or feature implemented, and then do:

```bash
$ git add srv_slct.c
$ git add srv_slct.dlg
$ git add srv_slct.hlp
$ git add product_manual.doc
$ git commit -s
```

The **git commit** step contains the new versions of all four files. Backing out the changes if a problem was discovered only involves removing the commit.

## Committing vs Publishing

The steps of _committing_ and _publishing_ are quite distinct in using Git, as compared with other revision control systems.

A commit is a local process; it merely means saving the current state of your working project files in your local repository, thus setting up a convenient marker in the development history. It is your decision whether you commit often with small changes, or infrequently with large change sets.

Publishing means sharing your changes by making them public. This can be done either by making a push or letting others pull, or by use of patches. This effectively freezes the repository history.

The commit operation requires no network access. You are free to reorganize your commits in your working repository before you make them public. You can pick the points to publish with care, so they are logical and well-defined.

## Upstream and Downstream

The parent repository is often considered upstream and the repository you are working on, at one point cloned from the parent, is often considered downstream.

Once again, there is nothing in Git itself which makes this distinction, akin to a server/client relationship, since Git has a fundamentally peer-to-peer architecture.

Conceptually, any repository to which you send changes is considered upstream; any repository which is based on yours is considered downstream.

There can be multiple levels involved if one has sub-trees or feeders. One repository can have both downstream and upstream relationships. For instance, it might be the focus of a particular sub-system and the entire project repository can be upstream, while individual sub-projects can be downstream.

## Forking

A project forks when someone takes the entire project and goes off in another direction. This is sometimes called branching. However, in Git this term has a different semantic meaning as there can be multiple branches within a given repository.

Technically, every time one clones a repository with Git, one creates a fork. But these are not meant to be permanent bifurcations.

There are a number of reasons a project may fork. There can be disputes among developers about the project direction or licensing issues, there can be political and personal conflicts such as the frequent case of an unresponsive lead maintainer.

What makes Git so useful is that its robust merging capabilities make healing a fork quite simple. Every time a branch is merged into the main repository, the fork is healed. Even forks which are quite old can often be merged back in with grace and ease.

### Review Lab

{% tabs %}
{% tab title="5.1 - question" %}
What are some features of Git that make it different from most, if not all, other revision control systems?
{% endtab %}

{% tab title="5.2 - answer" %}
The following list is of course incomplete and even debatable. Please think of additional aspects.

1. Git is not based on files as basic units. Instead, the primary quantities stored are binary blobs. If the same file appears in more than one place in the project, only one blob is needed to store the contents.
2. Most (but not all) revision control systems have a central repository that is authoritative. With Git, all repositories, no matter their location or ownership, are essentially the same. Leadership and authority is by social contract more than anything else.
3. Git was designed for distributed development from its outset, rather than evolved to work with it. Thus it has always emphasized minimizing transfer sizes and speeds and was built completely on the Internet.
4. Dealing with multiple branches and merging, rebasing and forking have always been organic features.
5. Committing (checking in changes) and publishing (making them widely available) are quite distinct steps by design.
6. Other features, such as the use of bisection to locate and fix problems, are rather innovative and unique.
{% endtab %}
{% endtabs %}

### Knowledge Check

1. In Git, the fundamental content-full object that is stored, is called a:
   * [ ] file
   * [x] blob
   * [ ] directory
   * [ ] deposit
2. Upstream and downstream Git repositories are:
   * [x] Structurally the same; it is a socio-political decision which repositories are upstream or downstream
   * [ ] Fundamentally different; it is structurally impossible to bring changes from the downstream repository to the upstream one
3. A git commit:
   * [x] Is a local process and does not make your changes visible to remote collaborators
   * [ ] Makes your changes visible to all collaborators both local and remote
4. Publishing changes with a git push:
   * [ ] Is a local process and does not make your changes visible to remote collaborators
   * [x] Makes your changes visible to all collaborators both local and remote



