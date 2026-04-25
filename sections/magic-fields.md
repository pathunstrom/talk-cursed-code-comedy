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
