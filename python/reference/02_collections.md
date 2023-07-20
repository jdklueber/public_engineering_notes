# Collections

## Lists

Operate as expected from other lanugages.

```python
friends = ["Alice", "Bob", "Carol"] # List literal
friends[0] # "Alice"
```

Lists are for homogeneous data that you expect to change over time and you want to be able to iterate over.

### Operations

```
list_var.append(element) # Adds to the end
list_var.remove(element) # Removes an object from a list
```

Additional [List Operations](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists)

## Tuples

[Documentation](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences)

**Immutable** data structure.  Accessed by index and iterable like a list.  

```python
friends = ("Alice", "Bob", "Carol") # Tuple literal
friends = "Alice", "Bob", "Carol"   # Also a tuple literal, but ambiguous
firends = "Alice",                  # ALSO A TUPLE, DON'T DO THIS
```

* Concatenating tuples generates a union of the tuples.
* Prefer tuples to lists unless you're indicating that the data can change.

## Sets

[Documentation](https://docs.python.org/3/tutorial/datastructures.html#sets)

Sets are collections which contain no duplicates and do NOT guarantee a stable sequence.  Iterable.  Use when you want to compare two collections to see what's in one and not in the other, or want to otherwise guarantee no duplication.

```python
social_friends = {"Alice", "Bob", "Carol", "Bob"} # Bob will only appear once
work_friends = {"Bob", "Dave", "Eve"}

social_but_not_work = social_friends.difference(work_friends)
# {'Carol', 'Alice'}
work_but_not_social = work_friends.difference(social_friends)
# {'Dave', 'Eve'}
not_in_both = social_friends.symmetric_difference(work_friends)
# {'Alice', 'Carol', 'Eve', 'Dave'} ## Bob is left out
not_in_both = work_friends.symmetric_difference(social_friends)
# Identical as prior call because symmetrical
in_both = work_friends.intersection(social_friends)
# {'Bob'}  ## and it doesn't matter which order you do the sets, also 
           ## symmetrical
```

## Dictionaries

[Documentation](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)

Dictionaries store key/value pairs and do not guarantee an ordering.  Use dictionaries when you need quick lookups.  Keys cannot be duplicated; attempts to duplicate will overwrite.

```python
ages = {"Alice": 26, "Bob": 28, "Carol": 20, "Dave": 35}
ages["Bob"] # 28

# You can turn a collection of tuples into a dictionary:
raw = [("Alice", 26), ("Bob", 28), ("Carol", 20), ("Dave", 35)]
ages = dict(raw)  # Same as with literal above
```



## Length and Sum

```python
grades = [80, 75, 90, 100]
total = sum(grades)
length = len(grades)
average = total/length
```


## Join

```python
comma_separated = ", ".join(social_friends)
# 'Carol, Alice, Bob'
# Notice that you're making a string of the separator and then calling
# join against that.
```

## List Slicing

Return a range out of a list, tuple, or string:

```python
friends = ["Alice", "Bob", "Carol", "Dave"]
friends[1:3] # ['Bob', 'Carol']
```

* Just like with the `range` function, a slice starts with the first number (inclusive) and proceeds to the last number (exclusive.)

You can leave some of the numbers out:

```python
friends[1:]  # from 1 to end
friends[:2]  # from start to 2 (exclusive)
friends[:]   # returns a new list of the contents of friend
```

* You can also use negative numbers, counting from the end with [-1] being the last element of the sequence (list, tuple...)

## Comprehensions

### List Comprehensions

These smell a little like map in other languages.

```python
numbers = [0, 1, 2, 3, 4]
doubled = [num * 2 for num in numbers] # Creates a new array
                                       # containing [0, 2, 4, 6, 8]
```

You can use any kind of iterable or generator for the in clause.

Remember that `string`s are iterable!

### With Conditionals

This starts to feel like a query.

```python
nums = range(10)
[num for num in nums if num % 2 == 1] # [1, 3, 5, 7, 9]
```

### Set Comprehensions

Work the same as list comprehensions, just use `{}` to make it a set.

### Dictionary Comprehensions

```python
# direct copy and paste from course
friends = ["Rolf", "Bob", "Jen", "Anne"]
time_since_seen = [3, 7, 15, 11]

long_timers = {
    friends[i]: time_since_seen[i]
    for i in range(len(friends))
    if time_since_seen[i] > 5
}
```

* Multiline is ok
* Note that line 6 is a `:` separated key/value pair
* The `for` statement is basically doing the classic for loop of indexing
* You can still put a conditional in the comprehension
* The comprehension is marked off with `{}` because that's how you make a `dict`.

### Zip

Easier way to make a `dict` out of two list/tuples.

```python
friends = ["Rolf", "Bob", "Jen", "Anne"]
time_since_seen = [3, 7, 15, 11]

# Remember how we can turn a list of lists or tuples into a dictionary?
# `zip(friends, time_since_seen)` returns something like [("Rolf", 3), ("Bob", 7)...]
# We then use `dict()` on that to get a dictionary.

friends_last_seen = dict(zip(friends, time_since_seen))
print(friends_last_seen)
```

* If you replace `dict` with `list`, you'll get a list of key/value tuples.
* You can also do a list of 3+ element tuples if you zipped 3+ lists/tuples.
* `zip` will ignore elements past the length of the smallest list/tuple passed in.

### Enumerate

```python
for index, friend in enumerate(friends):
    print (index + ": " + friend) # prints the index and the value at that index
```

* Not that `index, friend` is just using tuple destructuring syntax.

