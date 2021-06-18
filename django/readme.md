# How To Django

## Contents

- [Setting up a new project](https://github.com/jayteelan/tutorials/tree/master/django#lets-get-things-set-up)
- [Creating an app](https://github.com/jayteelan/tutorials/tree/master/django#now-were-ready-to-create-a-polls-app)
- [Integrating with Postgres](https://github.com/jayteelan/tutorials/tree/master/django#set-up-a-postgres-database)
- [Creating models in the database](https://github.com/jayteelan/tutorials/tree/master/django#lets-finally-make-some-dang-models)
- [Working with the database API and admin portal](https://github.com/jayteelan/tutorials/tree/master/django#interact-with-the-api)
- [Creating views for the browser](https://github.com/jayteelan/tutorials/tree/master/django#creating-views)

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

## Let's finally make some dang models!

### Define the models and their relationships

Models are defined in a module's `models.py` file and represented by Python classes:
_polls/models.py_

```python
import datetime
from django.db import models
from django.utils import timezone

class Question(models.Model):
  question_text = models.CharField(max_length=200)
  pub_date = models.DateTimeField('date published')

  def was_published_recently(self):
    return self.pub_date >= timezone.now() = datetime.timedelta(days=1)

  def __str__(self):
    return self.question_text

class Choice(models.Model):
  question = models.ForeignKey(Question, on_delete=models.CASCADE)
  choice_text = models.CharField(max_length=200)
  votes = models.IntegerField(default=0)

  def __str__(self):
    return self.choice_text
```

Each class variable represents a database field (or column) in the model and follows the generalized syntax

```python
field_name = models.FieldClass('optional human-readable name',other_arguments)
```

If no human-readable name is given, Django will default to using the machine-readable name (i.e., `field_name`).

Django has a [number](https://docs.djangoproject.com/en/3.2/ref/models/fields/#model-field-types "different model field types") of field classes built in; some of these have required arguments, such as `models.CharField(max_length=[integer])`. Custom field classes can also be written.

Notice that field relationships are also defined using field classes.

| Relationship | Field Class                                                                                                         | Syntax with Required Arguments                    |
| ------------ | ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------- |
| Many-to-one  | [ForeignKey](https://docs.djangoproject.com/en/3.2/ref/models/fields/#foreignkey "further documentation")           | `ForeignKey(to, on_delete)`                       |
| Many-to-many | [ManyToManyField](https://docs.djangoproject.com/en/3.2/ref/models/fields/#manytomanyfield "further documentation") | `ManyToManyField(to)`                             |
| One-to-one   | [OneToOneField](https://docs.djangoproject.com/en/3.2/ref/models/fields/#onetoonefield "further documentation")     | `OneToOneField(to, on_delete, parent_link=False)` |

The `to` argument in each of the above cases is the positional argument, or the other class/field in the relationship.

Model classes can also include custom methods, such as `Question.was_published_recently(self)`.

Finally, each class has a `__str__(self)` method, which allows Django to return a human-readable representation of the object when it's later called in the shell or admin page (this will be explained further in a later step).

### Install the models and run a migration

Django can use the model definitions in `models.py` to create schema (tables) for the polls app, but first the app needs to be included in the project. This is done by adding the path to its associated configuration class to the `INSTALLED_APPS` array in `mysite/settings.py`. Django created the configuration class `PollsConfig` for the polls app in `polls/apps.py`, so the updated settings should now read
_mysite/settings.py_

```python
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

Now that the polls app has been included and its schema has been updated to include the `Question` and `Choice` models, it's now necessary to create a new migration file:

    $ python3 manage.py makemigrations polls

The resulting migration file can be found at `polls/migrations/0001_initial.py`.

If desired, the `sqlmigrate` command can now be run on the new migration file to inspect the resulting SQL:

    $ python3 manage.py sqlmigrate polls 0001

Django's `check` function can also be used to check for problems in the project without making a new migration file or altering the database:

    $ python3 manage.py check

If everything looks good, run a migration to apply the changes to the database:

    $ python3 manage.py migrate

## Interact with the API

Django provides [an inbuilt API](https://docs.djangoproject.com/en/3.2/topics/db/queries/ "database API") to interact with the database through the Python shell rather than through Postgres. To begin, launch the shell through `manage.py`:

    $ python3 manage.py shell

Then import the models as well as the `timezone` utility, which is used to set `pub_date` values:

```python
from polls.models import Choice, Question
from django.utils import timezone
```

New records can be added by first defining a new object with a variable, then running the `save()` method to save it to the database:

```python
q = Question(question_text="What's new?", pub_date=timezone.now())
q.save()
```

Values for each record are accessed as Python attributes. Note that the primary key and an `id` attribute are automatically assigned.

```sh
>>> q.id
1
>>> q.question_text
"What's new?"
>>> q. pub_date
datetime.datetime(2021, 6, 10, 16, 1, 27, 25341, tzinfo=<UTC>)
```

Values can be updated by changing the attributes, then calling `save()` again:

```python
q.question_text = "sup, bitches?"
q.save()
```

Records can be called by running functions such as `objects.get()`, `objects.filter()`, or `objects.all()` on the model class:

```sh
>>> Question.objects.get(id=1)
<QuerySet [Question: sup, bitches?]>
# without the __str__(self) method in the model class, this would instead return
# <QuerySet [Question: Question object (1)]>
```

All of the choices associated with question `q` can be called with `q. choice_set.all()`; choices can be added with `q.choice_set.create()`. Choice objects can optionally be assigned a variable to simplify calling them later:

```sh
>>> q.set_choice.create(choice_text="Not much", votes=0)
<Choice: Not much>
>>> q.set_choice.create(choice_text="The sky", votes=0)
<Choice: The sky>
>>> c = q.choice_set.create(choice_text="Just hacking again", votes=0)

# Choice objects have access to their related Question objects and vice versa.
>>> c.question
<Question: sup bitches?>
>>> q.choice_set.all()
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
```

Double underscores are used to separate relationships. For example, to find all Choices associated with questions published in 2021, one would enter:

```sh
>>> Choice.objects.filter(question__pub_date__year=2021)
```

### Set up the admin portal

Alternately, the database can be accessed and edited through Django's admin page once [a user with admin access has been created](https://github.com/jayteelan/tutorials/tree/master/django#create-a-superuser-to-access-the-admin-panel-optional "how to create an admin user"). However, the new models must be added to the admin page before they can be viewed:

_polls/admin.py_

```python
from django.contrib import admin

from .models import Question,Choice

admin.site.register([Question, Choice])

```

## Creating views

Django is able to deliver content from the database to the client as views, or web pages, which are represented as Python functions or class methods. The URLconfs set up earlier provide structured URL patterns - such as `/newsarchive/<year>/<month>` - which Django then uses to select the appropriate view.

### Add some more views to the polls app

Now that the database has been set up, we can create views with arguments that pull information from it. Add the following to `polls/views.py`:

```python
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

Of course, each new view needs a correspoding URL pattern. Add the following `path()` calls to `polls/urls.py`:

```python
from django.urls import path

from . import views

urlpatterns = [
    # ex: /polls/
    path('', views.index, name='index'),
    # ex: /polls/5/
    path('<int:question_id>/', views.detail, name='detail'),
    # ex: /polls/5/results/
    path('<int:question_id>/results/', views.results, name='results'),
    # ex: /polls/5/vote/
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

Now when the browser points to `/polls/5`, Django will first reference the `/polls/` path from `mysite.urls` and follow its direction the the polls module. It will then recognize the `<int:question_id>/` pattern from `polls.utrls` and run the `detail()` method from `polls.views`.

### Make yourself useful and do something

A view can either return an `HttpResponse` object with the requested page content, or it can raise an exception such as `Http404`; it is not necessary to have it read records from a database, though it certainly can. External libraries can allow Python to, for example, generate PDF files or JSON responses; but none are needed to return an HTML document.

Let's replace the `index()` view (the "Hello world" page) with one that shows a comma-separated list of the five most recent questions in the poll system:
_polls/views.py_

```python

from django.http import HttpResponse

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)

  # ...and so on
```

### Create a template for the view

Right now, the `index()` view uses a very basic design that's been hard-coded into it. Django provides a templating system that allows the design to be separated from the view for more design flexibility.

By default, Django will look for these templates in an apps `/templates/` subdirectory, which must be manually created. Within that, another subdirectory named after the app (i.e., `/polls`) should be created to ensure Django uses the correct template rather than, for example, a template with the same file name from a different app.

The template for the `index()` view will be an HTML document with bits of Python logic added in. Make the appropriate directories and touch `index.html`, putting the following in the `<body>` of the document:
_polls/templates/polls/index.html_

```python
# ...
<body>
  {% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
      <li><a href="/polls/{{question_id}}/">{{question.question_text}}</a></li>
    {% endfor %}
    </ul>
  {% else %}
    <p>No polls are available.</p>
  {% endif %}
</body>
```

Now, update the `index()` view to use the template:
_polls/views.py_

```python
from django.http import HttpResponse
from django.template import loader

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```

`loader` selects the proper template, then passes it a context - that is, a dictionary with variable names mapped to Python objects. The template engine then creates a list item for each object (following the logic in the template) and `index()` returns the final HTML document as an `HttpResponse`.

### I know a shortcut!

Because it's so common to load a template, fill a context, and return an `HttpResponse` with a rendered HTML document, Django provides a shortcut. The above `index()` view can be rewritten as:
_polls/views.py_

```python
from django.shortcuts import render

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

Note that the `render()` function includes import statements for `loader` and `HttpResponse`; if no other views use either of these, it is unnecessary to import them separately from `render`

### Handling 404s

We'll now create a view for the question detail - that is, the page that shows a poll's question text. This view will also return a custom 404 message if the user tries to access a `question_id` that does not exist.

Make the following changes to `views.py`
_polls/views.py_

```python
from django.http import Http404
from django.shortcuts import render

from .models import Question
# ...
def detail(request, question_id):
  try:
    question=Question.objects.get(pk=question_id)
  except Question.DoesNotExist:
    raise Http404("Question does not exist")
  return render(request, 'polls/detail.html',{'question': question})
```

Of course, a new `detail.html` template needs to be created for the view. For now, keep it basic just to get it working:
_polls/templates/polls/detail.html_

```python
{{ question }}
```

### I know another shortcut!

Most of the time, we'll want a failed `GET` request to raise a 404 error, so Django has a shortcut for the `try:except` block above called `get_object_or_404`:
_polls/views.py_

```python
from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
  question=get_object_or_404(Question, pk=question_id)
  return render(request, 'polls/detail.html',{'question':question})
```

`get_object_or_404()` takes the target model (`Question`) as its first argument, then any number of keyword arguments, which it passes to the model manager's `get()` function; if the object doesn't exist, it raises a 404 error.

There's a similar `get_list_or_404()` shortcut, but it uses the `filter()` function rather than `get()`
