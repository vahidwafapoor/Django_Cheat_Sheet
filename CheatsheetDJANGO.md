# H1 Django Cheat Sheet #

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

#H2 URLS and Views 

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

`from django.conf.urls import url`
    This gives us access to the function url

`from . import views`
    This explicitly imports views.py in the current folder.
url(r'^$', views.index)
Uses the url method in a way that’s very similar to the @app.route method in Flask. The r after the ( tells Python to interpret the following as a raw string, so it won't escape any special characters -- useful when dealing with regex strings! We'll talk about regex in a bit more depth in the next module.
In this case, our regex will exactly match an empty string. That means if you were to go to localhost:8000/, Django (after removing the '/' automatically) will check if your url matches any of the patterns in the urlpatterns list.
In this case, it does! An empty string is what r'^$' looks for. Since the pattern matches, we run the views.index method.
url() will eventually take another parameter, something like name='index', which we’ll introduce when we talk about named routes.
Note that, unlike a Flask route where there is an HTTP method (e.g. “GET” and/or “POST”), Django doesn’t care. We (the developer) figure that out in the method by accessing request.method in our function.

