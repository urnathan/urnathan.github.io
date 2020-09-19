---
layout: post
title: On Formatting Source
category: blog
comments_id: 6
---

Code formatting, what's the right way?  Everyone has an opinion, and
there are several different styles to choose from.  I recently watched
a [Kevlin Henney talk](https://www.youtube.com/watch?v=ZsHMHukIlJY),
and he made a number of interesting observations.  Some of them were
new to me, and more excitingly were right.  So much so that I've been
experimenting with the implications and have started using them where
I can.


So, let's start with some basics.  My language of choice is C++, and
my default style is GNU.  Prior to meeting GNU code I had already
converged on 2-spaces per indent and block-scope bracing are on their
own lines:

```C++
void foo ()
{
  if (cond)
    {
      line1;
      line2;
    }
  else
    oneline;
}
```

The two things GNU style added to my style were:

1 white space between (most) operators, and in many other places.

2 line breaking puts the operator at the start of the second line
(which is indendented).

The first brought home that whitespace is important.  It makes your
code easier to read.  You're used to reading words separated by
spaces, the same is true, with a few exceptions, of the operators you
use in your code.  `a+b` looks like one thing.  `a + b` is clearly 3
things.  It took humans a while to figure that out with natural
languages -- old lation enscriptions do not have spaces, and are thus
very hard to read.

The second is rather subtle.  When you're reading one line of code how
can you tell if it continues the previous line?  Well, clue #1 is that
it's indented.  But that could be because it's a nested statement
inside an `if` or whatever.  You an look at the start of the previous
line to figure that.  But that still doesn't tell you enough -- this
could be a continuation of the condition itself!  If you put the
operator at the end of the line you also have to look at the end of
the previous line.  That means your're looking in two places, one of
which depends on the length of the previous line.  If however you put
the operator at the beginning of the continuation, you have an
immediate clue.  Compare:

```c++
if (some + variables >= value && frob () &&
    b == 5)  // badly indented, or continuation?
  widget (6);
```
vs
```c++
if (some + variables >= value && frob ()
    && b == 5)  // must be continuation
  widget (6);
```

Of course there are some possibly ambiguous cases unary vs binary + or
multiplication vs indirection.  However, those can be disambiguated by
the default spacing rules -- no space after unary + or indirection:
```c++
a = some_long_expression
  * multiplicand;  // contination
*ptr = value;   // not continuation
```

Kevlin talked about line length -- why stick at 80?  We have lovely
wide displays and variable sized editors.  So why the 80 column mind?
This continuation placement is a small case of that.  Putting it on
the left means one remains focussed on the left side.  I'll get back
to this later.

A final thing I've learnt is to add blank lines -- known in natural
language as paragraph breaks.  The same is true of code.  Add blank
lines between the phases of an algorithm.  You'd do the same in a
natural language essay, when you move on to a new concept. (Look I did
it with this paragraph!)

```c++
// validate input
if (...)
  error (...);
if (...)
  error (...);
// process
for (auto a: ary)
  ...;
```

vs:

```c++
// validate input
if (...)
  error (...);
if (...)
  error (...);

// process
for (auto a: ary)
  ...;
```

Who cares if we're taking up vertical space?  It's not like we're
printing this stuff on physical pages.  Our editor windows are large
enough that this isn't going to hide stuff -- and if your algorithm is
too long for that, *it's too long anyway*.

Anyway, Kevlin made the observation about line length.  What happens
if you don't have an 80 column limit?  Well mostly nothing -- nearly
all your lines remain short -- often less than 40 chars.  Except every
now and again one line zooms off the the right.  It either
*dissappears from view* or gets wrapped onto multiple display lines.
Both are extremely confusing.  Take a look at web pages -- they still
remain within the 8-inch or so line width.  Long lines are hard for
humans to remain focussed on.  Also we take cues from things that
happen on the left edge of the page -- para indentation, bullet
points, etc.  We never put them on the right.  Heck, even edit change
bars are in the left margin.

What does this mean for formatting?  (a) formatting clues should be on
the LHS (see above line break discussion) and (b) indentation should
be a property of the concept being conveyed, unaffected by the
spelling of preceeding words.

#b was enlightening.  Consider a function definition:

```c++
void foo (Type arg1, std::vector<thing> arg2, std::string name = "",
          Container value = nullptr)
{
   ...
}

```

That looks perfectly reasonable.  I;ve been writing them like that for
years.  But consider that line wrap, and what happens if we rename
`foo`, but forget to reindent?

```c++
void foo_variant (Type arg1, std::vector<thing> arg2, std::string name = "",
          Container value = nullptr)
{
   ...
}

```

Oops, the remaining paramters are not nicely aligned.  Now try adding
an attribute to it:

```c++
void foo_variant [[gnu::no_return]] (Type arg1, std::vector<thing> arg2,
                                     std::string name = "",
				     Container value = nullptr)
{
   ...
}

```

I had to rewrap the parameter list.  *And* I've reduced the space I
have for parameters by shiftng the open paren to the right.

We're doing this wrong.  We should place the paramters entirely on
their own line.  Then they're unaffected.  The error I've been making
is considering the declaration to be a single thing, so it should be
on a single line.  Except when it can't.  Instead I should think of it
as a set of things, each of which gets its own line.  Thus:

```c++
void foo_variant
  [[gnu::no_return]]
  (Type arg1, std::vector<thing> arg2, std::string name = "",
   Container value = nullptr)
{
}
```

Put the attributes on a separate line, put the parameters on a
separate [set of] line(s).

Why keep the return type with the name?  Well, we're introducing the
name as a new entity.  That's kind of the important thing about the
declaration.  Unfortunately we can't put that first, but we can keep
it on the first line.

This 'separate components on separate lines' rule can apply in other
situations too.  For instance, where do base classes go?  after the
class name, or with a line break?  (with a line break):
```c++
class D : public A
{
};
```
vs
```c++
class D
  : public A
{
};
```

(The same rule applies to enumerators with underlying types.)

Where do your except specification and/or this qualifiers go -- on
separate lines:

```c++
class A
{
public:
  void foo
    ()
    const
    noexcept;
};
```

Now you don't have to go searching for the end of the parameter list
to see what follows on that line.  It's much clearer.  I discovered
`override` is really clearer:
```c++
protected:
  virtual bool ProcessLine
    (std::string_view const &variant, std::string_view const &pattern)
    override;  // stands out here, doesn't it?
```

What about defaulted or deleted functions?  The `= default` bit *is*
the definition.  So it should go where you'd put the definition:

```c++
private:
  Error &operator=
    (Error &&)
    = delete;
```

Variables with complex initializers (anything braced) has similar
treatment.  Put the `=` at the start of the next line:
```c++
Type var
  = {
    init, init, init
};
```

You might notice that even in the zero-parameter case, I put the
parens on a separate line.  That's important, they're making it clear
it's a function as opposed to a variable.  It's not a waste of space.

I've been using this for a couple of weeks, and it is really nice.
There's a simple rule to follow, and indentation follows meaning, not
value.
