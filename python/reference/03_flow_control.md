# Flow Control

## While Loops

```
while boolean_test:
   do_the_thing()
```

## For Loops

```python
for friend in social_friends:
    print(friend)  # Prints each friend's name
```

* Note the use of `in` here.  This is analogous to the Java enhanced `for` loop:

```java
for (String friend: friends) {
    System.out.println(friend)
}
```

### The Else Keyword

If you want to run code only if a `while` or `for` loop completes successfully, use the `else` keyword like so:

```python
for line in file:
    if len(line.strip()) == 0:
        break
else:
    print("No empty lines found.")
```

## Error Handling

[Official Documentation for Built In Exceptions](https://docs.python.org/3/library/exceptions.html)

```python
raise ErrorType(message)
```

### Custom Errors

Pick an error to inherit from, or use the `Exception` class.

```python
class MyCustomError(Exception):
    pass

# or with additional info

class MyCustomErrorWithMoreInfo(BaseException):
    def __init__(self, message, code):
        super().__init__(message)
        self.code = code
```

### Try/Except

```python
try:
    # Do the thing
except ExceptionType:
    # Handle the error
except ADifferentException:
    # Handle a different exception
    raise # Reraise the error
else:
    # Runs only if no errors are raised
finally:
    # Always runs
```

