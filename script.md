# Intro

== Jamie ==

[ad lib]

She's Piper Thunstrom, known for bleeding on stage, writing a little game engine, and being a menace about community management.

== Piper ==

I'm also a business school survivor, writer, game dev, and get paid to make really big queries.

She's Jamie Bliss, less well known for writing the same little game engine and way too much open source.

== Jamie ==

My career started with teenage midnight code and went downhill from there.

== Piper ==

You can find me on the fediverse @pathunstrom@ngmx.com.

== Jamie ==

And I'm @astraluma@TacoBellLabs.net

== Piper ==

Together, we're the maintainers of PursuedPyBear.

== Jamie ==

We started Teahouse Hosting, making the web easier for everyone.

== Piper ==

And we even convinced a start up to hire both of us one time.

== Jamie ==

We got called "intense".
I think we scared them a little.

== Piper ==

As python's poly power partners, we throw ideas at each other a lot.
Some of them are good ideas. . .

== Jamie ==

Like setuptools (2006)

== Piper ==

And some of them are ... less obviously useful

== Jamie ==

Like setuptools (2026)

== Piper ==

So we thought that instead of just leaving them in discord histories and closed PRs, maybe we can make a talk out of them.

And North Bay Python was silly enough to give us a slot to do so.

# Bags

== Piper ==

We're going to start with a softball.

Jamie, how do you solve semi-structured data?

== Jamie ==

Can we clarify "semi-structured data"?

== Piper ==

So... Something JSON shaped? (slide)

== Jamie ==

Oh! Like an unpopular REST API with no client!

So, if you use dictionaries, you get easy membership with dot-get and the in operator,
but data access is a quoted string and brackets. 

Instead, if you use classes (what I call "structs"), data access now requires one
punctuation but conditional access now involves get-attr and has-attr. Plus,
many struct libraries require you to define your schema before you can use it.

== Piper ==

Okay, so I repeat:

What's your solution?

== Jamie ==

So let shove all the data into a bag--a data bag: a data holder
that acts like both a map and a struct.

== Piper ==

Okay, but that's not new. It's such a good idea that Javascript uses it almost
exclusively.

== Jamie ==

It isn't even new in Python--versions of this written with dunder methods have
existed for a while.

But I have a cleverer idea:

```
class Bag(dict):
    def __init__(self):
        super().__init__()
        self.__dict__ = self
```

Just subclass dict and then use yourself as your own instance dictionary!

== Piper ==

Well, if the implementation is easy to explain...

== Jamie ==

No extra memory, no performance impact, just a floppy container of data!

== Piper ==

I feel a "but" coming...

== Jamie ==

Well, it's basically impossible to describe in the type system?

== Piper ==

It might be a bad idea. Yeah.

I guess we're treating type hinting like a conspiracy by big type to sell more types.

== Jamie ==

Just like uppercase!

# PPB as elaborate stage metaphor

== Piper ==

You know how they say naming things is one of the hard parts of computer science?

== Jamie ==

<affirmative>

== Piper ==

I've never found that to be true.

After all, names are the root of conceptual abstraction!

And I really like abstractions.

== Jamie ==

Okay, we're talking about cursed code, wouldn't "good abstractions" be the opposite.

== Piper ==

You've met me, right?

So there's an alternate universe, before I ambushed you in a PyCon hallway to improve the event
system, where ppb used a very different naming convention.

In this universe, we settled on fairly conventional naming scheme for a game library.

GameEngine, GameObject, System, Scene, Sprite.

But ppb got named after a Shakespeare reference!

== Jamie ==

<negative>

== Piper ==

In our alternate timeline, our fundamental unit of game would be the Actor.

You group them with Sets into a Scenes.

This gives us a really cool distinction between mobile and stationary objects!

Then our subsystem architecture would be Techs since they operate behind the scenes on the game
state.

And we'd wrap all of this up in the Globe. You know, the bard's theater!

== Jamie ==

[beat]

It's probably best it went the way of Yorick.

# Magic Fields

== Piper ==

One of the things about PPB is that it uses classes and class heirarchies for both behavior inheritance and instance
templating--if you want a bunch of sprites to have common properties (eg, the same graphic), you make a sprite subclass
with that as a class property

```python
class Thingy(ppb.Sprite):
    image = ppb.Image("thingy.png")


things = [
    Thingy(position=ppb.Vector(0, 0)),
    Thingy(top_left=ppb.Vector(1, 1)),
    Thingy(bottom_right=ppb.Vector(-1, -1)),
]
```

== Jamie ==

During peak Pursued Py Bear development, one of the things that would happen is that Piper would go somewhere and give a
talk about PPB, and then I would watch the talk and I would file bugs for all the things that tripped her up or needed
apologies or felt rough. I can say quite well that PPB is very easy to teach and works well.

But then you have problems like "oops, vectorish values aren't actually Vectors" and "rotation maybe should be capped"
and "position, bottom, top, left, and right are actually different views of the same data".

```python
# You can't make this work

class Sprite:
    @property
    def rotation(self):
        return self._rotation

    @rotation.setter
    def rotation(self, value):
        self._rotation = value % 360


class Spam(Sprite):
    rotation = 480
```

But we're Python magicians! Witches, even! We can solve this.

== Piper ==

Should the audience be afraid?

== Jamie ==

Ok, we need to start with the attribute lookup order

[attribute resolution flow chart, credit: https://blog.ionelmc.ro/2015/02/09/understanding-python-metaclasses/ ]

You have data descriptors, the instance dictionary, the class dictionary, the MRO, descriptors, and a sprinkling of dunder methods. Just note that any time this says class dunder dict, it's actually multiple lookups down the MRO linearization.

== Piper ==

So, yes, they should.

== Jamie ==

So what we need to do is:

1. have a special declaration of fields so that subclasses can set default values without having to redeclare any
   behavior

So what you do is you define an inner class Fields where you can keep all the descriptors, like this:

```python
class Sprite(FieldMixin):
    class Fields:
        @property
        def rotation(self):
            return self._rotation

        @rotation.setter
        def rotation(self, value):
            self._rotation = value % 360
```

and you can wrap up some of the common cases with things like `typefield` or `conversionfield` to make it easier

== Piper ==

So this looks all well and fine, unusual but not entirely out of line for other Python frameworks. How do you implement
it?

== Jamie ==

Well, first, on class creation, you look for a Fields inner class and any existing fields in the parent classes, and you
build out a dunder fields to keep it all in.

We can ignore the earlier diagram because those attributes have been explicitly declared, and we know exactly what to do
with them. Just gotta override dunder get attribute, dunder set attr, and dunder del attr to plumb in the descriptors we
set aside earlier. Easy enough, everyone in this room has done that, right? [mild sarcasm]

```python
    def __setattr__(self, name, value):
    try:
        field = type(self).__fields__[name]
    except KeyError:
        super().__setattr__(name, value)
    else:
        field.__set__(self, value)


def __getattribute__(self, name):
    try:
        field = type(self).__fields__[name]
    except KeyError:
        return super().__getattribute__(name)
    else:
        return field.__get__(self, cls)


def __delattr__(self, name):
    try:
        field = type(self).__fields__[name]
    except KeyError:
        super().__delattr__(name)
    else:
        field.__delete__(self)
```

You can see in each case, we check to see if the field's defined, and if it is, we pass it to the field descriptor, and
if not, pass it to the rest of python.

And from there, we just have to add two additional initialization behaviors.

== Piper ==

"Just" she says, "just."

== Jamie ==

First, on instance creations, do a set attr on all the kwargs passed to init

```python
    def __init__(self, **fields):
    for name, value in fields.items():
        setattr(self, name, value)
```

Second, on class creation, do the same thing for any fields defined that exist in the class body:

```python
    varsdict = vars(cls)
for name, field in iterfields(cls):
    if name in varsdict and hasattr(field, '__set_class__'):
        # This is to get around problems with naively running __set__ on
        # the class.
        field.__set_class__(cls, varsdict[name])
```

And that's how you make a whole new category of attribute in python!

== Piper ==

Okay, that was. . . a lot. But also, I remember this PR being a lot bigger.

== Jamie == 

I have a few hundred more lines, but most of them are unnecessary.

== Piper ==

Okay, let me fast-forward through this. . .

So, you've invented an entirely new way to declare properties.

Why?

== Jamie ==

Python doesn't let you do it otherwise

== Piper ==

There is a reason we tell stories about this PR.

== Jamie ==

:D

== Piper ==

There's a reason nobody does it this way, nobody even remotely does it this way.
Experienced developers will have to paradigm shift.
New developers will forever be puzzled by everyone else.

== Jamie ==

:(

== Piper ==

Third, while we could teach mypy this, it's perfectly comprehensible hinting, I don't think we want to get into the
business of typing plugins.

# Self -> my

== Piper ==

One of the strengths of Python is how readable it is.

I take pride in my ability to write good python code that people with no coding background can read.

But there is one place that convention makes the code sound. . . odd.

== Jamie ==

Neither of us have had voice training, so we probably both sound odd

== Piper ==

We use "self" for the instance variable.

self.position sounds so dissociated.

If we moved to "my" we would be able to write code that takes ownership of its own properties.

With `my.position += Up` the code reads a lot more like English.

```
class Player(Sprite):
    def on_update(my, event, signal):
        my.position += (0, 1)
```

# Autoload

== Jamie ==

So that's a bust. How about one from a friend.

I got asked if there's a way to do autoload in Python.
That is, a module gets loaded the first time its referenced.

== Piper ==

Oh, yes, "can we do what Ruby does?"

Honestly, it's a reasonable enough question.

== Jamie ==

Yeah, who hasn't been annoyed in a repl by "name Any is not defined".

So I can talk about it?

== Piper ==

That is why we're here.

== Jamie ==

So the goal is

```
from __auto__ import *

requests.get(...)
```

Which does several things:

1. Check that it's being invoked with the correct magic runes
2. Inspect the call stack for the calling module
3. Hook name handling by updating the namespace with a default dict
4. Profit!

== Piper ==

At least this isn't a proposal for a serious project.

So, do you want to discuss the tools or the complexity first?

== Jamie ==

[Defeated] ... tools

== Piper ==

So every static analysis tool will just throw their hands in the air and give up.
But I think you knew that, since you said it's good for repls.

The real problem is that I think this amount of pretzel making of Python invoked an eldritch being.
Which is what you'll need because you can't actually update the namespace of an existing frame.
It's read only.

== Jamie ==

[Think] Could I replace the calling module object in sys dot modules?

== Piper ==

Nope, doesn't touch the local scope.

== Jamie ==

So uplift the dunder builtins in the caller's scope?

== Piper ==

Same problem.

== Jamie ==

Add a module-level dunder-get-attr?

== Piper ==

Not called within the module, just by users of the module object

== Jamie ==

Hmph.

# CLI from Struct

== Piper ==

Here, I'll make it up to you.

Did you know you can stuff anything you want in an annotation?

== Jamie ==

Where are you going with this?

== Piper ==

Type annotations. You can put anything you want there.

And if you're using from-future-import they all count as strings.

You know what else is always strings?

== Jamie ==

YAML if you do it wrong enough?

== Piper ==

Yes. And command line arguments.

== Jamie ==

What, exactly, are you proposing?

== Piper ==

We've made it clear, we think Python's type system is pretty cool.

Type hinting is like, _the_ tool that made us reject most of these ideas.

One of the worst parts about the standard library argparse is that it returns an untyped bag called a namespace.

== Jamie ==

I think most people consider the fact that it's extremely fiddly to be the worst part.

== Piper ==

Okay, fair, but what if we fix both problems at once.

You start with a simple interface:

A function that takes a struct, and returns an instance of that struct.

Python 3.12 generics syntax makes the definition trivial:

    def parse_args[T](application_definition: type[T]) -> T:
        ...

Then you use a struct that is definitely going to have type information, like dataclasses.

    @dataclasses.dataclass
    class WordCounter:
        file: Path

So now, we give some file path, and parse args can set up an argument parser, parse the arguments, and return the type.

Tada, now it's easy to set up argparse.

== Jamie ==

That'd be great if it worked.

== Piper ==

Well, I wrote the first implementation while editing another talk.

It's only 9 lines of function, and type checkers like it!

== Jamie ==

It's my turn to have doubts, isn't it?

(Wait a beat)

Okay, what's the catch?

== Piper ==

It only handled positional parameters.

It only worked on types that already operated on strings, like Path or int.

Anything more complex than a bare type fails in _exciting_ ways.

== Jamie ==

I don't know whether I should ask what you mean by "more complex" or "exciting".

== Piper ==

Optional and Unions yell that they're not callable, but deep inside of argparse with an unfortunate call stack?

== Jamie ==

AKA "The most common kind of typing that isn't just a class name".

== Piper ==

So, yeah, this got me thinking about a bunch of the other things I expect a good parsing library to give me.

== Jamie ==

Is there a reason you didn't use one of the famous ones?

== Piper ==

Yeah, none of them ever clicked with me.

== Jamie ==

Okay, fair.

But none of this example is especially unique in how it uses annotations.

== Piper ==

Well, how do you add help text to this interface?

== Jamie ==

dictionary of strings in a class var?

== Piper ==

So, Annotated can support arbitrary annotations. Which can be more or less any valid value.
So if you plug a type and a string into a type hint, you can extract the annotation to feed into the help keyword of argparse.

And once you've already started using Annotated, you've got a path to basically all the important bits of argparse.

For example, all it took to get customizable actions was an enum of all the valid actions.

== Jamie ==

Okay, I'm convinced, this is possible.

There's got to be a downside.

== Piper ==

It's got enough edge cases to make a graph theorist cry.

[beat]

argparse's add_argument has a bunch of mutually exclusive signatures, so you need to be careful about which you set based on other rules.

And then you've got the "designing a good API" problem where there's stuff that's valid in argparse, but is almost definitely going to do the wrong thing.

And of course, when I was doing all this exploration, it was a lot of finding an edge, writing a test, fixing it, and a lot less designing.

== Jamie ==

Wait, you're still working on this?

== Piper ==

Oh, yeah, this turned out to work well enough that I published it!

I called it dykes.

Like slang for diagonal cutters: snipping the useful bits out of the command line arguments.

== Jamie ==

Possibly dykes: earthworks to divide your program from the sea of userland, or Excavacon '26

# Outro

== Jamie ==

We've been Jamie and Piper.

You can find us on socials and our personal websites.

I'm @astraluma@TacoBellLabs.net and my website is at qwertyuiop.ninja

== Piper ==

I'm @pathunstrom@ngmx.com on fedi, @pathunstrom.bsky.app on bluesky, and my personal website is piper.thunstrom.dev.


PursuedPyBear is at ppb.dev.

Dykes is on github at pathunstrom/dykes

== Jamie ==

Teahouse Hosting is teahouse.cafe and you can signup today at counter.teahouse.cafe/r/nbpy2026

We're both looking for work.

I do devops, backend, frontend sometimes, and misc python.

== Piper ==

I do product, data, full stack web, and misc python.
