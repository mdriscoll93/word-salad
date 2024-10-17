---
description: Chapter 6.
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
    visible: true
---

# Managing Files and the Index

## Chapter Overview

Git distinguishes between three types of files: tracked, ignored, and untracked, with only the tracked ones residing in the repository. The most basic file operations are:

* Adding files
* Removing files
* Moving (and renaming) files
* Listing files

In the next section, we will discuss making commits, which is really not just a file-based operation.

## Learning Objectives

By the end of this chapter, you should be able to:

* Enumerate the categories files fit into: tracked, ignored, and untracked
* Define file categories and explain how they differ from each other
* Understand the use of **.gitignore** files
* Grasp the use of basic git file oriented commands including: **add**, **rm**, **mv**, and **ls-files**
* Get some practice with these commands and other essential ones

## File Categories

As far as Git is concerned, files in your project directories fall into three categories:

* Tracked files: They are already in the repository in their current working state, or have been staged, i.e. changed but not yet committed.
* Ignored files:  They are invisible to Git. Each directory may contain a file named **.gitignore** which lists either specific files or name patterns which are to be ignored. For instance, many temporary files could be ignored with a specification like **\*.o**. The **.gitignore** file in any directory applies recursively to all subdirectories below it, so if you specify a filename or pattern in the main directory, all files fitting the description will be invisible. It is also possible to override the rules by prefixing with **!**. For example, if your **gitignore** file includes:

{% code fullWidth="false" %}
```bash
*.ko
!my_driver.ko
```
{% endcode %}

{% hint style="info" %}
The second specification overrides the first, more general one, and the file `my_driver.ko` will be tracked.
{% endhint %}

* Untracked files: They are anything that does not fit in the previous two classifications. All files in your current working directories are examined and anything that is not tracked or ignored is considered untracked. These may be files that have not been added yet, they may be temporary files, etc. But if you commit the entire directory, they will become tracked files.

## Basic File Commands

```bash
git add
```

Adds one or more files as blobs to the object store and adds references to the new blobs in the index, by sha1 hash. Thus, we are staging one or more files and directories. It can be used either to add new files or stage changed ones. The files will not be committed until there is a **git commit** done. There are a number of options (see **git help add**). For instance, you can use **-i** for interactive choosing of files to stage, or **-u** to update only files already known to Git. You can also specify wildcard patterns or directory names (in which the entire subdirectory tree is added). It is worth repeating that until you do a **git commit** the changes are only staged; the repository is not updated.

***

```bash
git rm
```

Removes a file from the working tree and the index, but does not remove it from the repository; that only happens when you make a new commit. This can be pretty dangerous if you do not realize what you are doing. If you only want to remove the file from the working directory, and not the repository index, just use the normal **rm** command. While you remove files from the repository, you do not remove them from the history, as that would be dishonest. If you want to remove a file that has been staged but not committed, you have to add the `--cached` option as in:

```bash
$ git add myfile
$ git rm myfile --cached
```

***

```bash
git mv
```

Renames a file and stages the new filename in the repository. It is equivalent to renaming the working file and then doing a **git rm** on the old file name and a **git add** on the new one, i.e. the following operations are equivalent (please take a look at the commands below). This is an easier operation in git than it is in older revision control systems where renaming a file means actually deleting the old one and adding a new one. In this case, the binary blobs associated with the file remain the same, only the index is updated.

```bash
$ git mv oldfile newfile
$ mv oldfile newfile ; git rm oldfile ; git add newfile
```

***

Below is a table that shows how all three stages work.

<table data-full-width="true"><thead><tr><th>Command</th><th>Source Files</th><th>Index</th><th>Commit Chain</th><th>References</th></tr></thead><tbody><tr><td><code>git add</code></td><td>Unchanged</td><td>Updated with new file</td><td>Unchanged</td><td>Unchanged</td></tr><tr><td><code>git rm</code></td><td>File removed</td><td>File removed</td><td>Unchanged</td><td>Unchanged</td></tr><tr><td><code>git mv</code></td><td>File moved/renamed</td><td>Updates file name/location</td><td>Unchanged</td><td>Unchanged</td></tr></tbody></table>

***

```bash
git ls-files
```

Shows information about files in the index and working tree. By default, this command shows only files in the repository. If you want to show the untracked files, follow the command below where the `--others` option shows the untracked files, and the `--exclude-standard` option says to ignore standard exclusions such as **`.gitignore`** files.

```bash
$ git ls-files --others --exclude-standard
```

### Review Lab

#### Getting Practice with Basic file commands

{% tabs %}
{% tab title="6.1 - question" %}


1. First, initialize the repository, configuring it with name and email, etc. Then add a couple of files to the project and commit them.
2. Remove one of the files with the **git rm** command, and use the **git diff** command to see the difference with the repository.
3. Rename the remaining file with the **git mv** command and use the **git diff** command to see the difference with the repository, once again.
4. Commit again and look at the history using the **git log** command. Then do **git ls-files** without any arguments.
5. Add two new files, make one of them ignored by Git and modify the original remaining file. Do **git ls-files** again.
6. Now try some different options to **git ls-files**, such as **-t** and **-o**. Do **man git ls-files** to see the various options available and try some others.
7. Next, add the new files that are not ignored with the **git add** command, commit once again, and do **git ls-files** with some options to see the results. You may want to do **git log** again as well.
{% endtab %}

{% tab title="6.1 - answer" %}
<pre class="language-bash"><code class="lang-bash"><strong># 1.
</strong><strong># Initialize the repository and put our name and email in the .config file.
</strong>
echo -e "\n\n************* CREATING THE REPOSITORY AND CONFIGURING IT\n\n"

rm -rf git-test ; mkdir git-test ; cd git-test
git init
git config user.name "A Smart Guy"
git config user.email "asmartguy@linux.com"

echo -e "\n\n********* CREATING A COUPLE OF FILES AND ADDING THEM TO THE PROJECT AND COMMITTING\n\n"

# Create a couple of files and add them to the project, and then commit.

echo file1 > file1
echo file2 > file2
git add file1 file2
git commit . -s -m "This is our first commit"
</code></pre>

```bash
# 2.
# Remove one of the files and then see the difference with the repository.

echo -e "\n\n************* REMOVING ONE OF THE FILES AND THEN DIFFING\n\n"

git rm file2
git diff
```

```bash
# 3.
echo -e "\n\n************* RENAMING ONE OF THE FILES AND THEN DIFFING\n\n"

# Now rename a file and diff again.

git mv file1 file1_renamed
git diff

echo -e "\n\n************* RECOMITTING\n\n"
```

```bash
# 4.
# Now get it all in with another commit.

git commit . -s -m "This is the second commit"

echo -e "\n\n************* LOOKING AT THE HISTORY OF THE PROJECT\n\n"

# Look at the history.

git log

echo -e "\n\n************* DO git ls-files"

# Do git ls-files.

git ls-files
```

```bash
# 5.
echo -e "\n\n************* ADD TWO NEW FILES, MAKE ONE IGNORED, AND MODIFY A FILE\n\n"

echo extra1 >> extra1
echo extra2 >> extra2
echo anotherline >> file2
echo extra1 >> .gitignore

echo -e "\n\n************* DO git ls-files WITH NO ARGS\n\n"

git ls-files
```

```bash
# 6.
echo -e "\n\n************* DO git ls-files WITH -t \n\n"

git ls-files -t

echo -e "\n\n************* DO git ls-files WITH -t --others\n\n"

git ls-files -t -o
```

```bash
# 7.
echo -e "\n\n************* RECOMMIT AND CHECK AGAIN\n\n"

git add extra2
git commit -a -s -m "third commit"

git ls-files -t -c -o -s

git log
```

***

You can download a script with the above steps from `s_06/lab_gitbasics.sh` in your **SOLUTIONS** file. If you have not yet downloaded the **SOLUTIONS** file, please see the _Lab Exercises - Files to Download_ page in the _Welcome_ chapter for instructions.
{% endtab %}
{% endtabs %}

### Review Questions

1. An "ignored" file in Git is one that:
   * [ ] Has never been added to the repository
   * [x] Is enumerated in a **.gitignore** file
   * [ ] Exists in the working tree, but has never been added to the project
   * [ ] Does not belong to the project owner
2. The command `git rm some_file`:
   * [x] Removes `some_file` from the repository and from the working file tree
   * [ ] Removes `some_file` from the repository, but not from the working file tree
   * [ ] Removes `some_file` from working file tree, but not from the repository
3. To list all files in the repository, issue the command:
   * [ ] `git ls`
   * [x] `git ls-files`
   * [ ] `git log --numstat`
   * [ ] `git dump-list`
