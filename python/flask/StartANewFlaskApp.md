# Starting a new Flask app

1. Create your project
1. Make sure it has a virtual env

```console
c:\>c:\Python35\python -m venv c:\path\to\myenv
```

Or similar.  PyCharm can do this for you as well.

3. Activate the virtual environment

| Platform   | Shell                                   | Command to activate virtual environment |
| :--------- | :-------------------------------------- | :-------------------------------------- |
| POSIX      | bash/zsh                                | `$ source *<venv>*/bin/activate`        |
| fish       | `$ source *<venv>*/bin/activate.fish`   |                                         |
| csh/tcsh   | `$ source *<venv>*/bin/activate.csh`    |                                         |
| PowerShell | `$ *<venv>*/bin/Activate.ps1`           |                                         |
| Windows    | cmd.exe                                 | `C:\> *<venv>*\Scripts\activate.bat`    |
| PowerShell | `PS C:\> *<venv>*\Scripts\Activate.ps1` |                                         |

tl;dr:  `./venv/scripts/activate` or `./venv/bin/activate` depending on windows vs unix.

4. Create your application file:

   * Use the name `app.py` for simplicity
   * Here's the source:

   ```python
   from flask import Flask
   
   app = Flask(__name__)
   ```

5. Run flask:

   ```console
   flask run
   ```

This should start a dev server with no endpoints.  If it does, congrats, you've won.	