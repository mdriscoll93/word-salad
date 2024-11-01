---
description: Chapter 8.
icon: code-branch
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

# Branches

## Learning Objectives

By the end of this chapter, you should be able to:

* Explain what a branch is and how to create and switch between them.
* Know the difference between a branch name and a tag
* Know how to list all available branches and control the level of detail provided
* Know how to checkout a branch
* Obtain earlier versions of files

## What is a Branch

At some point, moving off the main line of development may become necessary to create an independent branch. For example, you may want to establish maintenance and development branches when a major product release comes out.

The maintenance release branch may be restricted to fixing bugs, severe performance bottlenecks, and plugging security holes. In addition to including these changes, the development branch would incorporate new features and optimizations not intended for public release until the next major product version is ready.

Another reason to maintain a separate branch is to isolate a particular development area or work on a severe bug without being burdened by the noise introduced by other simultaneous changes.

What makes Git so useful is that while the branching process is easy, the inverse merging process is also easy, and the whole infrastructure has been structured with this in mind. We will discuss merging in detail later.

This was inspired by the way the Linux kernel development community evolved. Many parallel branches exist for things like network drivers, networking, and USB devices. In fact, in the Git way of doing things, the branch maintained by Linux Torvalds, while it is the collection point for changes and is, in some sense, the most important branch, is no different from any other branch in a technical sense. Its importance is social, political, and tactical, but not structural.

One branch can track another branch while proceeding independently. That means it can be updated with changes in the other branch without starting over. In other words, the fork point can be advanced to the future of the original diversion. Thus, the differences between the parallel branches can be kept as small as possible even as they both advance.

While you can have many branches in a project as part of the same repository, you can only have one active (i.e., current) at a time. The files in your working directories are those from that branch. If you switch branches, the files will change.

## Branch Names vs Tags

Sometimes, people get confused about the difference between branch names and tags. It is possible to use the exact string for both, as they operate in different namespaces, but it requires some care and is not recommended for the non-expert.

A **branch name** represents a line of development. Usually, the main line of development is known as the main branch, as it is called by default. As time goes on, other branches will be born, given names, and develop in parallel. They might have names like `devel`, `debug`, or `stable`.

While the branch's contents will change as time passes and new commits are made, its name will not usually change except when another branch is embarked upon.

***

**Tags**, on the other hand, represent a stage of a particular branch at one point in its history. Unless you are asking for a lot of pain, you would never change the name of a tag in the future. They should be immutable.

be consideredIf two branches share a common ancestor, they will share tags that predate the separation. If two parallel branches adopt the same name for a tag after they separate, the merging process will have to deal with this. But remember, the tag can just be thought of as a nickname for one of those long hexadecimal strings, which are fundamental commit identifiers. When we discuss merging, we will consider this situation.

## Branch Creation

The basic command for creating a new branch is:

```bash
$ git branch [branch_name] [starting_point]
```

If you do not give any arguments, you get a list of branches with the active one starred. A very detailed history of the branches can be obtained with:

```bash
$ git show-branch
```

If you are creating a new branch, you must give it a name. Some rules apply, such as no blank spaces, no special or control characters, no slashes at the end, etc. Keep it simple.

A branch is like a tag, but you can add commits.​

<table data-full-width="true"><thead><tr><th>Command</th><th>Source Files</th><th>Index</th><th>Commit Chain</th><th>References</th></tr></thead><tbody><tr><td><code>git branch</code></td><td>Unchanged</td><td>Unchanged</td><td>Unchanged</td><td>A new branch is created in <code>git/refs/heads</code>. <code>HEAD</code> for the new branch points to <code>HEAD</code> of the current branch. The current branch is set to the new branch.</td></tr></tbody></table>

The starting point is any commit, if there is a tag that describes it, you can use that instead of the long string. If you do not give the argument, you create a copy of the active branch as of its last commit. So, you might do:

```bash
$ git branch devel
```

to create a new development branch off the mainline.

You can delete the `devel` branch with:

```bash
$ git branch -d devel
```

which cannot be your current working branch.

Recovering an accidentally deleted branch is difficult, although not always impossible, so use care.

## Branch Checkout

The checkout process lets you switch branches. If you do:

```bash
$ git checkout devel
```

You have now switched to the development branch in the preceding example, and the contents of any files that have been changed will reflect this. `HEAD` It is set to the top commit of the branch.

Note that you have not lost the old branch. All the information to go back to it is still in the repository. All you would have to do is:

```bash
$ git checkout main
```

<table data-full-width="true"><thead><tr><th>Command</th><th>Source Files</th><th>Index</th><th>Commit Chain</th><th>References</th></tr></thead><tbody><tr><td><code>git checkout</code></td><td>Modified to match the commit tree specified by branch or commit ID; untracked files not deleted.</td><td>Unchanged</td><td>Unchanged</td><td>Current branch reset to that checked out. <code>HEAD</code> (in <code>.git/HEAD</code>) now refers to last commit in branch.</td></tr></tbody></table>

Switching branches would be a bad move if you have made changes to your working directory that have not yet been committed. So, Git will refuse to do it and spit out an error message.

Suppose you do:

```bash
$ git branch devel
$ echo hello > hello
$ git add hello
$ git commit -a -s
$ git checkout devel
```

you will see the file `hello` does not exist in the **`devel`** branch.

It is also possible to combine the operations of creating a new branch and checking it out by using the `-b` option to the checkout operation.

Doing:

```bash
$ git checkout -b newbranch startpoint
```

Is entirely equivalent to:

```bash
$ git branch newbranch startpoint
$ git checkout newbranch
```

## Getting Earlier File Versions

Suppose you want to see an earlier version of a particular file. You can do this  `git show` if you specify a path name in addition to a tag (note the colon):

```bash
$ git show v2.4.1:src/myfile.c
```

If you want to restore that version, you can do it with:

```bash
$ git checkout v2.4.1 src/myfile.c
```

where there is no colon.

### Review Lab

{% tabs %}
{% tab title="8.1 - question" %}
1. First, initialize the repository and configure it with name, email, etc. Then, add a couple of files to the project and commit them.
2. Please create a new development branch and then check it out.
3. Modify a file, add another one, and then make a new commit. List the files that are present and also do `git ls-files`.
4. Now check out the original main branch. Once again, list the files that are present and also do `git ls-files`.
{% endtab %}

{% tab title="8.1 - answer" %}
```bash
# 1.
# Initialize the repository and put our name and email in the .config file

echo -e "\n\n******** CREATING THE REPOSITORY AND CONFIGURING IT\n\n"

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
```

```bash
# 2.
# Create a new development branch.

echo -e "\n\n**************** CREATING A NEW BRANCH, devel ***\n\n" git branch devel

# Check out the new branch.

echo -e "\n\n*************** CHECKOUT BRANCH devel ****\n\n" git checkout devel</strong
```

```bash
# 3.
# Modify a file and add a new one.

echo another line >> file1
echo file3 >> file3

git add file1 file3

echo -e "\n\n************* DIFFING\n\n"
git diff

echo -e "\n\n************* RECOMITTING\n\n"

# Now get it all in with another commit.

git commit . -s -m "This is a commit in the devel branch"

# List files.

echo -e "\n\nlist files, then git ls-files\n\n"

ls -l
git ls-files
```

```bash
# 4.
# Now check out the original branch.

echo -e "\n\n************ LIST BRANCHES AND CHECKOUT main **\n\n"

git branch
git checkout main

# List files.

echo -e "\n\n***************** list files, then git ls-files\n\n"

ls -l
git ls-files

echo -e "\n\n***************** SHOW THE BRANCH\n\n"

git branch
```
{% endtab %}
{% endtabs %}

You can download a script with the above steps from `s_08/lab_branch.sh` in your `SOLUTIONS` file. If you have not yet downloaded the `SOLUTIONS` file, please see the _Lab Exercises - Files to Download_ page in the _Welcome_ chapter for instructions.

### Knowledge Check

1. In Git, branching is the inverse process to:
   * [ ] Forking
   * [x] Merging
   * [ ] Pushing
   * [ ] Committing
2. A detailed branching history can be shown by:
   * [ ] `git branch --show`
   * [ ] `git branch show`
   * [x] `git show-branch`
   * [ ] `git show branch`
3. &#x20;
