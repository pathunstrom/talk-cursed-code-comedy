# Bags

== Piper ==

We're going to start with a softball.

Jamie, how do you solve semi-structured data?

== Jamie ==

Can we clarify "semi-structured data"?

== Piper ==

So... Something JSON shaped?

== Jamie ==

Oh! Like a REST API without a client!

So, if you use dictionaries, you get easy membership (dot-get, the in operator),
but data access is four punctuation instead of one. 

But, if you use classes (what I call "structs"), data access now requires one
punctuation but conditional access now involves get-attr and has-attr. Plus,
many struct libraries require you to define your schema before you can use it.

== Piper ==

Okay, so I repeat:

What's your solution?

== Jamie ==

So let shove all the data into a bag. Specifically a data bag: a data holder
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
