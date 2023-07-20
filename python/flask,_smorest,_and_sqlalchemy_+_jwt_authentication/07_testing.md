# Testing

This section covers:

* Creating a test suite with proper naming conventions.
* Writing tests using the AAA pattern.
* How to run unit tests.
* How to write parameterized unit tests.

# Creating a Test Suite

Each class or logical unit of program that you test should have its own test suite dedicated to it.  Test suites are simply files full of tests named such that `pytest` can autodetect them.  Best practice is to use a descriptive name like this:

```
test_login.py
^^^^---------- required for autodetection
     ^^^^^---- describes the class/unit of work under test
```

The `test_` part is the only bit that's **required** to be present, but having a logically chosen name to follow it both helps in finding the right spot to update/add new tests and in hunting down errors when tests fail.  

## Writing a Test

Inside of a test suite, any function following the `test_thing` naming convention will be ran and tracked as a test.  Inside of those methods we follow this pattern:

1. Arrange
2. Act
3. Assert

This is the "AAA" testing method.  Here is what those steps involve.

| Step    | Purpose                                       |
| ------- | --------------------------------------------- |
| Arrange | Set up test conditions and expected outcomes. |
| Act     | Run code under test.                          |
| Assert  | Validate the results of the Act step.         |

Let's look at a hypothetical example.

## Example Code

In our online shop, we want to run a sale where any purchase over $25.00 gets a 10% discount and any purchase over $50.00 gets a 20% discount.  Here is the code we wrote to support it.

```python
def calculate_discount(price: float):
    if price > 25.00 and price < 50.00:
        return .10
    elif price > 50:
        return .20
    else:
        return 0
    
```

Before we write tests (and before we write code), we should have an idea of the expected outcomes.  For a function like this with multiple `if` statements creating branches we should test the areas around those branches to make sure they're routing code correctly.  Here are the conditions we want to test:

| Price | Expected Outcome |
| ----- | ---------------- |
| 24.99 | 0                |
| 25.00 | .10              |
| 25.01 | .10              |
| 49.99 | .10              |
| 50.00 | .20              |
| 50.01 | .20              |

In order to test these, each of these expected conditions and outcomes will get their own test.  (There are tools and techniques to cover all of these in a single test, parameterized, but for now we will stick with the basics.)  Let's break down the first test in `test_discount_calculator.py`:

```python
from discount_calculator import calculate_discount

def test_calculate_discount_2499():
    input = 24.99
    expected = 0.00
    actual = calculate_discount(input)
    assert expected == actual
```

* We're importing from `discount_calculator` because test code is written in a different file than the code under test.
* We declare our input and expected output as variables.  This convention makes it easier to read and understand tests, as well as to copy and modify them.
* We run the code under test and store the result in a variable called `actual`. 
* We use the `assert` built-in function to validate our results.

When this runs, one of two things will happen:

* The test passes, in which case we cheer and move to the next test.
* The test fails, in which case we need to review the output and see whether the problem is with the test itself or the code being tested.

To run the test, use your IDE's test running feature (in PyCharm, right click your test directory and choose "Run Python tests in test...") or the `runtests.bat` file from the command line.  When we do this, we get the following result:

```
============================= test session starts =============================
collecting ... collected 1 item

test_discount_calculator.py::test_calculate_discount_2499 PASSED         [100%]

============================== 1 passed in 0.02s ==============================
```

This test passes, so we move on to the next test.

## A Failing Test

Here's the next test:

```python
def test_calculate_discount_2500():
    input = 25.00
    expected = 0.10
    actual = calculate_discount(input)
    assert expected == actual
```

Rerunning the tests, we get this result:

```
============================= test session starts =============================
collecting ... collected 2 items

test_discount_calculator.py::test_calculate_discount_2499 PASSED         [ 50%]
test_discount_calculator.py::test_calculate_discount_2500 FAILED         [100%]
test_discount_calculator.py:8 (test_calculate_discount_2500)
0.1 != 0

Expected :0
Actual   :0.1
<Click to see difference>

def test_calculate_discount_2500():
        input = 25.00
        expected = 0.10
        actual = calculate_discount(input)
>       assert expected == actual
E       assert 0.1 == 0

test_discount_calculator.py:13: AssertionError


========================= 1 failed, 1 passed in 0.07s =========================

Process finished with exit code 1
```

Our $20.00 test failed.  `pytest` gives us a lot of good feedback here.  First, we see in the summary section a list of passing and failing tests along with the assertion that failed.

```
test_discount_calculator.py::test_calculate_discount_2500 FAILED         [100%]
test_discount_calculator.py:8 (test_calculate_discount_2500)
0.1 != 0
```

Because we named our test file and method well, we can see that it's the 25.00 test failing, and that we expected 10% and got 0.  In the details section, we can see the code being tested and the line that failed marked off with the value of the variables underneath:

```
def test_calculate_discount_2500():
        input = 25.00
        expected = 0.10
        actual = calculate_discount(input)
>       assert expected == actual
E       assert 0.1 == 0
```

The test code doesn't look like it's incorrect- the input and expectation are correct, and the call/assertion are identical to our prior test so the problem is probably in the `calculate_discount` function.  Let's take a closer look:

```python
def calculate_discount(price: float):
    if price >= 25.00 and price < 50.00:
        return .10
    elif price >= 50:
        return .20
    else:
        return 0
```

We used `>` instead of `>=`.  Twice.  So, we can fix those bugs quickly enough and rerun the tests.

```
============================= test session starts =============================
collecting ... collected 2 items

test_discount_calculator.py::test_calculate_discount_2499 PASSED         [ 50%]
test_discount_calculator.py::test_calculate_discount_2500 PASSED         [100%]

============================== 2 passed in 0.01s ==============================
```

Victorious, we can go ahead and write the unit tests for the other conditions above.  

## Testing Multiple Parameters

In the prior section we deferred learning about how to write a single test which exercises code against multiple scenarios.  Here, then, is how to do that.

```python
from discount_calculator import calculate_discount
import pytest

@pytest.mark.parametrize("input,expected",
                         [
                             (24.99, 0),
                             (25.00, .10),
                             (25.01, .10),
                             (49.99, .10),
                             (50.00, .20),
                             (50.01, .20)
                         ])
def test_calculate_discount(input, expected):
    actual = calculate_discount(input)
    assert actual == expected
```

* We import `pytest` directly so that we can use its decorators to set up a parameterized test.
* We mark our test method with the `@pytest.mark.parameterize` decorator.
  * The first argument is a string listing the parameter names used by the function to catch the values being passed in.
  * The second argument is a list of tuples matching the parameters in the first argument.  In this case, because the first argument was "input,expected", the first tuple will be passed into `test_calculate_discount` as `input=24.99, expected=0`.
* The `test_calculate_discount` test now takes those parameters as arguments.
* The act and assert steps do not change in this case.  Parameterization is handling the arrange step for us.

Running this test yields the following result:

```
============================= test session starts =============================
collecting ... collected 6 items

test_discount_calculator.py::test_calculate_discount[24.99-0] PASSED     [ 16%]
test_discount_calculator.py::test_calculate_discount[25.0-0.1] PASSED    [ 33%]
test_discount_calculator.py::test_calculate_discount[25.01-0.1] PASSED   [ 50%]
test_discount_calculator.py::test_calculate_discount[49.99-0.1] PASSED   [ 66%]
test_discount_calculator.py::test_calculate_discount[50.0-0.2] PASSED    [ 83%]
test_discount_calculator.py::test_calculate_discount[50.01-0.2] PASSED   [100%]

============================== 6 passed in 0.02s ==============================

Process finished with exit code 0
```

* Each parameterized run shows the parameters used in `[]`.
* Failed tests throw off the formatting somewhat, but still call out the specific failure in the same way as before.  Deliberately failing the first test gave us the following result:

```
============================= test session starts =============================
collecting ... collected 6 items

test_discount_calculator.py::test_calculate_discount[24.99-10] 
test_discount_calculator.py::test_calculate_discount[25.0-0.1] 
test_discount_calculator.py::test_calculate_discount[25.01-0.1] 
test_discount_calculator.py::test_calculate_discount[49.99-0.1] 
test_discount_calculator.py::test_calculate_discount[50.0-0.2] 
test_discount_calculator.py::test_calculate_discount[50.01-0.2] 

========================= 1 failed, 5 passed in 0.08s =========================
FAILED    [ 16%]
test_discount_calculator.py:3 (test_calculate_discount[24.99-10])
0 != 10

Expected :10
Actual   :0
<Click to see difference>

input = 24.99, expected = 10

    @pytest.mark.parametrize("input,expected",
                             [
                                 (24.99, 10),
                                 (25.00, .10),
                                 (25.01, .10),
                                 (49.99, .10),
                                 (50.00, .20),
                                 (50.01, .20)
                             ])
    def test_calculate_discount(input, expected):
        actual = calculate_discount(input)
>       assert actual == expected
E       assert 0 == 10

test_discount_calculator.py:15: AssertionError
PASSED    [ 33%]PASSED   [ 50%]PASSED   [ 66%]PASSED    [ 83%]PASSED   [100%]
Process finished with exit code 1
```

We can see the error on test 1 plainly, even though the `PASSED` results aren't showing next to their relevant tests.  

