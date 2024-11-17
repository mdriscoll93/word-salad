---
icon: github
---

# Managing Local and Remote Repositories

## Objectives

* Understand the role of Git in facilitating distributed development
* Clone a repository
* Pull (and fetch) changes from a repository​
* Push changes to a repository
* Understand how to run Git daemon to make your repository available over the Internet

### Working with Others

Many other revision control systems are built on the notion of a central, authoritative repository which is the hub that individual developers work with. An essential difference in Git's architecture is that no one repository retains such a central role, at least not structurally.

Maintaining separate repositories makes sense in at least three situations:

1. A developer is working autonomously.
2. Developers are separated across a large (even global) network. A local group of developers may share their own repository to collect changes of immediate import and interest.
3. There are divergent projects or sub-areas that are worth developing deeply on their own, with a mind to eventual merging of changes that are found to be beneficial to the main project.

Because Git has such a strong infrastructure dedicated to branching and merging multiple repositories, it can handle relatively easily the complicated logistics of distributed development.

Some essential operations involved in handling remote repositories include:

* **Cloning**: Establish an initial copy of a remote repository and place it in your own object database. The essential command is `git clone`.
* **Pulling**: Bring home changes from the remote repository keeping your tracking branch up to date. The essential commands are `git pull` and `git fetch`.
* **Pushing**: Submit your changes to the remote repository. The essential command is `git push`.
* **Publishing**: Make your repository available for others to clone and pull from, and perhaps to push to.

Git uses tracking branches to handle content in remote repositories. These are local branches that serve as proxies or references, for specific branches in remote repositories.

There is a special kind of repository called a [**bare repository**](https://git-scm.com/book/en/v2/Git-on-the-Server-Getting-Git-on-a-Server). Normally, you work in a development repository. A bare repository has no working directories and is not used for development and has no checked out branches. It is used only as an authoritative place to clone and fetch from and push to. It is created with the `--bare` option to the `clone` command.

### Cloning (1)

Getting an initial clone of a remote repository is as simple as doing:

```bash
$ git clone git://git.kernel.org/pub/scm/git/git.git
```

which brings down the entire Git repository for Git itself. It puts it in a directory called `git` which contains the usual `.git` subdirectory with all the objects, indexes, etc., that are part of the repository.

This can be a large download, but it is a one time operation.

Note the use of the `git://` protocol in the remote specification. It is the preferred method, but not the only one that can be used. Other possibilities are:

* `file:///path/to/repo.git`
* `ssh://user@remotesite.org[:port]/path/to/repo.git`
* `user@remotesite.org:/path/to/repo.git`
* `ht‌tp://remotesite.org/path/to/repo.git`
* `ht‌tps://remotesite.org/path/to/repo.git`
* `rsync://remotesite.org/path/to/repo.git`

The `git://` method is the fastest and cleanest and should be used whenever it is supported.

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

You will notice that there are additional references in the remote repository which are not reflected in the clone.

If you want to update your repository with changes made at the remote site you can simply do:

```bash
$ git pull
```

to synchronize.

### Publishing your Project (1)

