---
layout: post
title: A Hackathon
category: blog
comments_id: 4
---

This Memorial Day weekend is a 4 day holiday for me, because my
employer decided we should all take Friday off too.  But of course, no
travel.  I decided that I needed to properly librarify my modules
protocol library of mine, so that it could be extended for other uses
and used in other projects.

First I needed a name, that of course is one of the hard problems of
CS.  I punted as one didn't come to mind &mdash; I can always apply
`sed` later.

During development I used the testsuite I [accidentally
wrote](/blog/2020/05/03/accidental-project.html).  That was a little
awkward and I added a `FIXME: it'd be nice to have here documents`.
That niggled me, and adding it required some reworking of a parser.
And I realized the grammar I had was awkward to use.  I spent the next
2 days reimplementing that and then finally adding `here` document
support.

Then testing the buffer handling of my library was easier, with less
indirection.  Hurrah!

I knew my testsuite wasn't ready for prime-time.  Using a tool really
shows you whether you've got the design right.  You shouldn't be
scared to redo the design in the face of user experience.

Now to decide on a good name for the module protocol library.  How was your
Memorial Day?
