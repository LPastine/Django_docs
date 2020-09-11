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

## Exercise

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