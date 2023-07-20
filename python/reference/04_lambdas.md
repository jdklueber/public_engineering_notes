# Lambdas

## Lambda Functions

```python
divide = lambda (x, y): x/y

#identical to
def divide(x, y):
    return x/y
```

## First Class Functions

Functions are first class variables.  So:

```python
def greeting:
    print("Hello")

hello = greeting
hello() # prints "Hello"
```

## 