# Setting Up A Test Environment

This section covers:

* How to set up a unit testing environment in your project.
* How to set up SQL Server Express in a Docker container.

Automated unit testing is a vital part of building and deploying software in a DevOps environment.  Fortunately, Flask makes it simple to put together unit tests for API endpoints, and with a Docker container running SQL Server Express we can build test databases to validate our SQL queries against as well.  

In order to test our APIs, we need:

* A library of unit tests
* A test database

### Setting up the project

The suggested project layout looks like this:

```
-+project_root/
 +-------src/     #contains the actual api code
 |- .gitignore
 |- Dockerfile
 |- (other support files for building the container)
```

Now, we will add a directory called `test` to the `project_root` directory.  This is where our test code will live.

We will also need a couple of support files in the `project_root` directory.  One is a configuration file for our tests:

#### `pytest.ini`

```ini
[pytest]
env=
    DB_DRIVER=mssql+pyodbc
    GLOBAL_DB_HOST=localhost
    GLOBAL_DB_DATABASE=testdb
    DB_USERNAME=sa
    DB_PASSWORD=TeStDaTaB4s3!
    CORS_RESOURCES={{"/api/*": {{"origins": "http://localhost:4200"}}}}
```

These are environment variables which will be used to set up the test environment.  Your environment may (will probably) vary from this.

##### A note on the `CORS_RESOURCES` environment variable

`pytest` has an odd relationship with curly braces in the INI file.  In order to escape them, they need to be doubled up, hence the large number of curly braces in the JSON for the `CORS_RESOURCES` variable.  In the actual environment variable, this escaping is not necessary.

#### `runtests.bat`

```
@ECHO OFF
setlocal
set PYTHONPATH=src/
python -m pytest
endlocal
```

This file will be used to run unit tests from the command line.  If you're using PyCharm (or another Python aware IDE) this batch file may not be necessary.  What it is doing is specifying to Python that the `src/` directory should be used for importing modules into the unit test environment and then kicks off `pytest` to actually run the tests.

### Install `pytest`

Next, add these lines to your `requirements.txt` file:

```
pytest
pytest-env
```

* `pytest` is the testing tool we will be using in our API projects.
* `pytest-env` allows us to configure `pytest` via the `pytest.ini` file we created above.

Run `pip install -r requirements.txt` to install these dependencies.  

## Verify that your test environment is working

Finally, before we go on to setting up SQL Server Express, we should verify that our testing environment is working properly.  In the test environment, create this file:

#### `test_verify.py`

```python
def test_verify():
    assert True
```

* Files containing tests **must** be named `test_*.py` for `pytest` to autodetect them.
* Similarly, test methods must also be named `test_*` to be detected and ran.

From your project root directory, run this:

```
./runtests.bat
```

If all is well, you should get a response similar to this:

```
========================== test session starts ====================================
platform win32 -- Python 3.9.5, pytest-7.4.0, pluggy-1.2.0
rootdir: C:\proj\my_api
configfile: pytest.ini
plugins: env-0.8.2
collected 1 item                                                                                                                                
test\test_verify.py .                                                                                                                       [100%] 

===================== 1 passed in 1.03s ====================================
```

Once you have your test environment created, it's time to set up a test database.

## Setting up SQL Server Express in Docker

First, set up and run the container:

```
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=TeStDaTaB4s3!" -p 1433:1433 --name sql1 --hostname sql1 -d mcr.microsoft.com/mssql/server:2022-latest
```

* The environment variable `MSSQL_SA_PASSWORD` specifies the password you'll use to connect to this database.  The password in this example is secure enough for a local dev environment, but feel free to change it to suit yourself.  One important note:

  > The container will fail to run if the password doesn't meet Microsoft's security requirements.  It must have three of the following four characteristics:  capital letters, lowercase letters, numerals, special characters.

The next step here is to build out your test database(s).  Inside of your `test` directory, make a `sql` directory.  This is where you will keep your database/table/test data scripts.  We recommend the following:

* Keep a single DDL file to hold your test database creation SQL.
* The DDL file should drop your test database and recreate it fully.
* Only create the tables and records you actually need.
* If your tests require altering data (as opposed to only querying it), build a stored procedure called "reset_test_database" which truncates your test tables and reinserts the test data into them.
* Keep the docker command used to create the container in a file called `create_test_db_container.bat` in the `sql` directory.  This way, the code to create and populate the test database are in a known place when other developers need to work on the API later.
* You might not know what test records you will need at the start.  Be sure to keep your DDL file (and reset sproc if you're using one) in sync with your tests as you develop.  When you're done with your testing, be sure to run the DDL one last time and run the tests again to verify that they still work.

Once you have your database set up, we can start writing tests.
