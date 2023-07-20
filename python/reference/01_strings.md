# Strings

* double quotes and single quotes are interchangeable for string literals
* Backslash escaping works
* String concat with + 
* `str()` to convert to string

### Multi Line Strings:

```python
"""
This is a multi line string
"""
```

### Formatted strings

```python
name = "Jason"
greeting = f"Hello, {name}" #prints Hello, Jason
```

The formatting happens **AT THE TIME IT'S PROCESSED** and is not dynamic:

```python
name = "Jason"
greeting = f"Hello, {name}" #prints Hello, Jason
name = "Bob"
print(greeting) # Prints Hello, Jason
```

You can also do a templated string like this:

```python
greeting = "Hello, {}" # Notice the lack of an f or a variable name.
print(greeting.format("Jason")) #prints Hello, Jason
print(greeting.format("Bob"))   #prints Hello, Bob

both_of_us = "Meet {} and {}"
print(both_of_us.format("Jason", "Bob")) #prints Meet Jason and Bob
```

For the templated string you CAN use named variables but you have to call it a little differently: 

```python
greeting = "Hello, {name}" # This is just a bare string with {name} 
                           # in it as a literal
print(greeting.format(name="Jason")) #you have to use kwargs for the
                                     #interpolation to work
```

Note that f strings also work with multiline strings.

f strings also require no type conversion for numbers.

Multiplying a string by an integer creates integer copies of the string catted together.

```
age = "3"
months = age * 12 # 333333333333
# 12 string 3's in a row
```

