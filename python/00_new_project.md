# Setting up a new project

## Make a new directory

Pretty self explanatory.

## Make it a git repo

```
git init
```

Or, use your IDE.

Or, clone a repo.

## Make a virtual environment

```
python -m venv venv
```

## Set up your .gitignore

[Stock .gitignore](./gitignore.md)

## VS Code Settings

[Stock settings](./vscode_settings.md)

Install the Black and MyPy extensions

* `pip install -U black

Stock settings file forces formatting on save and on paste.

## Make sure your tools are up to date

From inside your virtual environment:

```
pip install --upgrade pip setuptools wheel
```

## A word on the src/ layout

[Sample project from PyPA](https://github.com/pypa/sampleproject)

Short version:

* project definition files (ie, README.md, setup.cfg, etc) go in the project root

* tests go in `tests/`

* source starts in `src/`

  * `src` contains the project's main module, ie: `project_name`
  * `src` might contain a `project_name.py` file which would look like this:

  ```python
  from omission.__main__ import main
  main()
  ```

  * main module has `__init__.py`, making it a package
  * main module might also have `__main__.py` as an entry point if it's an application (as opposed to a library)
  * subpackages of `project_name` will also need `__init__.py` files to keep the packages concept working

## Files in the Root Directory

Copied from Dead Simple Python, by Jason D. MacDonald.  Based on [the Python Packaging Authority's (PyPA) sample project](https://github.com/pypa/sampleproject/).

| File               | Use                                                          |
| ------------------ | ------------------------------------------------------------ |
| `README.md`        | A Markdown file with project information.                    |
| `LICENSE`          | Contains the project license.                                |
| `pyproject.toml`   | specifies the build backend and lists build requirements for the package. |
| `setup.cfg`        | Contains distribution package metadata, options, and dependencies. |
| `setup.py`         | Used to contain packaging instructions and dependencies; now it just ties things together in the source distribution package. |
| `MANIFEST.in`      | Lists all the non-code files that should be included in the distribution package. |
| `requirements.txt` | Lists dependencies (which are used differently from setup.cfg; it’s often useful to have both). |

Create these files at the start of your project and maintain them as your project develops.  This will make it easier to pick up after a break and avoids a last minute skimp on build and documentation tasks.

## `setup.cfg`

[Official Documentation](https://setuptools.pypa.io/en/latest/userguide/declarative_config.html)

### Metadata

```
[metadata]
name = project-name-here
version = 1.2.3
description = Project description.
long_description = file: README.md
long_description_content_type = text/markdown; charset=UTF-8
license_files = LICENSE
author = Jason C. McDonald
author_email = codemouse92@outlook.com
url = https://github.com/codemouse92/timecard
project_urls =
    Bug Reports = https://github.com/codemouse92/timecard/issues
    Funding = https://github.com/sponsors/CodeMouse92
    Source = https://github.com/codemouse92/timecard
keywords = time tracking office clock tool utility
classifiers =
    Development Status :: 5 - Production/Stable
    Environment :: X11 Applications :: Qt
    Natural Language :: English
    Operating System :: OS Independent
    Intended Audience :: End Users/Desktop
    Topic :: Office/Business
    License :: OSI Approved :: BSD License
    Programming Language :: Python
    Programming Language :: Python :: 3
```

* Classifiers are used to make package searching easier.  [Full documentation here](https://pypi.org/classifiers/)

### Options

```
[options]

package_dir =
    =src
packages = find:
include_package_data = True
python_requires = >=3.6, <4
install_requires =
    PySide2>=5.15.0
    appdirs>=1.4.4
    
[options.packages.find]
where = src

[options.extras_require]
test =
    pytest

[options.entry_points]
gui_scripts =
    Timecard-App = timecard.__main__:main
```

* This is set for a `src/` style layout.  Notice the `=src` and `find:` values.  Those bits of punctuation are, in fact, required.
* `find:` is telling setuptools to use its package finding algorithm.
* `options.packages.find` is telling setuptools where to perform the find. (Why we have to specify it twice is currently unknown to me.)
* `include_package_data` is there to indicate whether or not there are non-code artifacts to package up.  (`True` = yes)
* `python_requires` indicates what version of python is required.
* `install_requires` lists the dependencies for this package.
  * It is possible to specify certain packages as only requires for certain versions of Python, though this is rare.
* The `options.extras_require` section lists optional extra requirements.  In this example, `pytest` would be installed if the user did this:
  * `pip install Timecard-App[test]`
* The `options.entry_points` section specifies where the application should begin if the package is a runnable application. You can use `gui_scripts` for a gui version and `console_scripts` for a console version.

## The Minimal `setup.py`

Probably not needed unless you do (for backwards compatibility or packages with C dependencies and such.)  If you do, here's a minimal one:

```python
from setuptools import setup
setup()
```



## The `MANIFEST.in` file

[Official Documentation](https://packaging.python.org/en/latest/guides/using-manifest-in/)

This file lists all non-code files which should be included with the package.

```
include LICENSE *.md
```

Note that these are two items.  The `LICENSE` file and all `.md` files.

``` 
graft src/timecard/resources
graft distribution_resources
```

The `graft` keyword bulk copies directories.

For more info as to all the neat stuff setuptools can do with the `MANIFEST.in` file, see the official documentation above.

## The `requirements.txt` file

Still needed.  `setup.cfg` is for your distribution, `requirements.txt` is for your dev environment.  There **will** be overlaps, but it's still important to keep these concepts separate.

## The `pyproject.toml` file

This file specifies the build system to be used.  Here's a sample:

```
    [build-system]
    requires = ["setuptools>=40.8.0", "wheel"]
    build-backend = "setuptools.build_meta"
```

## Testing the setup config

**From within a virtual environment, preferably a fresh one for testing!**

```
pip install .
```

If your package is directly runnable, you can test the installed version with:

```
venv/bin/project-name
```

If it's a library, run the REPL and try to import it.

## Installing as Editable

```
pip install -e .
```

This will cause the installation to use the `src/` directory directly, which is probably what you want for unit testing and subsequent debugging.

You might want to do this if you've got test dependencies marked out:  

```
pip install -e '.[test]'
```



## Building the package

```
python3 -m pip install --upgrade setuptools wheel build
python3 -m build
```

This should result in a `dist/` directory with two files:

* `.tar.gz`: the source distribution
* `.whl`: the distribution wheel

For more info on how to upload to PyPI (and test.pypi), see https://pypi.org/help/ or the book I'm cribbing all of this from, _Dead Simple Python_.

