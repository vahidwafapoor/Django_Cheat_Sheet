# Django Cheat Sheet

In `settings.py` (this file lives inside the `main` directory. The main directory is alongside `apps and manage.py`) we are going to have to add our application to the project. That means adding `apps.first_app` to our `INSTALLED_APPS` list:

```python
  # Inside main/settings.py
  INSTALLED_APPS = [
      'django.contrib.admin',
      'django.contrib.auth',
      'django.contrib.contenttypes',
      'django.contrib.sessions',
      'django.contrib.messages',
      'django.contrib.staticfiles',
  ]
  # BECOMES:
   INSTALLED_APPS = [
       'apps.first_app', ### added this line!
       'django.contrib.admin',
       'django.contrib.auth',
       'django.contrib.contenttypes',
       'django.contrib.sessions',
       'django.contrib.messages',
       'django.contrib.staticfiles',
   ]
```
Go to your `main` folder anf run `python manage.py runserver` from the terminal. Then proceed to `localhost:8000`. You should see a simple page. Stop the server with Ctrl-C before moving on. 

### URLS and Views 

Django's urls are the routing part of Django. To make sure you are sending the right routes to your first app, set up the `main` project urls to make requests to your apps routing file. That file is `urls.py` and will be inside of the directory `apps/first_app`.

Now open `main/urls.py` and modify the document in order to use routes from `first_app`:

```python
from django.conf.urls import url, include  # Notice we added include  
# comment out or delete the line below, we will not be using the admin module
# from django.contrib import admin
# Why won't we use the admin module? Because we're focusing on how to code, 
# not on how to help people who do not know how to code how to manage the app.
urlpatterns = [
    path(r'^', include('apps.first_app.urls')) # And now we use the include function to pull in our first_app.urls...
]
```

The app's urls file does not exist so make it in the command line with `touch urls.py`

Then in `apps/first_app/urls.py`

```python
from django.conf.urls import url
from . import views           # This line is new!
urlpatterns = [
    url(r'^$', views.index),   # This line has changed! Notice that urlpatterns is a list, the comma is in
]                            # anticipation of all the routes that will be coming soon
```
Let's take a closer look at `urls.py`:

* `from django.conf.urls import url`
    This gives us access to the function url

* `from . import views`
    This explicitly imports views.py in the current folder.

`url(r'^$', views.index)`
Uses the url method in a way that’s very similar to the @app.route method in Flask. The r after the ( tells Python to interpret the following as a raw string, so it won't escape any special characters -- useful when dealing with regex strings! We'll talk about regex in a bit more depth in the next module.
In this case, our regex will exactly match an empty string. That means if you were to go to localhost:8000/, Django (after removing the '/' automatically) will check if your url matches any of the patterns in the urlpatterns list.
In this case, it does! An empty string is what r'^$' looks for. Since the pattern matches, we run the views.index method.
url() will eventually take another parameter, something like name='index', which we’ll introduce when we talk about named routes.
Note that, unlike a Flask route where there is an HTTP method (e.g. “GET” and/or “POST”), Django doesn’t care. We (the developer) figure that out in the method by accessing request.method in our function.

### Views
Let’s build our first views function, `index`, inside of `views.py`:

`apps/first_app/views.py`
```python
  from django.shortcuts import render, HttpResponse, redirect
  # the index function is called when root is visited
  def index(request):
    response = "Hello, I am your first request!"
    return HttpResponse(response)
```

Look back at your code in `urls.py`. You should see that we have called url, passing in a regex that will be used to attempt to find a match for an incoming HTTP request. Next, we pass in views.index. We now have access to all the functions in our views file. When the given route is visited, the code in that function executes.

In the above code we respond to an HTTP request with a simple string. It's passed to the client and displayed there. That's not going to help us much with creating a complex website. We'll need to render some HTML from some templates. Let's take a look:

#### Regular Expressions
Regular expressions, commonly known as regex, are a set of rules for identifying or matching strings. Many programming languages, including Python, support use of regular expressions for matching and searching strings.

Common uses include searching string inputs from users. Search engines and other form input, like user registration and login, are great examples of correct uses for regular expressions. As you've seen, Django uses regex to match url patterns in routing. You won't need to do anything too complex when working with Django, but you can use the following reference for some of the most common expressions you'll use.

#### A good resource for you to get familiar with Regex is [regexr.com](https://regexr.com)

### Here is a cheat sheet for expressions you'll be using regularly:

- '^' Matches the following characters at the beginning of a string. Example: '^a' matches 'anna' but not 'banana'.
- '$' Matches the previous characters at the end of a string. Example: 'a$' matches 'anna' and 'banana' but not 'fan'.
- '[]' Matches any value in a range. Example: '[0-9]' matches '9' and '9s'.
- '{n}' Matches n number or more repetitions of the preceding pattern. Example: '[0-9]{2}' matches '91' and '9834' but not '9'
- \d Matches digits.  Example: "\d" matches "8" and "877x"
- \d+ matches a string with one or more digits
- \w Matches characters.
- \w+ matches a string with one or more character/word
- All of the above examples are pretty simple. You can combine any of the above patterns to match complex strings. You'll see lots of examples of regex in Django urls, but they follow a simple pattern.

Use Python's regex documentation as a reference if you need anything more complex than the above. You most likely won't. If you're curious and you have some spare time try Regex One or Regex 101 to practice your patterns.

#### Examples of Regular Expression
- url(r'^articles/(?P\d+)$', views.show)
- url(r'^articles/(?P\w+)$', views.show_word)
- url(r'^articles/2003/$', views.special_case_2003)
- url(r'^articles/(?P[0-9]{4})$', views.year_archive)
- url(r'^articles/(?P[0-9]{4})/(?P[0-9]{2}$', views.month_archive)