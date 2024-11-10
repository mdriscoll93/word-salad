---
icon: code-merge
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

For example, if there are two change sets, you could apply one and then the other; the order should not matter. The only technical refinement is that after one set of changes, there may be a shift of lines where the second set of changes has to work due to insertions or deletions made by the first one. Any sensible revision control system or patch manager can handle such simple offsets.

Even in this simple case of non-overlapping change sets, more subtle problems can arise that only humans are likely to notice. Complications can easily be introduced, for example, if two distinct change sets both try to fix the same bug but do it in different places with different methods.

In such cases, the Git methodology of having many small patch sets and commits, combined with tools like bisection, can help resolve such subtle problems quickly.

In the case of conflicting change sets, Git has been designed from the outset to have strong tools for conflict resolution. This session will focus on how to use such tools.

Merging can be seen as the inverse process of branching. We have already discussed how Git handles opening up a new branch. Without an efficient automated process for rejoining, we would have a much weaker revision control system.

## Merge Commands (1)

Merging the `devel` branch into the current main branch
