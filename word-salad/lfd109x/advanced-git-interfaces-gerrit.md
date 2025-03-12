---
description: >-
  While Git is very powerful, and graphical interfaces such as GitHub may be
  very useful, more complicated workflows can benefit from the use of tools such
  as Gerrit, which we will now discuss.
icon: plus-minus
---

# Advanced Git Interfaces: Gerrit

## Objectives

* Describe Git workflow with Gerrit

### Modes of Distributed Development

Git has well-established methods of workflow. It is pretty flexible, and projects can choose quite different approaches, but it is always based on a cycle of:

* Make changes in the code, probably in a development branch
* Commit those changes to the branch; there may be one or more changes (patches) per commit
* Publish those changes through a push or a pull request
* The changes will be reviewed and merged if necessary or sent back for further work

This method works well if you have a basic pyramid view of things. Each subsystem has a maintainer managing its piece of the work, and that manager has ultimate authority over the changes to that subsystem before passing them up the pyramid for ultimate review and merging.

But what if you want to have a more dispersed review? That’s where **Gerrit** comes in.

The picture below illustrates a simplified Git workflow.\


<figure><img src="../../.gitbook/assets/image (14).png" alt="" width="563"><figcaption><p>typical git workflow</p></figcaption></figure>

### Gerrit

Gerrit is entirely built on Git, but it adds another layer of code review before changes are committed to the authoritative main repository. Multiple contributors will likely do this review rather than just one powerful maintainer.

While such a workflow is not new (most projects have multiple reviewers with some structure for who makes the ultimate decisions), the Gerrit architecture is designed to formalize this procedure.

This works best when there is one change per commit rather than a block of them, as it is easier to review and modify/reject/accept each one on its own merits.

Below, you can see the Git workflow with Gerrit.

<figure><img src="../../.gitbook/assets/image (1) (3).png" alt="" width="563"><figcaption><p>git workflow with Gerrit</p></figcaption></figure>

### Review Process

From the preceding diagram, Gerrit introduces a reviewing layer between the contributors and the upstream repository.

* Contributors submit their work (one change per submission is best) to the reviewing layer.
* Contributors pull the latest upstream changes from the upstream layer
* Reviewers are the ones who submit work to the upstream layer

The reviewers evaluate pending changes and discuss them. According to project governing procedures, they can grant approval and submit it upstream, or they can reject or request modifications.

Gerrit also records comments about each pending request. He preserves them in a record, which can be consulted at any time to document why and how modifications were made.

#### Review  Lab 13.1 A Gerrit Walkthrough

Since we do not have a Gerrit server set up, it is not easy to do an exercise demonstrating its capabilities.

The project, however, has a great [walkthrough example](https://gerrit-review.googlesource.com/Documentation/intro-gerrit-walkthrough.html), which demonstrates step-by-step how to:

* Make a change
* Create the review
* Review the change
* Rework the change
* Verify the change
* Submit the change

Please read this example thoroughly.

\
