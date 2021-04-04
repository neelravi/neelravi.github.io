---
layout: post
title: Tech Tips
subtitle: CP2K wrap atoms back to the home unit cell in VMD
gh-repo: neelravi/neelravi.github.io
#gh-badge: [star, fork, follow]
tags: [technology, tips, CP2K, VMD]
#comments: true
---


## CP2K wrap atoms back to the home unit cell in VMD

The following set of commands in the VMD console will wrap back the atoms in the neighboring unit cells back to the home unit cell.

a, b, and c are the lattice constants in x,y and z directions.

```perl
pbc set {a b c} -all
pbc box
pbc wrap -all
```