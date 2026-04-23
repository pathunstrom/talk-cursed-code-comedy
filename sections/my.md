# Self -> my

== Jamie ==

We mentioned a game engine, PursuedPyBear. It's made for ease of learning and ease of use.
Based on how sprints and talks have gone, we think we've largely succeeded

== Piper ==

But we're always looking for ways to improve. In particular, with very newbies.

We want to help people getting into Python for game development to stop thinking of themselves in such dissociated terms, so we discussed moving from self to my, which would help them take ownership of their code.

```
class Player(Sprite):
    def on_update(my, event, signal):
        my.position += (0, 1)
```
