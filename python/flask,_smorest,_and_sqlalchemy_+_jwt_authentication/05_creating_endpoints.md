# Creating Endpoints

This section covers:

* How to use `Blueprint` objects.
* How to describe endpoint routes.
* How to write endpoints using the following http verbs:
  * GET
  * PUT
  * POST
* Use of path parameters.
* Use of requests with JSON data in the body.
* Use of query parameters.
* Registering endpoints with the API.
* CORS



Now that we have our endpoints and schemas designed, it's time to write our endpoints.  Create a subfolder of your `api_name` directory called `resources` and place an `__init__.py` file there to mark it as a module.  We will use one file per resource we're exposing, named after the resource.  So, for the export API it looks like this:  `api_name/resources/export.py`.

Here is a sample of an resources file.  We will look at it in segments:

### Imports

```python
from flask.views import MethodView
from flask_smorest import Blueprint, abort
from export_dashboard_api.schemas import ExportSummarySchema, ClientNameSchema
from export_dashboard_api.repository import ExportRepository
from export_dashboard_api.db import engine_manager
```

In this section, we're importing all of the relevant variable, classes, and functions we need for the endpoints.  

* `flask.views` and `flask.smorest` contain the framework code we will be using.
* The `schemas` classes describe the inputs and responses to be used by our endpoints.
* The `repository` class talks to the database.
* The `engine_manager` class is used by the `repository` to bind queries to the correct database.

### The Initial Setup

```python
repo = ExportRepository(engine_manager)
blp = Blueprint("Summary", __name__, description="Client View")
api_prefix = "/api"
```

* The `repo` variable is used by the endpoints to communicate with the database.
* The `blp` variable will be imported by `app.py` and handed off to Flask to service.  It is also used to generate documentation.
* The `api_prefix` variable is used by the routes to centralize the root of the endpoint path.

### Routes

Here is one route class:

```python
@blp.route(f"{api_prefix}/clients")
class Clients(MethodView):
    @blp.response(200, ClientNameSchema(many=True))
    def get(self):
        try:
            return repo.get_client_names()
        except Exception:
            traceback.print_exc()
            abort(500, "Internal Server Error")
```

| Line                                                         | Purpose                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `@blp.route(f"{api_prefix}/clients")`                        | This specifies the route being serviced by this class.       |
| `class Clients(MethodView):`                                 | The class name should be relevant to the resource being operated on by the endpoint.  These endpoint classes inherit from `MethodView`, which allows us to shorthand our endpoints by using their http verb as their method name. |
| `@blp.response(200, ClientNameSchema(many=True))`            | This specifies that a successful action will return an http 200 and data shaped by the `ClientNameSchema`.  `many=True` hints that the response should be in a json list form. |
| `return repo.get_client_names()`                             | Asks the repository to provide a list of client names for its response. |
| `except Exception:`<br/>            `abort(500, "Internal Server Error")` | In the event of a database or other error, aborts processing and returns a generic error message to the client. |

### Endpoints With Body Parameters

If writing an endpoint which needs to accept data in the body (a POST or PUT endpoint), do the following:

1. Declare the input data as a schema.

2. Use the `arguments` decorator to modify the handling method:

   ```python
   @blp.arguments(IncomingSchemaName)
   ```

3. Add a variable to catch the incoming body data into the method definition:

   ``` python
   def post(self, incoming_data)
   ```

### Endpoints With Path Variables

As mentioned in the section on endpoint design, we will need path variables to identify specific resources.  To get access to path variables, use this syntax:

```python  
@blp.route(f"{api_prefix}/client/<id>")
```

The `<id>` part indicates that this is a path variable, and it will be passed into the handling function with that name.  The function signature then looks like this:

```python
def get(self, id):
```

Multiple path variables work the same way.  The order in which the variables appear in the function definition is irrelevant as they are passed in via keyword arguments.

If combining path variables with body data, the method will look like this:

```python
@blp.route("/<pet_id>")
class PetsById(MethodView):
    @blp.arguments(PetSchema)
    @blp.response(200, PetSchema)
    def put(self, update_data, pet_id):
		#... handling code here
```

* The route contains the path variable as usual.
* There is an `arguments` decorator for the incoming data.
* The incoming data is passed in as the first argument to the handling method, with the path variables being tacked onto the end.

### Endpoints with Query Strings

This portion of the document has not been tested yet, as we do not have any API's currently relying on query strings.  That said, according to [the documentation](https://flask-smorest.readthedocs.io/en/latest/arguments.html), this is how it works.

1. Declare the arguments expected as a schema.

2. Associate the schema with the handling method via this decorator:

   ```python
    @blp.arguments(QueryArgsSchema, location="query")
   ```

3. Add `args` to the method definition:

   ```python
   def get(self, args):
   ```

You should then be able to access the items in the query string using args as a dictionary.  For example, to access a query string argument like this: `?start_dt=2023-01-01`, you would use syntax like this:

```python
args["start_dt"]
```

If you need to combine query inputs with path variables or incoming body data, order matters!

```python
@blp.route("/pet/<id>")
class Pets(MethodView):
    @blp.arguments(PetSchema)
    @blp.arguments(QueryArgsSchema, location="query")
    @blp.response(201, PetSchema)
    def post(self, pet_data, query_args, id):
        # ... handling code here

```

* Incoming body data gets passed in first, because it was the first `arguments` decorator mentioned.
* The query parameters are passed in second, because of the ordering of the `arguments` decorators.
* Path parameters are handled last, because they are non-positional.

### Registering the Endpoint

Once your endpoint is written, you need to register it with `flask_smorest`.  

#### `app.py` - imports section

```python
from flask_smorest import Api
from api_name.resources.endpoint_file import (
    blp as endpoint_blueprint,
)
```

Substitute your own module/endpoint/file names as necessary for the second import.

#### `app.py` - config section

After the SQLAlchemy configuration block, add this:

```python
    api = Api(app)

    api.register_blueprint(endpoint_blueprint)
```

If you have multiple endpoint files, you will need multiple calls to `register_blueprint` to register them.

## CORS

You will probably need to have CORS configured for your server.  Best practice is to import this configuration from an environment variable, which we've already done in `config.py`.  Your `app.py` file, with CORS configuration and using `config.py` for collecting environment variables, now should look like this:

```python
from flask import Flask
from flask_smorest import Api
from flask_cors import CORS
from my_api.db import db, engine_manager
from my_api.config import Config

from my_api.resources.client import (
    blp as client_blueprint,
)


def create_app():
    app = Flask(__name__)
    config = Config()

    CORS(app, resources=config.cors)  # <--- CORS config

    app.config["PROPAGATE_EXCEPTIONS"] = True
    app.config["API_TITLE"] = "My API"
    app.config["API_VERSION"] = "v1"
    app.config["OPENAPI_VERSION"] = "3.0.3"
    app.config["OPENAPI_URL_PREFIX"] = "/"
    app.config["OPENAPI_SWAGGER_UI_PATH"] = "/swagger-ui"
    app.config[
        "OPENAPI_SWAGGER_UI_URL"
    ] = "https://cdn.jsdelivr.net/npm/swagger-ui-dist/"

    # Setup SQLAlchemy
    engine_manager.initialize(db, config)

    app.config["SQLALCHEMY_DATABASE_URI"] = config.database_uri
    app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False
    db.init_app(app)

    api = Api(app)

    api.register_blueprint(client_blueprint)

    return app
