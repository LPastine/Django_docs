# Django Documentation

# Django Tutorial 1

## Setup Github Remote Repository

- Create  Repo in GitHub
- Create a folder in your PC for your project
- Access folder in terminal
- Follow instructions in GitHub to create repo in terminal

## Installing Django

- Create a virtual environment

```bash
python -m venv venv
source venv/bin/activate
```

- Install Django in venv

```bash
python -m pip install Django
python -m django --version
```

## Create Project

```bash
django-admin startproject mysite
```
<br>

Notes about files in Default Project:

- Outer **mysite** => root directory/container for the project
- **manage.py** => command-line utility that lets you interact with the project
- Inner **mysite** => Python package for the project which will allow to import anything inside of it
- **settings.py** => setting for the project
- **urls.py** => 'table of contents' of the site
- **asgi.py** => entry point for ASGI web servers
- **wsgi.py** => entry point for WSGI web servers

WSGI = Web Server Gateway Interface
ASGI = Asynchronous Server Gateway Interface

ASGI requests are becoming the norm in Javascript land (HTTP/2 - Websockets), especially if you want to use React, Angular or Vue for the frontend.

## Create Polls App

- Run the server - You need to be inside the mysite directory.

```bash
python manage.py runserver
```

- Create Poll App - You need to be in the same directory as **manage.py**

```bash
python manage.py startapp polls
```

- Write Polls Views

>polls/views.py

```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

The View is responsible for producing the contents of the page, and this view needs to be associated to a URL. In order to match views and URLs we need a URLconf that will tell which view code is associated to which URL. 

<br>

- Create a URLconf for the Polls View

>polls/urls.py
```python
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

The path() function receives four arguments:

**route** => string that contains an URL pattern. When processing a request Django starts at the first pattern in **urlpatterns** trying to match the requested URL to each of the patterns until one matches.
<br>
**view** => when Django finds a matching pattern it calls the specific view function associated to it.
<br>
**kwargs**(optional) => arbitrary keyword arguments.
<br>
**name**(optional) => naming your URL lets you refer to it unambigously from elsewhere.

<br>

- Point the root URLconf at the **polls.urls** module.

>mysite/urls.py
```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```
The include() function allows referencing other URLconfs. Whenever Django encounters include(), it chops off whatever part of the URL matched up to that point and sends the remaining string to the included URLconf.

- Run the server again

## Excercise 1

1) Create Project
2) Run the server
3) Create Poll App
4) Write Polls Views
5) Create a URLconf for the Polls View
6) Point the root URLconf at the **polls.urls** module.
7) Run the server again

<br>

# Django Tutorial 2

## Database Setup

<br>

If you plan to use Django's database API, you'll need to make sure a database server is running. Django is officially supported with PostgreSQL, MariaDB, MySQL, Oracle, SQLite.

## Installing Postgres

<br>

- Check if ubuntu is updated
```bash
sudo apt update
```

<br>

As Google dropped support for 32-bit Chrome on Linux it triggers an error when updating apt in 64-bit systems with multiarch enabled.
- Confirm you are using 64 bit ubuntu with multiarch enabled issue
```bash
dpkg --print-foreign-architectures
> i386
dpkg --print-architecture
> amd64
sudo dpkg --remove-architecture i386
```

<br>

- Add Postgres APT repository
```bash
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install postgresql-12
```

<br>

- Install postgresql with contrib package and pgadmin4
```bash
sudo apt install postgresql-contrib pgadmin4
```

<br>

- Install psycopg2 that is an adapter for python.
```bash
pip install psycopg2-binary
```

<br>

- Create password for default user

```bash
$ sudo -i -u postgres
postgres@ALTER:$ psql
postgres@ALTER:$ alter user postgres with password 'password';
\q
exit
```

<br>

- Visualize databases
```bash
sudo -i -u postgres
psql -l
```

- Create a role. The role will have a database with the same name that it will be able to access.
```bash
postgres@server:~$ createuser --interactive

Enter name of role to add: mysite
Shall the new role be a superuser? (y/n) y
```

- Create database
```bash
postgres@server:~$ createdb mysite
```

By default the apps in INSTALLED_APPS need a database to work. 

- To create the tables run this command.

```bash
python manage.py migrate
```

<br>

## Creating models

A model is the single, definitive truth about your data. It contains the essential fields and behaviors of the data you're storing. The goal is to define your data model in one place and automatically derive things from it.

- Create the polls models.

>polls/models.py
```python
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

Each model is represented by a class that subclasses django.db.models.Model

Each model has class variables that are instances of a Field class, that tells Django what data type each field holds.

A relationship is defined between these classes using **ForeignKey**, that tells Django each **Choice** is related to a single **Question**.

- Add the Polls App to INSTALLED_APPS

>mysite/settings.py
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

Migrations are how Django stores changes to your models.

- Make migrations for polls app.

```bash
python manage.py makemigrations polls
```

If you want to see what it's happening in SQL.
```bash
python manage.py sqlmigrate polls 0001
```

- Run migrate to to create the polls tables in your database.
```bash
python manage.py migrate
```
The migrate command takes all the migrations that haven't been applied and runs them against your database.

3 step guide for model changes:
- Change your models (in models.py)
- Run python manage.py makemigrations to create migrations for those changes
- Run python manage.py migrate to apply those changes to the database.

## Exercise 2

 - Create the polls models.
- Add the Polls App to INSTALLED_APPS
- Make migrations for polls app.
- Apply migrations to database

## Playing with the API

- Invoke python shell
```bash
python manage.py shell
```
It shows a little excercise with the API, and it's pretty cool because you interact with the API in a OOP way.

- Adjust models so that we receive a better text representation of the data stored in the objects.

>polls/models.py
```python
from django.db import models

class Question(models.Model):
    #...
    def __str__(self):
        return self.question_text
    
class Choice(models.Model):
    #...
    def __str__(self):
        return self.choice_text
```

It's important to add __str__() methods to your models, not only for your own convenience when dealing with the interactive prompt, but also because objects' representations are used throughout Django's automatically-generated admin.

- Create a method to Question model

>polls/models.py
```python
import datetime

from django.db import models
from django.utils import timezone

class Question(models.Model):
    #...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```

## Django Admin

The admin isn't intended to be used by site visitors. It's for site managers.

- Create an admin user

```bash
python manage.py createsuperuser
```
```bash
Username: <adminname>
Email adress: <admin@example.com>
```
- Start development server
```bash
python manage.py runserver
```
and enter in:
<br>
http://127.0.0.1:8000/admin/

- Make the poll app modifiable in the admin.

We ned to tell the admin that Question objects have an admin interface.

>polls/admin.py
```python
from django.contrib import admin

from .models import Question

admin.site.register(Questions)
```

## Exercise 3

 - Adjust models so that we receive a better text representation of the data stored in the objects.
- Create a method to Question model
- Start development server
- Make the poll app modifiable in the admin.

# Django Tutorial 3

## Views

- Writing more views(detail, results, vote)
>polls/views.py
```python
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```
- Wire these views into the polls.urls.

>polls/urls.py
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

Each view is responsible for doing one of two things: returnin an HttpResponse object containing the content for the requested page, or raising an exception such as Http404. The rest is up to you.

- Change index() view so that it displays the latest 5 poll questions in the system, separated with commas, according to the publication date.

>polls/views.py
```python
from django.http import HttpResponse

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)

# Leave the rest of the views (detail, results, vote) unchanged
```

Note: the page's design is hard-coded in the view. If you want to change the way the page looks, you'll have to edit this Python code. Therefore, we use Templates to separate design from Python by creating a template Python can use.

- Create a directory called templates in your polls directory.

You can configure in TEMPLATES settings how Django will look for templates, but by default it will look for a file "templates" inside each app.

- Inside the templates directory create another directory called polls.
- Inside this directory create a file called index.html
- Write the index.html code

>polls/templates/polls/index.html
```html
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```

- Update index view in polls/views.py to use the template
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

The code loads the template and passes it a context. The context is a dictionary mapping template variable names to Python objects.

It's very common to load a template, fill a context, and return an HttpResponse object with the result of the rendered template. Django provides a shortcut using render().

The render() function takes the request object as its first argument, a template name as its second argument and a dictionary as its optional third argument. Then, it returns an HttpResponse object of the given template rendered with the given context.

- Apply render() to views.py for index(). Remove loader but keep HttpResponse for the other functions.
``` python
from django.shortcuts import render

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

- Make the view raise a 404 error if the requested ID doesn't exist.

>polls/views.py
```python
from django.http import Http404
from django.shortcuts import render

from .models import Question
# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```
- Write a quick template for the detail view.

>polls/templates/polls/detail.html
```html
{{ question }}
```

- Create a shortcut for rasing 404 error.
>polls/views.py
```python
from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

- Refine the detail.html
>polls/templates/polls/detail.html
```html
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```
- Remove harcoded URLs in templates

>polls/index.html
```html
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```
Django looks up the URL definition as specified in polls.urls module.

- Change the detail url and add 'specifics' to it.
>polls.urls.py
```python
path('specifics/<int:question_id>/', views.detail, name='detail')
```
In real Django projects there are several apps, which may have equal url names between them, so in order to differentiate which url is associated with each app, when we use {% url %} template tag, we can Namespace the urls.

- Namespace polls app urls.
>polls/urls.py
```python
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/results/', views.results, name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

- Change the index.html file in order to be namespaced.
>polls/templates/polls/index.html
```html
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```

## Excercise 4

- Writing more views(detail, results, vote)
- Wire these views into the polls.urls.
- Change index() view so that it displays the latest 5 poll questions in the system, separated with commas, according to the publication date.
- Create a directory called templates in your polls directory.
- Inside the templates directory create another directory called polls.
- Inside this directory create a file called index.html
- Write the index.html code
- Update index view in polls/views.py to use the template
- Apply render() to views.py for index(). Remove loader but keep HttpResponse for the other functions.
- Make the view raise a 404 error if the requested ID doesn't exist.
- Write a quick template for the detail view.
- Create a shortcut for rasing 404 error.
- Refine the detail.html
- Remove harcoded URLs in templates
- Change the detail url and add 'specifics' to it.
- Namespace polls app urls.
- Change the index.html file in order to be namespaced.