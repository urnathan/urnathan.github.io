---
layout: post
title: An Accidental Project
category: blog
comments_id: 3
---

I made a thing I didn't expect to.  When working on a personal
project, and being the good engineer I am, I needed to test it behaved
as intended.  I started out with simply adding rules to a Makefile.
But that got increasingly cumbersome, and difficult to really tell
what was happening.  I needed a proper testsuite.

At this point I might have turned to
[dejagnu](https://www.gnu.org/software/dejagnu/), because that's the
testsuite I know.  But I didn---
layout: post
title: An Accidental Project
category: blog
comments_id: 3
---
't want to do that because (a) I've used
it and (b) [Ian Taylor](https://www.airs.com/blog/archives/499) has
some things to say about it.  I went looking for something else.  My
first port of call was [llvm's](http://llvm.org/) testsuite, as that
seemed to have roughly the right behaviour.  But it's not a separate
subproject within the LLVM project, is poorly documented, and has a
number of baroque inconsistent syntaxes closely tied to its use.  I
googled around for something else but it was all too confusing.

So I decided to start writing my own.  That's pretty damning in several ways:

* the existing projects are too confusing to start

* the closest one to my needs was too confusing to extract from its
  environment

* my chutzpah is, um, tsu fil

This forced me to break apart the configurey glue I was using, and that
created the [NMS](https://github.com/urnathan/nms) project.  And then
learn about git submodules and why [not to use
them](https://urnathan.github.io/blog/2020/04/18/git-submodules.html).

Anyway, I've spent the last 6 weeks or so creating another testsuite
infrastructure.  It seems to fulfil my own needs -- excitingly I use
it to test itself.  I don't think it ready for public consumption, I
need to use it more for the project that orignally wanted testing.

That's what engineers are like &mdash; too lazy to figure out if a bunch of
misses are __close enough__ that they'll spend a month and a half of
evenings building a thing that that works for them, completely
discounting the downstream support cost of that.