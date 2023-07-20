# File IO

## Files Operations

[General file IO](https://docs.python.org/3/tutorial/inputoutput.html#reading-and-writing-files)

[csv module documentation](https://docs.python.org/3/library/csv.html)

(json module documentation)[https://docs.python.org/3/library/json.html]

```python
file_handle = open('file.name', 'r') # Open for read only
contents = file_handle.read()        # Read the WHOLE file into one string
file_handle.close()                  # Release the file

file_handle = open('file.name', 'w') # Open for write - create or overwrite, 'a' for append
file_handle.write(string)            # Write a string to a file, doesn't add a newline

for line in file_handle:
    pass #this is the preferred loop for reading a file.

with open('workfile', encoding="utf-8") as f:
	# do operations here
    # When this block exits, the file handle will be closed.
    
# Note that `with` can be used for read operations as well.
```

> Per the official docs:
>
> JSON files must be encoded in UTF-8. Use `encoding="utf-8"` when opening JSON file as a [text file](https://docs.python.org/3/glossary.html#term-text-file) for both of reading and writing.
>
> See the "with" example above.