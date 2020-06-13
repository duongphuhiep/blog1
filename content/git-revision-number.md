---
title: "Git Revision Number"
date: 2017-03-12T12:32:04+02:00
tags: ["git"]
categories: ["devops"]
draft: true
---
As a former SVN user migrating to GIT, I miss the SVN revision number. 

I usually put the revision number in the package name, and I can tell which package is the newest one because I know that the revision 18742 is newer than the revision 18730.

The SVN-revision number equivalent in GIT is the horrible `SHA` thingy, I cannot tell if the commit `b3c76e` is newer than `df8ec9` (or am I?). 

Here come the rescue:

```
git describe [SHA]
```

It will count the number of commit from the last annotated tag. 

Example, my latest annotated tag is `v0.0.0`, and my `git describe` gives 

```
v0.0.0-114-gbcb5fb2
```

It means that

- it is the 114th commit since the tag `v0.0.0`
- the `bcb5fb2` is the commit `SHA`. We might have many 114th commit from other branches.

if you don't have any annotated tag, `git describe` will return 

```
fatal: No names found, cannot describe anything.
```

So you will have to create a tag somewhere, for example 'v0.0.0' for the 'initial commit'

```
git tag -a v0.0.0 -m "genesis" <SHA of the first commit>
```

To find the `<SHA of the first commit>`:

```
git log --oneline --reverse | head
```
The first line is the first commit

## Commit info
when you see a `v0.0.0-114-gbcb5fb2` and asking what is the commit **`bcb5fb2`**

```
git show bcb5fb2
```

