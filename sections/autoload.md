# Autoload

== Jamie ==

So that's a bust. How about one from a friend.

I got asked if there's a way to do autoload in Python.
That is, a module gets loaded the first time its referenced.

== Piper ==

[Extreme Skepticism]

== Jamie ==

Yeah, but who hasn't been annoyed in a repl by "name requests is not defined".

So the goal is

```
from __auto__ import *

requests.get(...)
```

Which does several things:

1. Check that it's being invoked with the magic runes
2. Inspect the call stack for the calling module
3. Hook name handling by updating the namespace with a default dict
4. Profit!

== Piper ==

Well, at least this isn't a proposal for a serious project.

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

[Think] Could I replace the calling module object?

== Piper ==

Nope, doesn't touch the execution context.

== Jamie ==

So uplift the builtins

== Piper ==

Same thing

== Jamie ==

Add a module-level dunder-get-attr?

== Piper ==

Not called within the module, just by users of the module object

== Jamie ==

Hmph.