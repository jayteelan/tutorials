# How To Django

## Let's get things set up

### Create a virtual environment

`venv` creates an isolated Python environment where packages can be installed locally and without admin privileges. Navigate to the target directory and run `venv`:
`$ python3 -m venv my-new-virtual-environment`
Then activate the virtual environment:
`$ source my-new-virtual-environment/bin/activate.fish`

### Install Django

`$ python3 -m pip install Django`
This adds the Django package to `bin/` in the virtual environment. You can confirm the installation by checking its version:
`$ python3 -m django --version`

### Create a new project

Navigate into the target directory and create a new project directory:
`$ django-admin startproject mysite`
In addition to the outer `mysite/` directory (which can take any title and acts as a container for the project as a whole), `startproject` also creates:

- `manage.py`: a command line utility to interact with the project
- `mysite/`: this inner directory (i.e., `mysite/mysite/`) is the actual Python package for the project. Note that `mysite` is also the name of the package, which is used when importing files into it (e.g., `mysite.urls`)
  - `__init__.py` marks this directory as a Python package
  - `settings.py` contains the project's settings and configuration
  - `urls.py` contains the URL declarations, i.e., a table of contents for the site
  - `asgi.py` is the entry point for ASGI-compatible web servers to serve the project, and
  - `wsgi.py` is the entry point for WSGI-compatible web servers

It's also a good idea to add a `.gitignore` to the project directory. Here's [the ultimate Django gitignore](https://djangowaves.com/tips-tricks/gitignore-for-a-django-project/#heres-the-ultimate-django-gitignore "The Ultimate Django .gitignore")

```
# Django #
*.log
*.pot
*.pyc
__pycache__
db.sqlite3
media

# Backup files #
*.bak

# If you are using PyCharm #
.idea/**/workspace.xml
.idea/**/tasks.xml
.idea/dictionaries
.idea/**/dataSources/
.idea/**/dataSources.ids
.idea/**/dataSources.xml
.idea/**/dataSources.local.xml
.idea/**/sqlDataSources.xml
.idea/**/dynamic.xml
.idea/**/uiDesigner.xml
.idea/**/gradle.xml
.idea/**/libraries
*.iws /out/

# Python #
*.py[cod]
*$py.class

# Distribution / packaging
.Python build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg
*.manifest
*.spec

# Installer logs
pip-log.txt
pip-delete-this-directory.txt

# Unit test / coverage reports
htmlcov/
.tox/
.coverage
.coverage.*
.cache
.pytest_cache/
nosetests.xml
coverage.xml
*.cover
.hypothesis/

# Jupyter Notebook
.ipynb_checkpoints

# pyenv
.python-version

# celery
celerybeat-schedule.*

# SageMath parsed files
*.sage.py

# Environments
.env
.venv
env/
venv/
ENV/
env.bak/
venv.bak/

# mkdocs documentation
/site

# mypy
.mypy_cache/

# Sublime Text #
*.tmlanguage.cache
*.tmPreferences.cache
*.stTheme.cache
*.sublime-workspace
*.sublime-project

# sftp configuration file
sftp-config.json

# Package control specific files Package
Control.last-run
Control.ca-list
Control.ca-bundle
Control.system-ca-bundle
GitHub.sublime-settings

# Visual Studio Code #
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json
.history
```

### Start the dev server

Navigate into the outer `mysite` directory and run
`$ python3 manage.py runserver`
Confirm the server is running by opening `http://localhost:8000` in the browser. The server should automatically reload the code as needed, but it may sometimes be necessary to restart it manually, e.g., when adding files.

The server's port can optionally by changed by passing it as an argument:
`$ python3 manage.py runserver 8080`

#
