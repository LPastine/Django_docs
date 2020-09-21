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

### Views

- Update index - latest_question_list/context/render.
- Make detail view raise a 404 error using a shortcut/render
- Create basic results and vote views.

### URLs

- Wire the views into the polls.urls.
- Change the detail url and add 'specifics' to it.
- Namespace polls app urls.

### Templates

- Create templates folder

index.html
- Display ul of latest_question_list
- Remove harcoded URLs in templates
- Change the index.html file in order to be namespaced.

detail.html
- Display the question as a heading
- Display a list of choices.

# Django Tutorial 4

## Forms

- Write a form element in detail.html
>polls/detail.html
```html
<h1>{{ question.question_text }}</h1>

{% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}

<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
{% for choice in question.choice_set.all %}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
{% endfor %}
<input type="submit" value="Vote">
</form>
```
- Implement vote() function.
>polls/views.py

```python
from django.http import HttpResponse, HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse

from .models import Choice, Question
# ...
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        # Redisplay the question voting form.
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        # Always return an HttpResponseRedirect after successfully dealing
        # with POST data. This prevents data from being posted twice if a
        # user hits the Back button.
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```

- Write the results view
>polls/views.py
```python
from django.shortcuts import get_object_or_404, render


def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {'question': question})
```
- Create results.html
>polls/results.html
```html
<h1>{{ question.question_text }}</h1>

<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
{% endfor %}
</ul>

<a href="{% url 'polls:detail' question.id %}">Vote again?</a>
```
As we can perceive in the views.py code there's a pattern in how Web Development works: we get data from the database according a parameter passed in the URL; we load a template; we return a rendered the template.

Convert views.py to use generic views.
- Convert the URLconf.
- Delete some of the old, unneeded views.
- Introduce new views based on Django's generic views.

>polls/urls.py
```python
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
    path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```
>polls/views.py
```python
from django.http import HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from django.views import generic

from .models import Choice, Question


class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5]


class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'


class ResultsView(generic.DetailView):
    model = Question
    template_name = 'polls/results.html'


def vote(request, question_id):
    ... # same as above, no changes needed.
```

Two generic views:

- ListView = abstracts the concept of displaying a list of objects
- DetailView = abstracts the concept of displaying a detail page for a particular type of object.

Each generic view needs to know what model it will be acting upon. This is provided using the model attribute.

We've change question_id to pk in urls because generic views expect this.

## Excercise 5

### Views

- Implement vote() function.
- Use generic views for Index, Detail and Result views.


### URLs

- Change URLconfs for generic views.

### Templates

- Write a form element in detail.html
- Create results.html

# Django Tutorial 5

## Tests

The testing system will automatically find tests in any file whose name begins with test.

- Create a test for future_questions.

>polls/tests.py
```python
import datetime

from django.test import TestCase
from django.utils import timezone

from .models import Question


class QuestionModelTests(TestCase):

    def test_was_published_recently_with_future_question(self):
        """
        was_published_recently() returns False for questions whose pub_date
        is in the future.
        """
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)
```

- Run test
```bash
python manage.py test polls
```

- Fix the bug
>polls/models.py
```python
def was_published_recently(self):
    now = timezone.now()
    return now - datetime.timedelta(days=1) <= self.pub_date <= now
```

- Create two other tests for the two other possible scenarios.
>polls/test.py
```python
def test_was_published_recently_with_old_question(self):
    """
    was_published_recently() returns False for questions whose pub_date
    is older than 1 day.
    """
    time = timezone.now() - datetime.timedelta(days=1, seconds=1)
    old_question = Question(pub_date=time)
    self.assertIs(old_question.was_published_recently(), False)

def test_was_published_recently_with_recent_question(self):
    """
    was_published_recently() returns True for questions whose pub_date
    is within the last day.
    """
    time = timezone.now() - datetime.timedelta(hours=23, minutes=59, seconds=59)
    recent_question = Question(pub_date=time)
    self.assertIs(recent_question.was_published_recently(), True)
```

Django provides a test Client to simulate a user interacting with the code at the view level. We can use it in tests.py or the shell.

- Don't show polls that have a pub_date in the future.
>polls/views.py
```python
from django.utils import timezone

def get_queryset(self):
    """
    Return the last five published questions (not including those set to be
    published in the future).
    """
    return Question.objects.filter(
        pub_date__lte=timezone.now()
    ).order_by('-pub_date')[:5]
```
- Create test for views.
>polls/tests.py
```python
from django.urls import reverse

def create_question(question_text, days):
    """
    Create a question with the given `question_text` and published the
    given number of `days` offset to now (negative for questions published
    in the past, positive for questions that have yet to be published).
    """
    time = timezone.now() + datetime.timedelta(days=days)
    return Question.objects.create(question_text=question_text, pub_date=time)


class QuestionIndexViewTests(TestCase):
    def test_no_questions(self):
        """
        If no questions exist, an appropriate message is displayed.
        """
        response = self.client.get(reverse('polls:index'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])

    def test_past_question(self):
        """
        Questions with a pub_date in the past are displayed on the
        index page.
        """
        create_question(question_text="Past question.", days=-30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )

    def test_future_question(self):
        """
        Questions with a pub_date in the future aren't displayed on
        the index page.
        """
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])

    def test_future_question_and_past_question(self):
        """
        Even if both past and future questions exist, only past questions
        are displayed.
        """
        create_question(question_text="Past question.", days=-30)
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )

    def test_two_past_questions(self):
        """
        The questions index page may display multiple questions.
        """
        create_question(question_text="Past question 1.", days=-30)
        create_question(question_text="Past question 2.", days=-5)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question 2.>', '<Question: Past question 1.>']
        )

```

- Exclude unpublished questions form DetailView

>polls/views.py
```python
class DetailView(generic.DetailView):
    ...
    def get_queryset(self):
        """
        Excludes any questions that aren't published yet.
        """
        return Question.objects.filter(pub_date__lte=timezone.now())
```

- Test DetailView

>polls/tests.py
```python
class QuestionDetailViewTests(TestCase):
    def test_future_question(self):
        """
        The detail view of a question with a pub_date in the future
        returns a 404 not found.
        """
        future_question = create_question(question_text='Future question.', days=5)
        url = reverse('polls:detail', args=(future_question.id,))
        response = self.client.get(url)
        self.assertEqual(response.status_code, 404)

    def test_past_question(self):
        """
        The detail view of a question with a pub_date in the past
        displays the question's text.
        """
        past_question = create_question(question_text='Past Question.', days=-5)
        url = reverse('polls:detail', args=(past_question.id,))
        response = self.client.get(url)
        self.assertContains(response, past_question.question_text)
```

Rules of thumb on testing:

- a separate TestClass for each model or view.
- a separate test method for each set of conditions you want to test.
- test method names that describe their function.

# Django Tutorial 6

## Frontend - Static Files

In Django we refer to images, JavaScript, or CSS necessary to render the complete web page as "Static Files"

Especially for big projects, that have several apps, we use django.contrib.staticfiles to collect the static files for each of the apps into a single location that can be served in production.

- Create a style.css

Django will look for static files there similarly to how Django finds templates in polls/templates.

STATICFILES_FINDERS setting contains a list of finders that know how to discover static files from various sources.

One of the defaults is AppDirectoriesFinder which looks for 'static' subdirectory in each of the INSTALLED_APPS.

The same as templates the structure for the directory is:
>polls/static/polls/style.css
```css
li a {
    color: green;
}
```
- Load the style in the index.html
>polls/templates/polls/index.html
```html
{% load static %}

<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}">
```

- Create a subdirectory for images and add a background image.
(polls/static/polls/images/background.gif)

- Add the image to the stylesheet.
>polls/static/polls/style.css
```css
body {
    background: white url("images/background.gif") no-repeat;
}
```
