# SQL Alchemy - Raw Notes
*Note:  This file will eventually disappear and be replaced by a folder full of smaller files organized by topic.  This file represents my summation of the comprehensive tutorial found at https://docs.sqlalchemy.org/en/20/tutorial/index.html*
## Architecture

* Two APIs:  Core and ORM
  * Core is the database toolkit
  * ORM is, obviously enough, the ORM
  * ORM utilizes the Core, but automates it- knowing Core is useful for understanding the ORM but isn't directly used much when coding to it

## ORM Overview

* Provides a configuration layer that maps Python classes to database tables/constructs 
* Object persistence mechanism is the Session
* Extends Core SQL Expression Language to allow queries to be composes and invoked in terms of user defined objects

## Establishing Connectivity - the Engine

### Creating the `Engine`

```python
from sqlalchemy import create_engine
engine = create_engine("sqlite+pysqlite:///:memory:", echo=True)
```

`create_engine`'s primary argument is the URL string.  It contains:

* The type of database we are working with (`sqlite`)
* The `DBAPI` (driver) we are using (`pysqlite`)
* Where the database is (`/:memory:`, which is `sqlite3` specific)

It appears that this is the general form, but I haven't confirmed this with documentation yet:

```python
"database_type+driver://location"
```

* The `DBAPI` can be omitted and SQLAlchemy will use a default driver.

Creating the `Engine` does not connect to the database!  That will happen only when something is actually being invoked requiring that connection.  This is the "lazy initialization" pattern.

The `echo=True` `kwarg` tells the `Engine` to log its SQL to a Python logger that writes to `stdout`. 

## Working with Transactions and the DBAPI

In lieu of learning the SQLAlchemy Expression Language immediately, this section relies on the `text` function which lets us run hand written SQL.

### Getting a Connection

```python
from sqlalchemy import create_engine, text

engine = create_engine("sqlite+pysqlite:///:memory:", echo=True)

with engine.connect() as conn:
    result = conn.execute(text("select 'hello world'"))
    print(result.all())
```

Use the `with` (context management) block with connections whenever possible because they represent an open resource against the database.  This block results in the following output:

```
2023-06-30 13:49:02,992 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2023-06-30 13:49:02,993 INFO sqlalchemy.engine.Engine select 'hello world'
2023-06-30 13:49:02,993 INFO sqlalchemy.engine.Engine [generated in 0.00074s] ()
[('hello world',)]
2023-06-30 13:49:02,993 INFO sqlalchemy.engine.Engine ROLLBACK
```

* The `DBAPI` assumes that transactions are always in progress, note the `BEGIN` statement on the first line.
* The data came back as a `Result` object which was represented as a `List` of `Tuple`s .
* A `ROLLBACK` was issued when the connection dropped since the transaction was not committed.
* Committing a transaction is done with `conn.commit()`
* Autocommit is possible, but not recommended under most circumstances.
* It is best practice to consume the `Result` object inside the connection context and not pass it outside that scope.

Here is an example of a table creation and commit:

```python
with engine.connect() as conn:
     conn.execute(text("CREATE TABLE some_table (x int, y int)"))
     conn.execute(
         text("INSERT INTO some_table (x, y) VALUES (:x, :y)"),
         [{"x": 1, "y": 1}, {"x": 2, "y": 4}],
     )
     conn.commit()
```

Resulting in the following output:

```
2023-06-30 13:55:15,229 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2023-06-30 13:55:15,230 INFO sqlalchemy.engine.Engine CREATE TABLE some_table (x int, y int)
2023-06-30 13:55:15,230 INFO sqlalchemy.engine.Engine [generated in 0.00065s] ()
<sqlalchemy.engine.cursor.CursorResult object at 0x0000014CC6CB4460>
2023-06-30 13:55:15,231 INFO sqlalchemy.engine.Engine INSERT INTO some_table (x, y) VALUES (?, ?)
2023-06-30 13:55:15,231 INFO sqlalchemy.engine.Engine [generated in 0.00030s] [(1, 1), (2, 4)]
<sqlalchemy.engine.cursor.CursorResult object at 0x0000014CC6CB44C0>
2023-06-30 13:55:15,231 INFO sqlalchemy.engine.Engine COMMIT
```

* Note that without asking for print statements, the logger printed the `repr` for two `CursorResult` objects.
* Note that `conn.execute` took in an insert statement generated via the `text` function along with a list of tuples containing two records worth of insert statements.
* Each `commit` call ends one transaction and begins a new one.  This can be done as many times as required, in a "commit as you go" model.
* By opening the connection with `engine.begin` you indicate to the engine that you want to commit everything at the end of the block.

### Basics of Statement Execution

Tutorial:  https://docs.sqlalchemy.org/en/20/tutorial/index.html

### Fetching Rows

Here is a more normal version of selecting and processing rows from a query:

```python
with engine.connect() as conn:
     result = conn.execute(text("SELECT x, y FROM some_table"))
     for row in result:
         print(f"x: {row.x}  y: {row.y}")
```

Results:

```
2023-06-30 14:50:17,311 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2023-06-30 14:50:17,311 INFO sqlalchemy.engine.Engine SELECT x, y FROM some_table
2023-06-30 14:50:17,311 INFO sqlalchemy.engine.Engine [generated in 0.00069s] ()
x: 1  y: 1
x: 2  y: 4
2023-06-30 14:50:17,312 INFO sqlalchemy.engine.Engine ROLLBACK
```

* Use a standard `for` loop to iterate over the results of the query.

* `row` is an arbitrary variable name, not a magic variable name.

* In this case, we accessed fields via `row.columnname`.

  * Indexing works:

  ``` python
  for row in result:
      x = row[0]
  ```

  * You can also do named tuple destructuring like so:

     ```python
  for x, y in result:
     ```

  * If you want dictionary access, use the `mappings` method:

  ```python
  for dict_row in result.mappings():
      x = dict_row["x"]
      y = dict_row["y"]
  ```

Bottom line, SQLAlchemy will try to meet you where you are.

### Parameterized Queries

```
with engine.connect() as conn:
    result = conn.execute(text("SELECT x, y FROM some_table WHERE y > :y"), {"y": 2})
    for row in result:
        print(f"x: {row.x}  y: {row.y}")
```

Results in:

```
2023-06-30 14:59:05,214 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2023-06-30 14:59:05,216 INFO sqlalchemy.engine.Engine SELECT x, y FROM some_table WHERE y > ?
2023-06-30 14:59:05,216 INFO sqlalchemy.engine.Engine [generated in 0.00071s] (2,)
x: 2  y: 4
2023-06-30 14:59:05,216 INFO sqlalchemy.engine.Engine ROLLBACK
```

* Bound parameters are marked in the query like so: `:variablename`.
* Parameters are passed into the query in dictionary form: `{"variablename" : value}`.
* Multiple parameters can be passed in as a list of dictionaries in the above form.
* Note that if you send in a list of parameters, you will not get result rows back.  The "executemany" pattern should only be used for DDL statements, updates, inserts, and deletes.
* This methodology also works with the ORM's `Session` object, which will be explored later.

## Working with Database Metadata

Tutorial:  https://docs.sqlalchemy.org/en/20/tutorial/metadata.html

This appears to be where the rubber meets the road, SQLAlchemy wise.  Everything up to this point is just `pyodbc` with some (very nice) syntactic sugar.

### `MetaData`, `Table`, and `Column`

`MetaData`, `Table`, and `Column` are the foundational classes for the SQL Expression Language.  They are used in both Core and ORM style usage.

#### `MetaData` and `Table`

The `MetaData` class serves as a collection point for `Table` objects, and can be treated as a facade over a bog standard Python dictionary keying table names to `Table` objects.

>  A `MetaData` object can be constructed directly, via the ORM, or read from the database via reflection.

Here is the direct method:

```python
from sqlalchemy import MetaData, Table, Column, Integer, String

metadata_obj = MetaData()

user_table = Table(
     "user_account",
     metadata_obj,
     Column("id", Integer, primary_key=True),
     Column("name", String(30)),
     Column("fullname", String),
)
```

In this example, when writing code referencing the `user_account` table, we will use the `user_table` object.

* The `Table` object represents a table in a database.

* It self-assigns to a given `MetaData` object.

* `Column` objects represent columns in the table.

* `Column` objects can be referenced via a dictionary stored at `Table.c`:

  ```
  >>> user_table.c.keys()
  ['id', 'name', 'fullname']
  >>> user_table.c.name   
  Column('name', String(length=30), table=<user_account>)
  ```

* [Official Datatypes Documentation](https://docs.sqlalchemy.org/en/20/core/types.html)

* [Official MetaData reference](https://docs.sqlalchemy.org/en/20/core/metadata.html)

### Declaring Simple Constraints

[Tutorial](https://docs.sqlalchemy.org/en/20/tutorial/metadata.html#declaring-simple-constraints)

| Constraint  | Example                                                      |
| ----------- | ------------------------------------------------------------ |
| Primary Key | `Column("id", Integer, primary_key=True)`                    |
| Foreign Key | `Column("user_id", ForeignKey("user_account.id"), nullable=False)` |
| Nullability | `Column("email", String,  nullable=False)`                   |

### Creating the Database

```python
metadata_obj.create_all(engine)
```

This will emit (based on a full schema that can be seen in the tutorial) the following:

```
BEGIN (implicit)
PRAGMA main.table_...info("user_account")
...
PRAGMA main.table_...info("address")
...
CREATE TABLE user_account (
    id INTEGER NOT NULL,
    name VARCHAR(30),
    fullname VARCHAR,
    PRIMARY KEY (id)
)
...
CREATE TABLE address (
    id INTEGER NOT NULL,
    user_id INTEGER NOT NULL,
    email_address VARCHAR NOT NULL,
    PRIMARY KEY (id),
    FOREIGN KEY(user_id) REFERENCES user_account (id)
)
...
COMMIT
```

(This is using the SQLite dialect.)

> Per the tutorial:
> This sort of database creation strategy is most appropriate for prototypes and test suites.  For more production ready techniques, a migration tool such as [Alembic](https://alembic.sqlalchemy.org/en/latest/) is more appropriate

### Using ORM Declarative Forms to Define Table MetaData

[Tutorial](https://docs.sqlalchemy.org/en/20/tutorial/metadata.html#using-orm-declarative-forms-to-define-table-metadata)

This seems unusual to me so I'm going to directly copy and paste from the tutorial for this:

> When using the ORM, the [`MetaData`](https://docs.sqlalchemy.org/en/20/core/metadata.html#sqlalchemy.schema.MetaData) collection remains present, however it itself is associated with an ORM-only construct commonly referred towards as the **Declarative Base**. The most expedient way to acquire a new Declarative Base is to create a new class that subclasses the SQLAlchemy [`DeclarativeBase`](https://docs.sqlalchemy.org/en/20/orm/mapping_api.html#sqlalchemy.orm.DeclarativeBase) class:

```python
from sqlalchemy.orm import DeclarativeBase
class Base(DeclarativeBase):
    pass
```

I am still unsure why we're subclassing DeclarativeBase just to go on and do this:

```python
from typing import List
from typing import Optional
from sqlalchemy.orm import Mapped
from sqlalchemy.orm import mapped_column
from sqlalchemy.orm import relationship

class User(Base):
    __tablename__ = "user_account"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(30))
    fullname: Mapped[Optional[str]]
    addresses: Mapped[List["Address"]] = relationship(back_populates="user")
    def __repr__(self) -> str:
        return f"User(id={self.id!r}, name={self.name!r}, fullname={self.fullname!r})"

class Address(Base):
    __tablename__ = "address"
    id: Mapped[int] = mapped_column(primary_key=True)
    email_address: Mapped[str]
    user_id = mapped_column(ForeignKey("user_account.id"))
    user: Mapped[User] = relationship(back_populates="addresses")
    def __repr__(self) -> str:
        return f"Address(id={self.id!r}, email_address={self.email_address!r})"
```

* Each class describes a single table.
* The columns in the table are described in terms of *class attributes*.  This was a new-ish one for me- I know about class level variables because I've been doing (and teaching) OOP for 20 years, but I wasn't familiar with the Python syntax for them.  Short story long, variables defined outside of a method and not attached to a `self` reference object are class scoped.  No special decorator is needed to define them (as opposed to methods which need the `@classmethod` decorator to mark them as class methods.
* Columns are given type hinting to describe their data type.  This allows for static checkers like `mypy` and IDE integrated linters a chance to do type checking while simultaneously telling SQLAlchemy about the metadata.
* Looks like constraints are handled by assigning a value returned from methods handed to us by SQLAlchemy. 
* Defining `__repr__` is a good idea, as always.
* The classes are automatically given an `__init__()` method if we don’t declare one of our own. The default form of this method accepts all attribute names as optional keyword arguments:

```python
sandy = User(name="sandy", fullname="Sandy Cheeks")
```

| Goal                           | Technique                    | Example                                                      |
| ------------------------------ | ---------------------------- | ------------------------------------------------------------ |
| Define table name              | Assign to `__tablename__`    | `__tablename__ = "address"`                                  |
| Set data type of column        | Use `Mapped`                 | `name: Mapped[str]`                                          |
| Set primary key                | Use `mapped_column`          | `id: Mapped[int] = mapped_column(primary_key=True)`          |
| Specify length of string field | Use `mapped_column`          | `name: Mapped[str] = mapped_column(String(30))`              |
| Specify nullable field         | Use `Mapped` with `Optional` | `fullname: Mapped[Optional[str]]`                            |
| Create database                | Call `create_all`            | `Base.metadata.create_all(engine)`<br />**nb:  This seems to imply that you can call this against the `Base` subclass of `DeclarativeBase` and it'll catch all of its subclasses??* |

* The `relationship` method will be discussed later vis a vis the ORM.

### Creating MetaData via Reflection

[Tutorial](https://docs.sqlalchemy.org/en/20/tutorial/metadata.html#table-reflection)

From the tutorial:

> There is no requirement that reflection must be used in order to use SQLAlchemy with a pre-existing database. It is entirely typical that the SQLAlchemy application declares all metadata explicitly in Python, such that its structure corresponds to that the existing database. The metadata structure also need not include tables, columns, or other constraints and constructs in the pre-existing database that are not needed for the local application to function.

That said... here's how to do it.  :)

```python
some_table = Table("some_table", metadata_obj, autoload_with=engine)
```

* Declare a `Table` object passing in the name of the table, the relevant `MetaData` object, and the `kwarg` `autoload_with` getting the appropriate `engine`. 

This results in the following (logged only on request, of course):

```
BEGIN (implicit)
PRAGMA main.table_...info("some_table")
[raw sql] ()
SELECT sql FROM  (SELECT * FROM sqlite_master UNION ALL   SELECT * FROM sqlite_temp_master) WHERE name = ? AND type in ('table', 'view')
[raw sql] ('some_table',)
PRAGMA main.foreign_key_list("some_table")
...
PRAGMA main.index_list("some_table")
...
ROLLBACK
```

* Verifies that the table exists
* Looks up the table parameters in the `sqlite_master` table (for sqlite, obviously)
* Also the foreign keys and indices

The `Table` object now looks like this:

```python
Table('some_table', MetaData(),
    Column('x', INTEGER(), table=<some_table>),
    Column('y', INTEGER(), table=<some_table>),
    schema=None)
```

More or less exactly what you would expect.  Hooray.

## Working with Data

This section details how to work with data directly via the SQLAlchemy Core module.  ORM details will be in the following section.

### Inserting

Here's the basic idea:

```python
from sqlalchemy import insert
stmt = insert(user_table).values(name="spongebob", fullname="Spongebob Squarepants")
```

Note that this is depending on a `Table` object called `user_table` which needs to have been built either directly with the Core library, or via the ORM declarative base.

This produces an `Insert` object.  Printing it produces the following:

```
INSERT INTO user_account (name, fullname) VALUES (:name, :fullname)
```

You can look at the parameters of the statement by `compiling` it and interrogating the compiled statement object:

```python
compiled = stmt.compile()
print(compiled.params)
```

Yields:

```
{'name': 'spongebob', 'fullname': 'Spongebob Squarepants'}
```

To execute the statement, send it into a connection:

```python
with engine.connect() as conn:
    result = conn.execute(stmt)
    conn.commit()
```

This performs (and commits) the insert:

```
BEGIN (implicit)
INSERT INTO user_account (name, fullname) VALUES (?, ?)
[...] ('spongebob', 'Spongebob Squarepants')
COMMIT
```

If a **single** insert is done, the value returned will include values that were generated during the insert:

```
result.inserted_primary_key # (1,)
```

> The `inserted_primary_key` is a tuple because primary keys can have multiple values.

A more usual way of doing the insert omits the `.values()` call and relies on passing in dictionary(ies) of parameters:

```python
with engine.connect() as conn:
    result = conn.execute(
        insert(user_table),
        [
            {"name": "sandy", "fullname": "Sandy Cheeks"},
            {"name": "patrick", "fullname": "Patrick Star"},
        ],
    )
    conn.commit()
```

* The insert() call itself is the same as above, but it's nested inside of the `conn.execute` call along with a list of dictionaries which have the parameters for insertion.  
* This is equivalent to the earlier "executemany" version we did with `text()`.
* We don't get back the results this way.

#### Insert ... Returning

[Tutorial](https://docs.sqlalchemy.org/en/20/tutorial/data_insert.html#insert-returning)

There is an implicit call to the method `returning` when doing an `insert`.  This is what retrieves the primary key and server defaults.  It can also be specified explicitly to add additional columns, like so:

```python
insert_stmt = insert(address_table).returning(
    address_table.c.id, address_table.c.email_address
)
print(insert_stmt)
```

This prints out:

```
INSERT INTO address (id, user_id, email_address)
VALUES (:id, :user_id, :email_address)
RETURNING address.id, address.email_address
```

This also works with the "insert from select" method below.

#### Insert ... From Select

This is used for situations where you want to build rows in one table based on a query.  It looks like this:

```python
select_stmt = select(user_table.c.id, user_table.c.name + "@aol.com")
insert_stmt = insert(address_table).from_select(
    ["user_id", "email_address"], select_stmt
)
print(insert_stmt)
```

The general form is:  `insert(table_obj).from_select([list_of_field_names], select_obj)`.

### Selecting

[Tutorial](https://docs.sqlalchemy.org/en/20/tutorial/data_select.html)

For both the ORM and Core, use the `select` method to generate select statements.

#### Simple Version

```python
from sqlalchemy import select
stmt = select(user_table).where(user_table.c.name == "spongebob")
print(stmt)
```

This yields:

```
SELECT user_account.id, user_account.name, user_account.fullname
FROM user_account
WHERE user_account.name = :name_1
```

* We're passing a `Table` object into the `select` method.
* We use standard Python comparators inside of the `where` method.
* Notice that we pull a `Column` object off of the `Table` object as part of the comparison.

Running the statement involves passing it into `Connection.execute()` (or `Session.execute` if we're using the ORM model)  like so:

```python
with engine.connect() as conn:
    for row in conn.execute(stmt):
        print(row)
```

* This is exactly the same as we have seen up to this point.
* The `row` variable is a tuple, as before.
* The ORM variant returns an ORM entity as expected.

To limit the columns being returned, you can specify them as part of the `select` call like so:

```python
select(user_table.c.name, user_table.c.fullname)
```

* Note that we're using the `Table.c` accessor to specify the columns.
* The `FROM` clause will be inferred from the `Columns` (and other `FromClause` objects which will be described later) given in the `select` call.

You can also specify the columns by name like this:

```python
select(user_table.c["name", "fullname"])
```

#### Selecting ORM Entities

You can use ORM entities in your select like so:

```python
select(User) # where User is an ORM entity class
```

This will return an iterable containing objects of the `User` class:

```python
row = session.execute(select(User)).first()
```

`row` now contains:

```
(User(id=1, name='spongebob', fullname='Spongebob Squarepants'),)
```

#### The WHERE Clause

We have already seen that the WHERE clause can be specified as a standard Python boolean comparison.  These can be chained as AND statements by listing several in the call like so:

```python
select(address_table.c.email_address).where(
    user_table.c.name == "squidward",
    address_table.c.user_id == user_table.c.id,
)
```

To make more explicit AND and OR statements use the `and_` and `or_` methods:

```python
from sqlalchemy import and_, or_
select(Address.email_address).where(
    and_(
        or_(User.name == "squidward", User.name == "sandy"),
        Address.user_id == User.id,
    )
)
```

* This is saying that the `User.name` field is "squidward" OR "sandy" AND that the `Address.user_id` field matches the `User.id` field.
* This puts the join in the WHERE clause, which is interesting:

```python
SELECT address.email_address
FROM address, user_account
WHERE (user_account.name = :name_1 OR user_account.name = :name_2)
AND address.user_id = user_account.id
```

#### Explicit FROM clauses and JOINs

In order to specify JOIN clauses, we generally do one of two things.  

#### `join_from`

```python
select(user_table.c.name, address_table.c.email_address)
      .join_from(user_table, address_table
```

This specifies both sides of the join.  SQLAlchemy uses the `MetaData` associated with the tables to infer the joining columns.

#### `join`

```python
select(user_table.c.name, address_table.c.email_address).join(address_table)
```

In this case, we only specify the right side of the join, letting SQLAlchemy infer the left side via the `MetaData`.

If needed, we can be explicit about the `FROM` clause as well:

```python
select(address_table.c.email_address).select_from(user_table).join(address_table)
```

This method is only used when SQLAlchemy isn't producing the desired SQL output (or doesn't have sufficient information to do so via the `MetaData`.)  This can happen when using functions such as:

```sql
SELECT count(*) FROM user_table
```

The SQL `count` function, used in this way, doesn't give enough details to infer what table is being selected from.  In this case, we can do this:

```python
from sqlalchemy import func
select(func.count("*")).select_from(user_table)
```

* We're importing the `func` namespace from `sqlalchemy`.
* `func` supports the `count` method as a way to use the ANSI SQL `COUNT` keyword.

* For more information on the `func` namespace, [here is some official documentation](https://docs.sqlalchemy.org/en/20/core/sqlelement.html#sqlalchemy.sql.expression.func).

### Setting the ON Clause

[Tutorial](https://docs.sqlalchemy.org/en/20/tutorial/data_select.html#setting-the-on-clause)

Generally, SQLAlchemy can figure out the correct JOIN ON clause based on the existing `MetaData`.  If, however, specificity is needed:

```python
select(address_table.c.email_address)
.select_from(user_table)
.join(address_table, user_table.c.id == address_table.c.user_id)
```

* In the `join` method, the first argument is the targeted table and the second is a standard Python boolean comparator indicating the columns used in the JOIN.

You can also specify OUTER and FULL joins like so:

```python
select(user_table).join(address_table, isouter=True)
select(user_table).join(address_table, full=True)
```

* To do a RIGHT OUTER JOIN, reverse the order of the tables.

#### ORDER BY, GROUP BY, HAVING

These are pretty straightforward and are deducible from the conventions we've seen so far.  In short:

##### ORDER BY

```python
select(user_table).order_by(user_table.c.name)
select(User).order_by(User.fullname.desc())
```

* The first line orders by a given column ascending.
* The second line specifies descending order using the `desc` method.  
  * There is also an `asc` method for ascending order, but that's the default.

##### Aggregating with GROUP BY and HAVING

```python
with engine.connect() as conn:
    result = conn.execute(
        select(User.name, func.count(Address.id).label("count"))
        .join(Address)
        .group_by(User.name)
        .having(func.count(Address.id) > 1)
    )
    print(result.all())
```

Resolves to:

```
[('sandy', 2)]
```

* This is an ordered list (because of the use of `all()`) with the grouping value coming first and the aggregate function value second.
* Notice the `having` method at the end of the chain.  This is using the SQL `COUNT` function as part of the boolean comparison.

If you're using labels for your query and want to order/group by a labeled column, pass the label in as text:

```python
from sqlalchemy import func, desc
stmt = (
    select(Address.user_id, func.count(Address.id).label("num_addresses"))
    .group_by("user_id")
    .order_by("user_id", desc("num_addresses"))
)
print(stmt)
```

Resulting in:

```
SELECT address.user_id, count(address.id) AS num_addresses
FROM address GROUP BY address.user_id ORDER BY address.user_id, num_addresses DESC
```

#### Aliases

When writing queries which require multiple references to the same table (due to subqueries or other nonsense), SQLAlchemy can provide you with alias variables using the aptly named `alias` method:

```python
user_alias_1 = user_table.alias()
user_alias_2 = user_table.alias()

select(user_alias_1.c.name, user_alias_2.c.name).join_from(
    user_alias_1, user_alias_2, user_alias_1.c.id > user_alias_2.c.id
)

```

This yields the following SQL:

```sql
SELECT user_account_1.name, user_account_2.name AS name_1
FROM user_account AS user_account_1
JOIN user_account AS user_account_2 ON user_account_1.id > user_account_2.id
```

Though why you would be doing that specific query is... yeah.  Completely beyond me.

If you're using the ORM frame, the `aliased` method is what you're looking for:

```python
from sqlalchemy.orm import aliased
address_alias_1 = aliased(Address)
address_alias_2 = aliased(Address)

select(User)
.join_from(User, address_alias_1)
.where(address_alias_1.email_address == "patrick@aol.com")
.join_from(User, address_alias_2)
.where(address_alias_2.email_address == "patrick@gmail.com")

```

That yields this SQL:

```sql
SELECT user_account.id, user_account.name, user_account.fullname
FROM user_account
JOIN address AS address_1 ON user_account.id = address_1.user_id
JOIN address AS address_2 ON user_account.id = address_2.user_id
WHERE address_1.email_address = :email_address_1
AND address_2.email_address = :email_address_2
```

Also a bizarre example.  Huh.

At this point we're getting pretty deep in the weeds, so I'm just going to list bookmarks to the remaining esoterica:

| Topic                                           | Link                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| Subqueries and Common Table Expressions (CTE's) | https://docs.sqlalchemy.org/en/20/tutorial/data_select.html#subqueries-and-ctes |
| Scalar and Correlated Subqueries                | https://docs.sqlalchemy.org/en/20/tutorial/data_select.html#scalar-and-correlated-subqueries |
| Set Operations (Union and friends)              | https://docs.sqlalchemy.org/en/20/tutorial/data_select.html#union-union-all-and-other-set-operations |
| EXISTS                                          | https://docs.sqlalchemy.org/en/20/tutorial/data_select.html#exists-subqueries |
| Special Modifiers in GROUP/FILTER               | https://docs.sqlalchemy.org/en/20/tutorial/data_select.html#special-modifiers-within-group-filter |
| Data Casts and Type Conversions                 | https://docs.sqlalchemy.org/en/20/tutorial/data_select.html#data-casts-and-type-coercion |

### Working with SQL Functions

[Tutorial](https://docs.sqlalchemy.org/en/20/tutorial/data_select.html#working-with-sql-functions)

Short version: 

* `from sqlalchemy import func`
* `func.functionname` calls the function in question.  Given the variety of backends, SQLAlchemy is very liberal with what you call here:  if the function doesn't exist natively in SQLAlchemy, it'll just use whatever you put in as the function name and send it to the database engine.

Example:

```python
select(func.lower("A String With Much UPPERCASE"))
```

yields

```sql
SELECT lower(:lower_2) AS lower_1
```

Arguments work as expected:

```python
select(func.some_crazy_function(user_table.c.name, 17))
```

```sql
SELECT some_crazy_function(user_account.name, :some_crazy_function_2) AS some_crazy_function_1
FROM user_account
```

### Updating and Deleting



## Data Manipulation with the ORM



## Working with ORM Related Objects



## Further Reading





