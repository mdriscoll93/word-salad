---
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: false
  pagination:
    visible: false
---

# Git and Revision Control Systems

## Chapter 3.

### Converting between different systems:

It is possible to bring projects which use other revision control systems into the Git universe.

While it is always possible to simply take the working copy of the project files, ignore the revision control information, and create a new repository, this throws away the previous history of changes, and the possibility of reverting to an earlier state.

There are tools for importing projects into Git. Indeed, we will do exercises showing how to do this for CVS and Subversion.

It is also possible to go in the opposite direction, to export a Git project to another system.

You might do this because you want to switch your revision system to one of the other choices. More likely, you would do this because you need to mirror your Git repository, so those who have access only to one of the older systems can synchronize with it. We will not give any details on this process.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption><p><strong>Different Systems: RCS > CVS > SVN</strong></p></figcaption></figure>

### Revision Control Systems (RCS)

&#x20;An old widely used source code system is provided by Revision Control Systems. It's easy to convert projects using the earlier Source Code Control System (SCCS) to using RCS

`make` has built-in rules for RCS which makes it easy to use in dev projects. Emacs can be used tightly with RCS using vc  mode and related functions (vc-ediff)

Conventionally, a sub-directory named **RCS** (or **rcs**) is situated under the directory containing the files under control. This is the repository.

RCS forces developers to work in a serial fashion:

* When a file is unlocked, no one can commit changes to a file, but the lock is available to anyone with access to the RCS repository
* When a file is locked, only the holder of the lock can commit changes to the file

As we shall see, more modern systems permit better cooperative development.

### Concurrent Versions System (CVS)

The **Concurrent Versions System** (CVS) is more powerful than RCS and does a more thorough job of letting multiple developers work on the same source concurrently. However, it has a steeper learning curve, as it has many more commands and possible environment customizations.

All files are stored in a centralized repository. One never works directly with the files in the repository. Instead, you get your own copies into your working directory, and when you are finished with changes, you check (or commit) them back into the repository.

Repositories can be on the local machine or on a remote platform anywhere in the world. The repository location may be specified either by setting the **CVSROOT** environment variable, or with the **-d** option; for example, the two following lines are equivalent:

```bash
$ cvs -d /home/coop/mycvs init
$ export CVSROOT=/home/coop/mycvs ; cvs init
```

A number of users can make changes to a given module at once. There are a number of commands to help with resolving differences and merging work.

As for RCS, emacs can be used tightly with CVS using the **vc** mode and related functions.

### Subversion

**Subversion** is an open source project designed to be the successor to CVS, which has gradually replaced CVS in many projects.

In order to keep the transition smooth, the Subversion interface resembled that of its predecessor, and made it easy to migrate CVS repositories to Subversion.

Directories, copies, and renames are versioned, not just files and their contents. Metadata associated with files (such as permissions) can also be versioned.

Revision numbering is in a per-commit basis, not per-file, and atomicity of commits is complete; unless the entire commit is completed, no part of it goes through.

A standalone Subversion server can be set up using a custom protocol, which runs as an **inetd** service or as a daemon, offering authentication and authorization. One can also use Apache to set up an http-based server.

There are many enhancements that improve efficiency and reduce storage size requirements. To be more specific, costs are proportional to the size of the change set, not that of the data set.

In particular, binary files can be handled, and there are built-in tools for the mirroring of repositories.

### Git

The Linux kernel development system has special needs in that it is widely distributed throughout the world  with hundreds and thousands od devs. Furthermore it is all done publicly under GPL

For a long time, there was no real source revision control system. Then, major kernel developers went over to the use of [BitKeeper](https://www.bitkeeper.org/) which, as a commercial project, granted a restricted use license for Linux kernel development.

However, in a very public dispute over licensing restrictions in the spring of 2005, the free use of BitKeeper became unavailable for Linux kernel development. The response was the development of Git, whose original author was Linus Torvalds.​

Technically, Git is not a source control management system in the usual sense, and the basic units it works with are not files. It has two important data structures: an object database and a directory cache.

The object database contains objects of three varieties.

* Blobs: Chunks of binary data containing file contents.
* Trees: Sets of blobs including file names and attributes, giving the directory structure.
* Commits: Changesets describing tree snapshots.

The directory cache captures the state of the directory tree.

By moving away from a file by file-based system, one is better able to handle changesets which involve many files.

### Git and Distributed Development&#x20;

Many projects have developers working separately, united by a common version control system. This is possible even when there is a central authoritative repository, such as with CVS.

What makes Git different is that on a technical level, there is no such thing as a central authoritative repository. The underlying framework is actually peer-to-peer in nature. The importance and central role played by one particular location is political and sociological, not technical.

Distributed development is not defined by various parts of a project being worked on separately, with a partition between different host repositories. In Git, distributed development means every repository is authoritative, and contains not just one part of the project, but the entire code base.

Git has no politics and has no preferred model for organization. The hierarchy of the development community can be very centralized and top down, or it can be very flat. The exchange of information and changes can be pyramidal, with a number of development trees coalescing into a top level one, or it can be very egalitarian.

Git is a tool, not a rigid method, and it can be used in any way that fits the needs and philosophy of a project.

#### Lab 3.1. Converting a Subversion Repository to Git

Make sure you have the appropriate subversion software installed to do the following steps. In addition to the basic subversion package, you need subversion-perl. There may be other useful packages which are obtainable from the EPEL repository on RHEL-based systems.

The easiest way to get a copy, or clone, of a Subversion project is to use **git svn**. To obtain a copy of part of the Subversion repository itself, you can do:

```bash
git svn clone https://svn.apache.org/repos/asf/subversion/trunk/doc my_svn_repo
```

where we have chosen only the **doc** module of Subversion in order to keep things small.

However, this can take a long time as it gathers the entire history of the project and sometimes hangs. It is easier for learning purposes to just get the most recent version (which for Git we would call a _shallow clone_). There is no easy way to just say give me the last version with Subversion; we need an actual release number, which we can get with:

```bash
svn log https://svn.apache.org/repos/asf/subversion/trunk/doc | head
------------------------------------------------------------------------
r1663949 | brane | 2015-03-04 05:55:13 -0600 (Wed, 04 Mar 2015) | 1 line
* doc/svn-square.jpg: Copy the logo used by Doxygen from the site tree.
------------------------------------------------------------------------
r1663948 | brane | 2015-03-04 05:53:09 -0600 (Wed, 04 Mar 2015) | 1 line
...
```

We can then pick just the latest release for the shallow clone:

```bash
git svn clone -r1663949 https://svn.apache.org/repos/asf/subversion/trunk/doc my_svn_repo
Initialized empty Git repository in /tmp/SVN/my_svn_repo/.git/
        A       doxygen.conf
        A       svn-square.jpg
        A       programmer/gtest-guide.txt
        A       programmer/WritingChangeLogs.txt
        A       README
        A       user/svn-best-practices.html
        A       user/lj_article.txt
        A       user/cvs-crossover-guide.html
r1663949 = f2f1312fe65123c1be0935421d05cc862d0d008e (refs/remotes/git-svn)
Checked out HEAD:
  https://svn.apache.org/repos/asf/subversion/trunk/doc r1663949
```

This creates a Git repository under the **my\_svn\_repo** directory, which you can examine. Notice all the Git information goes in the **.git** subdirectory.

If you want to compare with original Subversion repository, you can bring that down with:

```bash
svn checkout https://svn.apache.org/repos/asf/subversion/trunk/doc doc
....
```

where the repository information will go under **.svn** directories.

Compare:

```bash
$ diff -qr my_svn_repo/ doc/
Only in my_svn_repo/: .git
Only in doc/: .svn
```

