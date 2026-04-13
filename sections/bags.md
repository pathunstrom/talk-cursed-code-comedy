# Bags

== Jamie ==

Ok, so let's start easy. You're interacting with ad-hoc data--might be a rest API without a client. Might be a data set. Whatever.

If you use dictionaries, you get easy membership (dot-get, the in operator), but data access is four punctuation instead of one. If you use classes (what I call "structs"), data access now requires one punctuation but conditional access now involves get-attr and has-attr. Plus, many struct libraries require you to define your schema before you can use it.

So let shove all the data into a bag. Specifically a data bag: a data holder that acts like both a map and a struct.

This isn't new--it's such a good idea that JavaScript uses it exclusively. It isn't even new in Python--versions of this written with dunder methods have existed for a while.

But I have a cleverer idea:

```
class Bag(dict):
    def __init__(self):
        super().__init__()
        self.__dict__ = self
```

Just subclass dict and then use yourself as your own instance dictionary! No extra memory, no performance impact, just a floppy container of data.

== Piper ==

Well, I'll give you "If the implementation is easy to explain, it may be a good idea." And dang if that isn't a neat trick. Type hinting is going to be limited. 

