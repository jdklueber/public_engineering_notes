# Making  Virtual Environment

You can do this in a couple of ways.



Old School:

```
python -m venv venv
```

That makes a virtual environment inside your current directory.



Possibly better:

```
# If you haven't already
pip install pipenv  
# Create/load the virtual environment
pipenv shell
```

This makes a new virtual environment in your user directory (~/.virtualenvs) if one doesn't already exist and then loads it.

