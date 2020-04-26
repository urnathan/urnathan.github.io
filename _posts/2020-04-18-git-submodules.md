---
layout: post
title: Git Submodules
category: blog
comments_id: 1
---

TL;DR: Symlinks > Submodules.

I was developing a personal project, (not ready for public release),
and realized it'd actually grown into 2 separate projects.  But if I
did that, I'd end up with a bunch of duplicated boilerplate.  So
breaking it into two, would mean breaking it into 3 and then
symbolically duplicating the boilerplate.  The other wrinkle was that
I still wanted the ability to have one of the pieces appear inside the
other piece.  Essentially I was trying to create the following virtual
class hierarchy in a source graph.

```c++
class Boilerplate {};
class ProjFoo : virtual Boilerplate {};
class ProjBar : virtual Boilerplate, virtual ProjFoo {};
```

(In the build tree, there'd be two instances of the Boilerplate,
because that has distinct data from each of the two projects.)

I was wondering whether I should have sibling checkouts and use
symlinks to make them appear in eachother as I wanted.  But, a bit of
googling showed that git submodules were a thing.  Searching for
'should I use git submodules', showed lots of pointers as to how to
use Git Submodules to do this.  Nothing showed as to not do that.  All
the uses didn't match my use of ``Boilerplate`` appearing in two
places, but I figured I could experiment.

So I broke my project apart using ``git filter-branch``, and then
added the pieces I wanted with ``git submodule``.  Now I needed to to
a bit of tidying up.  It didn't seem to be supporting the two
instances of `Boilerplate`, but I thought that might just be my
inexperience with submodules.  When committing changes I noticed some
quirks with committing the change to the submodule and updating the
containing module.  Again, I thought I was messing up, and with
experience would be getting it right.

Anyhow, I asked on an IRC channel I hang out on, and was told not to
use submodules, they are confusing and brittle.  Googling for 'git
submodules are bad' found a couple of enlightening blogs, which had
not shown up in my 'should I use ...' searches.  Essentially the
quirks I described in the previous paragraph were not me messing up!
It was submodules.  While submodules can permit the imported to
specify the hash of the imported submodule, this behaviour is
confusing in practice, and results in *excitement* updating from
various places.  Let's not do that, git is exciting enough :)

I'd not gone far down this route, it was simple to convert to the
symlink mechanism.  Each project that wants a subproject uses a
symlink to a sibling of its toplevel directory.  This naturally solves
the virtual inheritance graph described above.  A bit if configurey
can verify there's a project there.  And for distribution outside of a
VC, one could replace the symlink with an actual directory tree.  (In
C++ terms, pass-by-value, rather than pass-by-reference.)

I'd already pushed some of the submodule work, and didn't want that
interfering with further pushes &mdash; who knows if git'll do
something baroque.  I learnt about ``git push``'s ``-f`` flag to push
a ``git reset --hard`` repo.  Thus completely erasing the submodule
work from history.  **That was only safe because this is a private
repo and I am the only committer.**

Symlinks are the virtual inheritance of the directory tree.
