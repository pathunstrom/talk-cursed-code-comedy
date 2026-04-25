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
