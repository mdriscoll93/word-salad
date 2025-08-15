---
description: >-
  git lets you describe the differences between different branches and commits
  in a clear form.
icon: code-compare
---

# Diffs

## Overview

In this section, we will discuss how git lets you describe the differences between different branches and commits in a clear form. Depending on the options you give to the `git diff` command, you can print out the actual differences (in patch form), or list the files that have changed, and display other information. This ability is essential for clean development strategies.

### Objectives

* Use the standard `diff` command line utility and compare it with the built in `git` command
* Use `git diff` to compare the working copy to any specific branch or commit
* Compare file versions between branches and commits
* Know how to compare entire project trees, individual files, or directories

### Differencing Files

The common UNIX `diff` command is part of your standard toolbox. It shows the difference between any two files, or appiled recursively, two complete directory trees.

As a simple example suppose `file1` contains:

```bash
This is the
contents
of a simple
file.
```

and `file2` contains:

```bash
This is the
contents of a slightly
different
file.
```

Simply comparing the files gives us:

```bash
$ diff file1 file2
2,3c2,3
< contents
< of a simple
---
> contents of a slightly
> different
```

However, this is not the most useful form of output. One usually applies the `-u` option, to give them what is termed "unified output", which is used in patch commands:

```bash
$ diff -u file1 file2
--- file1
2024-10-31 15:48:15.933974603 -0600
+++ file2
2024-10-31 15:48:11.621507573 -0600
@@ -1,4 +1,4 @@
This is the
-contents
-of a simple
+contents of a slightly
+different
file.
```

Note the following:

* the `---` notes the first file and the `+++` denotes the second file.
* The `@@` line gives the line number context for both files.
* Lines that have been removed in going from `file1` to `file2` are denoted by `-` and lines that have been added are denoted by `+`
* The output also shows the context of the differences by showing the unmodified lines before and after the patch

When comparing two directory tree, the form used is often:

```bash
$ diff -Nur directory1 directory2
```

where the `-r` option forces a recursive descent into the trees, and the `-N` option forces files which have been added or deleted to appear in the differencing, instead of just generating a warning that a file is in only one directory tree.

### Diffing in Git

Differencing can be done with Git in a number of ways. The simple command:

```bash
$ git diff
```

shows the differences between the current working version of your project and the last commit.

The next form:

```bash
$ git diff earlier_commit
```

shows the differences between your current working version and the earlier commit specified by `earlier_commit`. This may often be specified as a branch name.

Another form:

```bash
$ git diff --cached earlier_commit
```

shows the differences between the saged changes in theindex and the commit. If you do not specify a commit, it defaults to `HEAD` for the current situation, and the output shows you how the next commit will differ from the current one. In Git versions 1.6.1 and on, you can say `--staged` instead of `--cached` which might be more intuitive.

The command:

```bash
$ git diff one_commit another_commit
```

shows the differences between two commits.

There are many other options that control either the nature of the differencing being done or the form of the output. For example, you can use `--ignore-all-space` to ignore white space differences, or `--stat` or `--numstat` to generate brief statistics.

It is also possible to limit the scope of the diff to a particular part of the directory tree. For example, if you are in the Linux Kernel Git repository, the command:

```bash
$ git diff v4.2.1 v4.2.2 Documentation/vm
```

will show only the changes in the `Documentation/vm` subdirectory.

Similarly, the command:

```bash
$ git diff --stat v4.2.1 v4.2.2 arch/x86_64
```

will show only brief statistics rather than detailed differences for changes to the `arch/x86_64` subdirectory tree.

### Review Lab 9.1 - Exploring Changes with `git diff`&#x20;

{% hint style="success" %}
You can work with one of your repositories created earlier, but it will be richer to work with a full repository such as theone for the Git project itself.
{% endhint %}

Working in the main project directory, first do:

```bash
$ git tag
```

to get a list of references. Then, to get a full difference between two versions, you can do something like:

```bash
$ git diff v1.7.0 v1.7.0-rc2
```

This is likely going to be a long output; try with the `--stat` flag to see a short summary of changes.

Now look at the changes to a particular directory, such as in:

```bash
$ git diff v1.7.0 v1.7.0-rc2 Documentation
```

or you can pick to look at one or more particular files.

### Knowledge Check 9.1

1. If there are two branches, br1 and br2, showing all differences between them can be done with:
   * [ ] git diff --all br1 br2
   * [x] git diff br1 br2
2. Showing which files have changed between two branches can be done with (Select all answers that apply):
   * [x] git diff --numstat br1 br2
   * [x] git diff --stat br1 br2
   * [ ] git diff --show br1 br2
   * [x] git diff --pretty br1 br2
3. Showing all differences between the current working tree and the last commit can be done with:
   * [x] git diff
   * [ ] git diff --clean
   * [ ] git diff --latest
   * [ ] git diff --working
