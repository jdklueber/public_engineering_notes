# Endpoint Design

This section covers:

* Selecting an HTTP verb for your endpoint:
  * POST
  * GET
  * PUT
  * DELETE
* Defining schemas for your expected inputs and responses.

Before we can move on to creating the actual endpoints, we need to think about the messages we will be sending to and receiving from them.  We describe these inputs and outputs by defining schemas using the `marshmallow` package.  These schemas are an important part of the automatic swagger documentation that our APIs use.

### Defining Endpoints

RESTful APIs make full use of HTTP verbs as a part of their design.  The verbs we will make use of are these:

| Verb   | Usage                |
| ------ | -------------------- |
| POST   | Creating resources   |
| GET    | Retrieving resources |
| PUT    | Updating resources   |
| DELETE | Deleting resources   |

Together, these verbs cover the CRUD operations most APIs will be needing to do.

To make our endpoints work smoothly, we will need a combination of the right verbs, the right nouns, path variables, query strings, and json bodies.  Here's a rundown of how we should think about these endpoints, organized by verb.

### POST endpoints

POST endpoints are intended for resource creation only, as they are the only verb which is explicitly not idempotent.  Idempotent actions can be performed multiple times and have the same results.  Nonidempotent actions possibly (but not necessarily) create different results each time they are run.  So, if you run a POST request to create a new widget for your store inventory more than one time, you will get more than one created widget (probably duplicated except for an id!)

Generally speaking, the convention for POST endpoint naming looks like this:

```
https://host:port/api/resource_name
```

So, if you were creating items for a specific store's catalog, the endpoint might look like this:

```
https://host:port/api/store/1/items
                     ^^^^^^^^ specifying the store number to be affected
                              ^^^^^ specifying that you are working with the items in that store's inventory
```

POST requests also contain json data in their body.  For the above, the request might look like this:

```
POST https://host:port/api/store/1/items
Content-Type: application/json

{
    name: "Basketball",
    price: 19.99
}
```

POST requests generally send data back to the client on success.  The response will be an HTTP code 201 (Created), along with the entity created:

```
{
    "id": 20391,
    "store_id": 1
	"name": "Basketball",
	"price": 19.99
}
```

### GET endpoints

GET endpoints are for retrieving resources (in our case, from a database, but that's not universal.)  They come in several flavors:

| Purpose                | Style                                                        | Notes                                                        |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Get a single resource  | `https://host:port/api/resource_name/1234`                   | In this case, "1234" is the id of the resource being requested. |
| Get many resources     | `https://host:port/api/resource_name`                        | Always end with the resource's name for retrieve many operations.  In this case, we would expect to retrieve all of the resources. |
| Get many with a filter | `https://host:port/api/resource_name?start_date=2022-01-01&end_date=2022-12-31` | If you want to send query filters in, use a query string.  In this case, we would expect to get all resources with a date in 2022. |

When resources are associated with another resource (like items in a store), use logical path names to describe the relationship from general to specific.  For example, if you wanted to get all sales for a specific store in 2022, the endpoint might look like this:

```
https://host:port/api/store/12/sales?year=2022
```

This is requesting the sales for store 12 in 2022.  If you wanted all sales ever, you'd leave off the query string.  If you wanted a specific sales record, you would add to the path like this:

```
https://host:port/api/store/12/sales/12345
```

This would request sale #12345 from store 12.

### PUT endpoints

PUT endpoints are for doing updates to existing data.  Generally you will use a path that specifies the resource you're updating in the "get a single resource" style from the section on GET endpoints.  Carrying on from the example at the end of that section, if we wanted to update the name of the store, we would do something like this:

```
PUT https://host:port/api/store/12
Content-Type: application/json

{
	"name": "New Store Name"
}
```

The expected response is an http code 200 along with body showing the new state of the resource.  This is very similar to how POST endpoints work, but with the exception that running this PUT request multiple times will have no real impact on the data.  The update is the update; it doesn't care what the prior state was.

### DELETE endpoints

We will probably never use DELETE endpoints, but for completeness's sake they use the same pattern as PUT does, but without the body being sent.  The response should be an http code 200 along with the state of the resource before it was deleted.  If sent multiple times, an http code 204 is more appropriate as it does not imply that there's any data in the body of the response.  Multiple DELETE requests will not harm the system as the final result is the same:  the resource at the given path no longer exists.

### Creating Schemas

Now that you have an idea of what your endpoints will look like, we can start describing their inputs and outputs.  Create a file in your `api_name` directory called `schemas.py`.   Here's a sample schema file:

```python
from marshmallow import Schema, fields


class CustomerSchema(Schema):
    customer_id = fields.Integer(dump_only=True)
    customer_name = fields.Str(dump_only=True)

```

* We import `Schema` and `fields` from `marshmallow`. 
* We create a subclass of `Schema` for each schema item we create.
* We create a schema class for each input or output message we expect to send.  In many cases, it's safe to use the same class for both input and output (think POST and PUT operations here.)  
* `dump_only=True` indicates that a given field is only for output.  In the above schema, the API only does GET requests, so all fields are `dump_only`.  ID fields are a perfect candidate for `dump_only=True` as they should never be passed in from a client.  In a POST request, the ID isn't known, and for a PUT request, the ID should be in the URL path.
* `load_only=True` (not represented in this example) is for fields which should only be passed in and NEVER passed out.  This is generally used for security relevant data like passwords in a login or password update endpoint.
* [Documentation on marshmallow data types is here.](https://marshmallow.readthedocs.io/en/stable/marshmallow.fields.html)
* [Full documentation for marshmallow is here.](https://marshmallow.readthedocs.io/en/stable/)

