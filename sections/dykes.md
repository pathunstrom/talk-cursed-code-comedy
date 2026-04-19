# CLI from Struct

== Piper ==

Here, I'll make it up to you.

Did you know you can stuff anything you want in an annotation?

== Jamie ==

Where are you going with this?

== Piper ==

Type annotations. You can put anything you want there.

And if you're using __future__ they all count as strings.

You know what else is always strings?

== Jamie ==

Text files? (Could be anything that is a bunch of text)

== Piper ==

Yes. And command line arguments.

== Jamie ==

What, exactly, are you proposing?

== Piper ==

We've made it clear, we think Python's type system is pretty cool.

Type hinting is like, _the_ tool that made us reject most of these ideas.

One of the worst parts about the standard library argparse is that it returns an untyped bag called a simple namespace.

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

[idea that isn't Annotated]

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

Oh, year, this turned out to work well enough that I published it!

I called it dykes.

Like slang for diagonal cutters: snipping the useful bits out of the command line arguments.

== Jamie ==

More appropriately, Excavacon 2026: earthworks to divide your program from the sea of userland.
