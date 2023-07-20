# Databases

## SQLite

```python
import sqlite3

with sqlite3.connect("data.db") as conn:   # Connect to a file
    with conn.cursor() as cursor:          # Open a cursor
        pass                               # Do the things
```

