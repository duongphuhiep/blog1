---
title: "Git Merge Conflicts"
date: 2017-03-12T16:30:48+02:00
tags: ["git"]
categories: ["devops"]
---
I used Github for years. Most of my personals projects are on Github. However I'm only an amateur Git user who only knowledgeable enough to work with one-person projects. I suppose that many Github's users (or evens a majority of them) are like me.

Now, I need to use Git in a team. Things become complicated: branches to merge, conflicts pop up, cryptic error messages or strange situations... I tried many GUI tools with hope that it will make Git more friendly. In the end, I realized that there is no help. I have to learn and to practice Git seriously. There is many good Git GUI Tools out there, but if you don't understand Git, you don't understand them either (especially in case of problem).

I don't want to learn about the internals of Git. I only need to be proficient enough to work on a team. So I need to hone my skills to manage branches, merge branches and resolve conflicts. 

After a few days playing around with git, experiment all sort of branching merging, rebasing, resolve conflicts. I think that I got a hold of it and.. I like it (stockholm syndrome?).

This article is not a tutorial, there is already many tutorials out there. The main purpose is to show you how to practices and learn Git yourself.

## How to play with Git

* Create a new folder `git-playground`
* In this folder, we will simulate a team of 2 persons: "**Bob**" and "**Dave**" working on a project called "demonic" on a shared repository `host`. 
* **Everything is on your PC local.** (no need Github or internet connection)

```
git-playground
|
+- host
```

Create a shared repository in the `host` folder

```
cd host
git init --bare --shared demonic.git
```

**Bob** will make a clone of this repo to the `bob` folder

```
cd git-playground
git clone host/demonic.git bob
```

**Dave** will also make a clone of the shared repository to the `dave` folder

```
git clone host/demonic.git dave
```

Now, your `git-playground` resembles:

```
git-playground
|
+- host
|  |
|  +- demonic.git
|
+- bob
|
+- dave
```

Next you will open 2 terminals, one cds to `bob` to simulate **Bob**'s actions, other cds to `dave` to simulate **Dave**'s actions. For example: 

**Bob** creates a file `main.js` and push it to the shared repository:

```
cd git-playground/bob
vim main.js
...
git add main.js
git commit -m "bob start working on repository"
git push
```

On other terminal, **Dave** pull the `main.js`

```
cd git-playground/dave
git pull
```

All the setup is done. You are ready to play!

## Prepare the GUI tools

I mostly use command lines, but I prefer GUI tools for everything related to visualization such as 

* display branches / history: `gitk`
* diff, merge: [`meld`](http://meldmerge.org/)

`gitk` is bundled with the Git installation by default. I constantly call `gitk --all` to visualize the graph. 

The only tool I have to install in addition is [`meld`](http://meldmerge.org/). My [`.gitconfig`](https://gist.github.com/duongphuhiep/0801616d8c87bb71c535e8fbcd71e263) uses `meld` as the default "difftool" and "mergetool"

**I discovered that `meld` can smartly do the merge for me: menu "Changes > Merge All"**

You might also find more useful resources [here](https://github.com/duongphuhiep/duongphuhiep.github.io/wiki/Git)

## Play a classic merge

* **Bob** and **Dave** both commit some changes to `main.js`
* **Bob** does a `git push` first

* **Dave** makes a `git fetch`  
  => `gitk --all` will show you that the branch `master` is not the same as `origin/master`

* **Dave** is on his branch `master`, and he will have to merge to the branch `origin/master`: 

```
git merge origin/master
```

If there are conflicts, he will enter the process of solving the conflicts.

* he can see the conflicts status at anytime `git status`
* he can quit the process at anytime: `git merge --abort`
* he can solve the conflict with help of `meld`: `git mergetool`

## Play with Rebase

Git provides 2 methods to merge branches: The classic merge, and the "rebase". Beginner often have trouble with the "rebase". 

Read [this chapter](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) first. You must to "eat" at least the first half. 

And again, the best way to understand it is to play with it:

- Make **Bob** push 1 commit to `origin/master`
- Make **Dave** commits 2 times on his `master`

After **Dave** run `git fetch`, he will see 2 branches (with `gitk --all`):

* the `master` with his two commits
* the `origin/master` with the commit of **Bob**

he will rebase his 2 commits on top of the `origin/master`

```
git rebase
```

After this command, he will enter the Rebase process. Each of his two commits will be "replayed" on `origin/master`. It is as if **Dave** is merging each commit to `origin/master` one by one, and each one might got conflicts:

* **Dave** can see the conflicts status at anytime `git status`
* each time he finshed to solve a conflict commit, he will continue to solve the next one `git rebase --continue`
* he can quit the process at anytime: `git rebase --abort`
* he can solve the conflicts with help of `meld`: `git mergetool`

## Long-live branch

- **Dave** creates a branch `topic001` and implements a big feature on it with many changes on `main.js`
- It is a long live branch, that **Dave** won't merge it back to `master` anytime soon.
- On the other side, **Bob** is working actively on the master branch, and push very oftens.
- In order to maintain the long-live `topic001` branch, so that it is not diverged too far from the `master`, **Dave** often merges `origin/master` to his `topic001` branch, and solves all the small conflicts on the way. 

**Dave** notices that he often have to solve the same conflicts over and over again each time he do the **maintenance work** (merge `origin/master` to `topic001`). 

Why is Git so stupid? 

In fact Git is only smart to detect conflict situation, but let all the doubt of resolving conflicts to you and your external tools (eg. `meld`, `p4merge`, `kdiff3`, `tortoise-merge`..). 

Here come the rescues:

* 1) activate the [`rerere` option](https://git-scm.com/book/en/v2/Git-Tools-Rerere)

  ```
  git config --global rerere.enabled 1
  ```

  Git will memorize how did you solve the conflicts, and automatically apply it the next time it see the same conflict   again. You teach git how to solve the conflict so Git won't bother you next time.

* 2) A good 3-ways merge tool is absolutely necessary: eg. `meld` menu "Changes > Merge All". and other tools below..

Lesson to learn: Git won't try to be smart and won't automaticly merge things for you if there is doubt of conflict.

It could happen that git detected a conflict, but when you open it with your external merge tool (eg. `meld`, `p4merge`..), your tool is smart enough to resolve common conflicts and automaticly merge the 2 files with no trouble. You can say that Git is stupid when it comes to auto-merging, it won't take evens a slightest risk. So a good merge tool is especially important to complement Git.

### Other tools

They seem interesting, but I didn't try yet: 

* [Semantic Merge](https://www.semanticmerge.com/) - this paid tool claims to understand C#, Java.. See the [screenshoot](https://www.semanticmerge.com/images/illustration-what.png), it seems that the tool knows how to handle "moving" code blocks (a very hard problem for auto-merging)

* [Deep Git Blame](http://www.syntevo.com/deepgit/tour) - free tool to trace back origin of the codes. While solving conflicts, we often ask where did this code came from (and cause the conflict now)?

## How to reduce conflicts

I hears peoples claim that [git-flow](http://stackoverflow.com/a/13448924/347051) or a good use of [Rebase instead of Merge](https://www.derekgourlay.com/blog/git-when-to-merge-vs-when-to-rebase/) will reduce the conflicts poping up.

IMHO, they are only illusions. If 3 persons change the same codes, you will have to solve conflicts 2 times whatever VCS or flow you are adopting.

The only way to minimize conflicts is to change your project architecture and organize your codes so that everything is isolate as much as possible; to minimize the chance that 2 differents developments step on each other. But in case they both need to touch the same common codes, then conflicts are unavoidables.

A good flow won't really help you to minimize conflicts. But I think that

* it might facilitates the conflicts resolving task.
* it might help to better distribute this task among team members (it won't neccessary be you who will have to solve conflicts all the time)

It is very subjective. But, the general rule is:

If you regulary merge 2 branches so that they won't diverge too far from each other, then you will need to solve only "small" conflicts. I called it the **maintenance work** (ref. the [Long live branch](#long-live-branch-4) section). 

Voilà, it is the only way to deal with conflicts. Don't expect any silver bullet on this matter.

## How to rewrite history

Unlike [Mecurial](https://www.mercurial-scm.org/), it is easy to change history in a Git repository. Even though I'm not a fan of changing the history. I don't care if my history is a mess of branches. 

It might important to maintain an optimized history for big open-source projects so that every commits appear to be carefully planned and the repository size won't blow up. 

But IMO, for most common projects, history is sacred. In addition, we shouldn't waste time to craft out a beautiful history but rather concentrate to the codes.  In this regard, I got the same opinion with Mecurial.

However, there is time that I really need to change a past commit:

**Bob** did a stupid thing, he committed the database password, it was 2 commits before (HEAD~2) so that he cannot just `amend` it.

To fix this situation, **Bob** will 

* go to the stupid commit: `git reset --hard HEAD~2`, 
* remove the password and `amend` the change to a new commit. It means that the new commit will be the same as the old one except that there is no password there.
* then rebase others commits to the new one.

**Lesson to learn**: you can move around commits (= go to the past)  with (`git checkout` or `git reset`, then make commit to rewrite history. In fact you are creating other branch of universe. About the old commits, if there is no tags, no branches reference to them, these commits will be garbage collected after 30 days.

Read also: [Reset Demystified](https://git-scm.com/blog) *Honestly speaking, I didn't read it :)). But I got the 'feel' of 'git reset' by practicing*.

## cherry-pick

It will also come in handy: You can pick some commit to replay, and ignore others.

Sometime, I did some small commits on a "unstable" branch. Later I can cherry-pick some of these commits to merge to the "stable" branche, so that I can release them earlier on production without waiting for the unstable branch.


## Conclusion

As a professional coder, I tried to get confortable with at least one Centralize CVS (eg. SVN, TFS..) and one Distributed CVS (eg. Git, Mecurial..).

On the Distributed side, I choose Git which is a little more difficult (more verbose) than other alternatives (eg. Mecurial). But, whatever you chose, you'll need to learn it and practice it in order to benefice its power. However

* Git is so popular that you can always find the solution for all sort of problems (forget about the man pages! [git book](https://git-scm.com/book/en/v2) and [stackoverflow](http://stackoverflow.com/questions/tagged/git) are your friend). All the questions I wanted to ask, someone usually asked already, and I usually got the responses straight to the point.

* Just play with Git a lot, explore its power. No book is better than practicing yourself. Once you got a hold on it, I doubt that you will look back to something else.

Enjoy the game!


# Update 2021

It's been a long time since this post, my team and I collaborate in git on a daily basis. The way we used git evole:

* We use UI tools such as [Fork](https://git-fork.com/)(free), [Gitkraken](https://www.gitkraken.com/)(paid) to visualize history, search for commits messages etc.. and sometime to resolve conflicts as well.
* We use IDEs to resolve conflict (no longer use meld or `mergetool`).
* We use IDE to make commits, because we always go-over all the changes one last time + make sure everything compile before commit. It is more pratical to checks directly in the IDE.
* I still use git commands line for many things.. as long as it make me faster than doing the same thing via UI. Sometimes a small command line worth many click + searching on UI, so I used UI mostly to visualize things. Here my [most used git command](https://gist.github.com/duongphuhiep/0801616d8c87bb71c535e8fbcd71e263)
