---
layout: post
title: Tech Tips
subtitle: Create a tag from a particular commit git
gh-repo: neelravi/neelravi.github.io
#gh-badge: [star, fork, follow]
tags: [technology, tips, git, github]
#comments: true
---


## Create a tag from a particular commit git / github


1. Create a tag from a particular commit and give some message to the tag

`git tag -a v1.0.0 fg3ab1b -m "Tagged the first commit with v1.0.0"`



2. Verify that the tag is created and all the previous tags

`git tag -n`



3. push the tag to remote

`git push origin v1.0.0`

