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

## Excercise

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

