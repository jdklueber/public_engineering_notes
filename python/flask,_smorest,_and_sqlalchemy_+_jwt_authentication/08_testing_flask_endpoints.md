# Testing Flask Endpoints With Test Fixtures 

This section covers:

* Creating a test suite with proper naming conventions.
* Writing tests using the AAA pattern.
* How to run unit tests.
* How to write parameterized unit tests.
* A case study on how to test Flask endpoints with the mock client.

## Fixtures

Quite often, we end up repeating code in a test suite because testing the same behavior different ways can require the same (or very similar) arrange steps.  Let's take a look at the `export_dashboard_api` for a case study.  In this test suite, we are validating the endpoints of our API.  To this end, we need a mock client to talk to the app (so we don't need to automate a browser.)  Flask comes with this capability built in.

```python
import pytest
from my_api.app import create_app

@pytest.fixture
def app():
    return create_app()


@pytest.fixture
def client(app):
    return app.test_client()
```

* We imported `pytest` to make use of its decorators.
* The `@pytest.fixture` decorator is used to mark factory methods which produce needed resources for other tests.  
* Each fixture runs once before each test.
* Fixtures are named for the resource they provide
* The `client` fixture requires an instance of `app` to run.
  * The parameter name `app` instructs `pytest` to look for a fixture named `app` and run it first.
  * The result from the `app` fixture gets passed in as an argument to the `client` fixture.

## Using Fixtures

Making use of these fixtures in our tests works exactly the same way:

```python
def test_get_clients(client):
    endpoint = "/api/clients"
    expected = [
        {"client_name": "Client One"},
        {"client_name": "Not A Real Client"},
        {"client_name": "TEST"},
    ]

    response = client.get(endpoint)
    assert response.json == expected
```

* Our test has a parameter named `client` which pulls from the `client` fixture.
* The `endpoint` variable indicates the endpoint under test.
* The `expected` variable, as before, indicates what we expect to get back from the endpoint.
* `client.get(endpoint)` runs the mock client against our API and passes the result into the `response` variable.
* We assert our expectation as usual.

For more about testing Flask endpoints, [see the official documentation](https://flask.palletsprojects.com/en/2.3.x/testing/).

For more about test fixtures, [see the official documentation](https://docs.pytest.org/en/6.2.x/fixture.html).
