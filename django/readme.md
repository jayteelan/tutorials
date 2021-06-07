# How To Django

## Contents

- [Setting up a new project](https://github.com/jayteelan/tutorials/tree/master/django#lets-get-things-set-up)
- [Creating an app](https://github.com/jayteelan/tutorials/tree/master/django#now-were-ready-to-create-a-polls-app)
- [Integrating with Postgres](https://github.com/jayteelan/tutorials/tree/master/django#set-up-a-postgres-database)

## Let's get things set up

### Create a virtual environment

`venv` creates an isolated Python environment where packages can be installed locally and without admin privileges.

Navigate to the target directory and run `venv`:

    $ python3 -m venv my-new-virtual-environment

Then activate the virtual environment (omit `.fish` if using bash):

    $ source my-new-virtual-environment/bin/activate.fish

### Install Django

    $ python3 -m pip install Django

This adds the Django package to `bin/` in the virtual environment. You can confirm the installation by checking its version:

    $ python3 -m django --version

### Create a new project

Navigate into the target directory and create a new project directory:

    $ django-admin startproject mysite

In addition to the outer `mysite/` directory (which can take any title and acts as a container for the project as a whole), `startproject` also creates:

- `manage.py`: a command line utility to interact with the project
- `mysite/`: this inner directory (i.e., `mysite/mysite/`) is the actual Python package for the project. Note that `mysite` is also the name of the package, which is used when importing files into it (e.g., `mysite.urls`)
  - `__init__.py` marks this directory as a Python package
  - `settings.py` contains the project's settings and configuration
  - `urls.py` contains the URL declarations, i.e., a table of contents for the site
  - `asgi.py` is the entry point for ASGI-compatible web servers to serve the project, and
  - `wsgi.py` is the entry point for WSGI-compatible web servers

It's also a good idea to add a `.gitignore` to the project directory. Here's [The Ultimate Django .gitignore](https://djangowaves.com/tips-tricks/gitignore-for-a-django-project/#heres-the-ultimate-django-gitignore "The Ultimate Django .gitignore")

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

    $ python3 manage.py runserver

Confirm the server is running by opening `http://localhost:8000` in the browser. The server should automatically reload the code as needed, but it may sometimes be necessary to restart it manually, e.g., when adding files.

The server's port can optionally by changed by passing it as an argument:

    $ python3 manage.py runserver 8080

## Now we're ready to create a Polls app!

This new app will ultimately be imported as a top-level module in our project, so it'll go in the outer `mysite/` directory. If we instead create an app in the inner `mysite/` directory (i.e., the project package), it will be imported as a submodule of the `mysite` package.

Navigate to the proper directory and create the app:

    $ python3 manage.py startapp polls

The newly-created `polls/` directory houses the poll application

### Write a view

This first view will return a simple "Hello world" statement when the user navigates to the index URL. The logic will be defined in `polls/views.py` while routing will be handled by an URLconf, which we will have to manually create in the `polls/` directory as `urls.py`.

_polls/views.py_

```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello world! This is the polls index")
```

_polls/urls.py_

```python
from django.urls import path

from . import views

urlpatterns=[
  path('',views.index,name='index'),
]
```

### Create a path to the view

Now update the project package's URLconf (`mysite/urls.py`) with a new route that points to the poll module's URLconf (`polls/urls.py`) for further processing. The `include()` function from `django.urls` is used to indicate where external URLconfs can be found.

_mysite/urls.py_

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
  path('polls/', include('polls.urls')),
  path('admin/', admin.site.urls),
]
```

Note the general syntax of URL patterns:

```python
path('route/', include('view.urls')),
```

where `route/` represents what gets appended to the base URL and `view.urls` indicates where the target URLconf is located.

The default `admin/` route pointing to `admin.site.urls` is the **ONLY** time the `include()` function isn't used within the `path()` function.

With the dev server running, `http://localhost:8000/polls/` will now display the polls app!

## Set up a Postgres database

Python includes and defaults to SQLite for databases. To use a different database such as PostgreSQL, [a bit more work is needed](https://djangocentral.com/using-postgresql-with-django/ "a bit more work is needed")...

### Install Psycopg2, which allows PostgreSQL to communicate with Python:

    $ sudo pip install psycopg2-binary

### Enter the Postgres CLI with full super admin access:

    $ sudo -u postgres psql

The terminal should now be prefixed with `postgres=#`.

### Create a database and user

```sql
CREATE DATABASE mydb;
CREATE USER defaultuser WITH ENCRYPTED PASSWORD '12345';
```

### Modify connection parameters to work with Django

```sql
ALTER ROLE defaultuser SET client_encoding TO 'utf8';
ALTER ROLE defaultuser SET default_transaction_isolation TO 'read committed';
ALTER ROLE defaultuser SET timezone TO 'UTC';
```

### Grant permissions to `defaultuser` and exit the SQL prompt

```sql
GRANT ALL PRIVILEGES ON DATABASE mydb TO defaultuser;
\q
```

### Integrate PostgreSQL with Django

Open `mysite/settings.py` and update the settings in the database section:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'mydb',
        'USER': 'defaultuser',
        'PASSWORD': '12345',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```

### Run a migration to test the database connection

    $ python3 manage.py migrate

### Create a superuser to access the admin panel (optional)

    $ python3 manage.py createsuperuser

With the dev server running, go to `http://localhost:8000/admin` in the browser and login with the superuser credentials. The admin panel will show the `Groups` and `Users` models from Django's authentication framework.
