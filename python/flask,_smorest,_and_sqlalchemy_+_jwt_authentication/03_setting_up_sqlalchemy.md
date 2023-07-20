# Setting up SQLALchemy

This section covers:

* Setting up `flask_sqlalchemy`.
* A generic `EngineManager` class for dynamically binding repositories to databases.
* Configuration of SQLAlchemy in the Flask `app`. 

This is a little involved, so this section has been split into several subsections.

### `db.py`

Create a `db.py` file inside your `api_name` directory.  It should contain the following code:

```python
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()
```

All of your modules which interact with the database will use this `EngineManager` object for their access.  The `db` variable is there so that `app.py` can configure it and then `engine_manager` can use it.

### `config.py`

```python
import os
import json
from sqlalchemy import URL

default_db_host = "localhost"
default_database = "DevDatabase"
default_db_driver = "sqlite"


class Config:
    def __init__(self):
        self.db_host = os.getenv("DB_HOST") or default_db_host
        self.db_database = (
            os.getenv("DB_DATABASE") or default_database
        )
        self.db_username = os.getenv("DB_USERNAME") or None
        self.db_password = os.getenv("DB_PASSWORD") or None
	self.db_driver = os.getenv("DB_DRIVER") or default_db_driver
        self.cors = os.getenv("CORS_RESOURCES") and json.loads(os.getenv("CORS_RESOURCES")) or {r"/api/*": {"origins": "http://localhost:4200"}}

    def get_url(self):
        return URL.create(
	    self.db_driver,
	    host=self.db_host,
	    database=self.db_database,
	    username=self.db_username,
	    password=self.db_password
        )
```

This class centralizes all the environment variables and configuration options used by the rest of the application.  It is here to isolate these configurations to a single point in case we need to change how we configure our API's in the future.

### `app.py`

Add SQLAlchemy setup code to `app.py`:

1. In the imports section, add this:

   ```python
   from my_api.db import db
   from my_api.config import Config
   ```

   We will be using the `Config` class to get values stored in environment variables.  The other imports have already been discussed.

2. At the top of the `create_app` function, add this:

   ```python
   	app = Flask(__name__)
       config = Config() # <--- Create the config object
   ```

   

3. Inside the `create_app` function, under the Flask Config section, add this:

   ```python
       # Setup SQLAlchemy
       app.config["SQLALCHEMY_DATABASE_URI"] = config.get_url()
       app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False
       db.init_app(app)
   ```

   

   | Config Option                    | Purpose                                                      |
   | -------------------------------- | ------------------------------------------------------------ |
   | `SQLALCHEMY_DATABASE_URI`        | Connection string for the project database |
   | `SQLALCHEMY_TRACK_MODIFICATIONS` | Turns off an unnecessary feature for performance reasons.    |

   These configuration options allows Flask to manage the database engine via the `flask-sqlalchemy` package.

4. At this point, remove the "hello world" route from the `app.py` file:

   ```python
   	# DELETE THIS SECTION
   	@app.route('/')
       def index():
           return "Flask is set up correctly!"
   ```

SQLAlchemy is now configured to talk to your database(s).
