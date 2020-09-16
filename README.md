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