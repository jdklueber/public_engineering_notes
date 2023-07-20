# Basic Syntax

### Operators

```python
13/3  # Returns     float: 4.33333333333333333333
13//3 # Returns       int: 4
13%3  # Returns remainder: 1
```

## User Input

The input function:

```python
your_name = input("Enter your name: ") # prints Enter your name: 
                                       # and waits for input
```

Remember to cast to a number if you want to do math on a user inputted number.

## Booleans

```python
literal_true = True
literal_false = False
```

* The usual comparison operators work as expected:  `==`,  `>`,  `<`,  `>=`,  `<=`
* `and` and `or` instead of `&&` and `||`
* `and` and `or` short circuits as expected

| Term   | Meaning                                                    |
| ------ | ---------------------------------------------------------- |
| Falsey | 0, "", [] all evaluate as False                            |
| Truthy | non 0, non-empty strings, non-empty lists evaluate as True |

You can use `or` to specify a default value:

```python
user_name = name || "foobar" # user_name will be foobar if name is Falsey
```

## 