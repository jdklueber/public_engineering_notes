# Classes

## Magic Methods

A small selection of dunder methods.

| Method                     | Usage                                                        |
| -------------------------- | ------------------------------------------------------------ |
| `__init__(self)`           | constructor                                                  |
| `__class__`                | class name                                                   |
| `__len__(self)`            | Called when `len()` is called against the object             |
| `__getitem__(self, index)` | Called with an object is indexed:  `object[index]`           |
| `__repr__(self)`           | Should output a string representing the object ie, how to build it.  Is directed at programmers. |
| `__str__(self)`            | Should output a string that is directed at users.            |

* Define `__len__` and `__getitem~~` to make a class iterable.

## Inheritance

Works the way you'd expect it to based on other languages.

Syntax:

```python
def Student:
    pass

def WorkingStudent(Student):
    super().__init__()
    pass

# Working Student inherits from Student
```

## Properties and the `@property` decorator

Marking a straight up getter method that doesn't require any arguments with the `@property` decorator lets you access that method without `()`:

```python
class Foo:
    @property
    def spam(self):
        return 23
    
foo = Foo()
foo.spam # returns 23

## For a property setter:
     @spam.setter
     def spam(self, value):
            self.spam = value
```

## `@classmethod` and `@staticmethod`

Methods decorated with `@classmethod` take `cls` as their first argument (instead of `self`).

Methods decorated with `@staticmethod` do not get `cls` or `self` for their first argument.

Factory method example:

```python
def Foo:
    #...
    @classmethod
    def factory(this, that):
        return cls(this, that)
```

## Abstract Base Class

[Documentation](https://docs.python.org/3/library/abc.html)

Use to make abstract classes.  Can also be used as an interface. 

## Multiple Inheritance

Just make your inheritance into a tuple with commas.  Especially good with ABC's.
