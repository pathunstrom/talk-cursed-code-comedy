# CLI from Struct

(I wish I could fit the excavacon reference here, but it's not working)

== Jamie ==

So argparse is largely reasonable and usable.
But as we've mentioned a few* times, we care about static analysis.
And the major data structure in argparse is a mediocre, half-implemented bag, with no options for typing.
And setting it up can be... fiddly.

== Piper ==

You know... you can put _anything_ in an Annotated.

== Jamie ==

I suspect this non-sequitor means you have an idea.

== Piper ==

You are correct!
I mean, I already had a shitpost.
But I bet I can make it better.
Since the alternatives never really clicked for me.

So we've already discussed structs--whether you use attrs or pydantic or dataclasses.
You can keep a lot of secondary information in one of those.
So if you defined a schema for that information, and you mapped that schema to argparse, you'd have a CLI library.

== Jamie ==

Ok, I see where you're going with that.

It's type hint friendly.
And this implementation is no less reasonable than anything else we've discussed.

What aren't you saying?

== Piper ==

Well, full type introspection is annoying complicated.
It turns out that argparse and CLI parsing are both made of edge cases.

== Jamie ==

You wrote it, didn't you.

== Piper ==

And published!
