---
icon: github
---

# Managing Local and Remote Repositories

## Objectives

* Understand the role of Git in facilitating distributed development
* Clone a repository
* Pull (and fetch) changes from a repository​
* Push changes to a repository
* Understand how to run Git daemon to make your repository available online.

### Working with Others

Many other revision control systems are built on the notion of a central, authoritative repository, the hub that individual developers work with. An essential difference in Git's architecture is that no one repository retains such a central role, at least not structurally.

Maintaining separate repositories makes sense in at least three situations:

1. A developer is working autonomously.
2. Developers are separated across an extensive (even global) network. A local group of developers may share their repository to collect changes of immediate import and interest.
3. Divergent projects or sub-areas are worth developing deeply on their own, with a view to eventually merging changes that benefit the main project.

Because Git has such a strong infrastructure dedicated to branching and merging multiple repositories, it can handle the complicated logistics of distributed development relatively easily.

Some essential operations involved in handling remote repositories include:

* **Cloning**: Establish an initial copy of a remote repository and place it in your object database. The essential command is `git clone`.
* **Pulling**: Bring home changes from the remote repository, keeping your tracking branch current. The essential commands are `git pull` and `git fetch`.
* **Pushing**: Submit your changes to the remote repository. The essential command is `git push`.
* **Publishing**: Make your repository available for others to clone, pull from, and perhaps push to.

Git uses tracking branches to handle content in remote repositories. These local branches serve as proxies or references for specific branches in remote repositories.

There is a special kind of repository called a [**bare repository**](https://git-scm.com/book/en/v2/Git-on-the-Server-Getting-Git-on-a-Server). Usually, you work in a development repository. A bare repository has no working directories, is not used for development, and has no checked-out branches. It is used only as an authoritative place to clone and fetch from, and push to. It is created with the `--bare` option to the `clone` command.

### Cloning (1)

Getting an initial clone of a remote repository is as simple as doing:

```bash
$ git clone git://git.kernel.org/pub/scm/git/git.git
```

which brings down the entire Git repository for Git itself. It puts it in a directory called `git` which contains the usual `.git` subdirectory with all the objects, indexes, etc., that are part of the repository.

This can be a large download, but it is a one-time operation.

Note the use of the `git://` protocol in the remote specification. It is the preferred method but not the only one that can be used. Other possibilities are:

* `file:///path/to/repo.git`
* `ssh://user@remotesite.org[:port]/path/to/repo.git`
* `user@remotesite.org:/path/to/repo.git`
* `ht‌tp://remotesite.org/path/to/repo.git`
* `ht‌tps://remotesite.org/path/to/repo.git`
* `rsync://remotesite.org/path/to/repo.git`

The `git://` method is the fastest and cleanest and should be used whenever supported.

If you create a clone of a local repository, Git will use hard links where possible to save disk space. If you want or need to prevent this, use the `--no-hardlinks` option to `git clone`.

You can see the references within either a local or remote repository.

For example, in the **local Git project directory**, do the following:

```bash
$ git show-ref

bd757c18597789d4f01cbd2ffc7c1f55e90cfcd0 refs/heads/main
bd757c18597789d4f01cbd2ffc7c1f55e90cfcd0 refs/remotes/origin/HEAD
6d325dff7434895753dcad82809783644dec75f6 refs/remotes/origin/html
dc89689e86c991c3ebb4d0b6c0cce223ea8e6e47 refs/remotes/origin/maint
....
1250aafa65e7ec62cf776d863ca8c7e4f822928c refs/tags/v1.6.6-rc2
d205d24b8ae17232babad615572bb0265bc029f1 refs/tags/v1.6.6-rc3
09e5ddd756bca67552aad623bab374614ae5e60d refs/tags/v1.6.6-rc4
```

To see what is in the remote repository, do the following:

```bash
$ git ls-remote git://git.kernel.org/pub/scm/git/git.git

bd757c18597789d4f01cbd2ffc7c1f55e90cfcd0 HEAD
6d325dff7434895753dcad82809783644dec75f6 refs/heads/html
dc89689e86c991c3ebb4d0b6c0cce223ea8e6e47 refs/heads/maint
8407cc83c60b5e45869c5f64cdcaafee5e9f2f92 refs/heads/man
bd757c18597789d4f01cbd2ffc7c1f55e90cfcd0 refs/heads/main
....
d205d24b8ae17232babad615572bb0265bc029f1 refs/tags/v1.6.6-rc3
94058a90cf3e10122037cd80ea48d3d52be5efd9 refs/tags/v1.6.6-rc3^{}
09e5ddd756bca67552aad623bab374614ae5e60d refs/tags/v1.6.6-rc4
ab0964d951e4ea88f9ea2cbb88388c1bcd4ae911 refs/tags/v1.6.6-rc4^{}
```

You will notice additional references in the remote repository that are not reflected in the clone.

If you want to update your repository with changes made at the remote site, you can do:

```bash
$ git pull
```

to synchronize.

### Publishing your Project (1)

Suppose you want to make your project available to others, either on your local machine or the network, for cloning, pushing, etc.

First, you need to create a bare version of your project:

```bash
$ git clone --bare git-test /tmp/git-test
```

Where you see that `/tmp/git-test` It contains a copy of your project's repository but none of the working files themselves. A local user can easily make a new cloned copy with the same command, excluding the `--bare` option:

```bash
$ git clone /tmp/git-test my-git
```

But what about network users? To make a copy available using the native Git protocol, you must have the Git daemon installed and running as a service. To make your bare repository accessible, you have to create an empty file in its main directory:

```bash
$ touch /tmp/git-test/git-daemon-export-ok
```

You can configure the Git daemon to run automatically by configuring either `xinetd` or `inetd` on your system, and by doing so, you can control behavior rather precisely. However, to keep things simple, you can just run the daemon in the background:

```bash
$ git daemon &
```

Then, a remote user can clone your repository with:

```bash
$ git clone 192.168.1.100:/tmp/git-test my-git
```

This allows them to clone and fetch but not push changes back. To provide everyone with the ability to push changes, you would do the following:

```bash
$ git daemon --enable=receive-pack
```

Additionally, this will be done for all Git repositories on the system. To enable write access for just one repository, you have to instead add to the configuration file in the repository the following lines:

```ini
[daemon]
receivepack = true
```

To enable access through the `http` Protocol: you must have a proper web server installed and running (probably Apache) and know a little about configuring it. You can place your project directory under \
&#x20;or in the unprivileged place /home/username/public\_html (in which case your server must be configured to allow such access).

​Before access is available, you have to go to the project directory and run the command:

```bash
$ git --bare update-server-info
```

Accessing through the web server can then be done through these commands:

```bash
$ git clone https://192.168.1.100/git-test my-git
$ git clone https://192.168.1.100/~username/git-test
```

You must substitute the right IP address or domain with the correct username.

Suppose you want to make your project available to someone who does not use Git or store archived material of your current working tree without including th&#x65;**.** `git` Repository information directories.

This is easy to do with `git archive` the following:

```bash
$ git archive --verbose HEAD | bzip2 > myproject.tar.bz2
```

If you want to create an archive corresponding to a particular point in time rather than the latest state, say to tag **v1.7.1**, you could do:

```bash
$ git archive --verbose v1.7.1 | bzip2 > myproject_v1.7.1.tar.bz2
```

### Fetching, Pulling, and Pushing

To bring your repository up to date with the original remote repository, you can merge from the original's main branch with:

```bash
$ git fetch
$ git merge origin/main
```

It is possible to do this in one single step with:

```bash
$ git pull origin main
```

And if you have the main branch already checked out, you can do the following:

```bash
$ git pull
```

Which merges with the `HEAD` branch of the origin repository. If you want to specify a particular branch, you can do either of the following:

```bash
$ git pull . branch
$ git merge branch
```

The inverse process to <mark style="color:green;">**pulling**</mark> is <mark style="color:blue;">**pushing**</mark> and getting your changes into the remote repository. To publish your revisions, you should first make sure your repository is clean and committed up to date; then, you can use any of the accepted protocols, such as:

```bash
$ git push git://remotesite.org/path/to/repo.git main
```

If you have write access, that will be all that is necessary. If you use SSH protocols, you will be prompted with passwords, as you might expect, unless you have configured SSH not to require a password each time.

Note that when you push, it should be to a bare repository. Otherwise, the push will not update the working tree of the remote repository. If you push to the currently checked-out branch, the results will not be what you expect.

#### Review Lab 11.1 - Accessing Your Repository Remotely with the `git://` Protocol

Create a repository and populate it; you can use a solution from a previous session to do this. To make a proper remote repository, first clone it on your local machine using the `--bare` option, as in:

```bash
$ git clone --bare /git-test /tmp/my-remote-git-repo
```

To make it accessible through the `git://` protocol, you must create the `git-daemon-export-`**ok** file in the main project or  `.git` subdirectory and start the git-daemon process. If you do:

```bash
$ git daemon
```

Someone can clone your repository by doing the following:

```bash
$ git clone git://ipaddress/tmp/my-remote-git-repo
```

More conveniently, you can specify a root, or base, directory for the repositories by doing:

```bash
$ git daemon --base-path=/tmp
```

{% hint style="info" %}
Please Note: You do not have to be a superuser to run `git-daemon` You will probably want to run it in the background or figure out how to get it to run as a service on boot.
{% endhint %}

For testing purposes, you might try removing the step of starting the daemon or creating the `git-daemon-export-ok` file to see what happens. If you have a partner on another machine or are running two machines, try cloning each other’s repositories using this method.

#### Review 11.2 Accessing Your Repository Remotely Using SSH

Make a new clone of the repository using the SSH protocol, using each of the two following methods:

```bash
$ git clone ssh://user@ipaddress/tmp/my-remote-git-repo
$ git clone user@ipaddress:/tmp/my-remote-git-repo
```

If you have a partner on another machine or are running two machines, try cloning each other’s repositories using this method.

You may have to install an SSH server to get this to work. On RPM-based machines, this might involve:

```bash
$ sudo dnf install openssh-server
```

While Debian-based systems will need to run:

```bash
$ sudo apt-get install openssh-server
```

#### Review Lab 11.3 - Accessing Your Repository Remotely Using HTTP

Make a new clone of the repository using the HTTP protocol, as in:

```bash
$ git clone https://ipaddress/my-remote-git-repo
```

We are substituting a correct value for the **IP address**.

If you happen to have a partner on another machine or are running two machines, try cloning each other’s repositories using this method.

You may have to install an HTTP server to get this to work. On RPM-based machines, this might involve:

```bash
$ sudo dnf install httpd
```

And on deb-based systems:

```bash
$ sudo apt-get install apache2
```

and then start it up with

```bash
$ sudo systemctl start httpd
```

Do not forget to run

```bash
$ git --bare update-server-info
```

in the project directory before accessing the repository through `https://`.

For this to work, the repository has to be available through your web server. For simplicity, you can put it under `/var/www/html` (or in `/var/www/git` on deb-based systems), or you can set up a link from there to the actual location, as in:

```bash
$ sudo ln -s /tmp/my-remote-git-repo /var/www/html/my-remote-git-repo
```

Of course, you can put it in other places, but we do not want to discuss the details of web server configuration here.

#### Review Lab 11.4 - Pushing Changes into the Remote Repository

Make some changes to your local repository, which we will then push to the remote location. You might just add a file or change one of them, etc.

First, try using the ssh protocol. Do you have any problems?

Now, try with the `git://` protocol. For this to work, you must configure the daemon  `--enable=receive-pack` for a global change for all repositories on your system (which is not intelligent) or configure the `config` file or your repository, as noted previously.

Can you push an update through the `https://` protocol?

#### Knowledge Check 11.1

1. When you clone a remote repository, you receive:

* [ ] A complete copy, including only the most recent commits of each branch
* [x] A complete copy, including all branches and their detailed history
* [ ] A read-only version of the repository

2. To make a compressed tarball of the most current version of your repository, do the following:
   * [ ] `git archive HEAD > myproject.tar.gz`
   * [ ] `git archive | gzip > myproject.tar.gz`
   * [x] `git archive HEAD | gzip > myproject.tar.gz`
3. There have been changes in the branch devel's remote repository, and you want to incorporate them in the **main** branch. A procedure that would work would be:
   * [ ] `git checkout devel ; git pull ; git checkout main ; git merge devel`
   * [ ] `git checkout main ; git pull ; git merge devel`
   * [ ] `git checkout devel ; git pull ; git merge main`
   * [ ] `git checkout main ; git merge devel`
