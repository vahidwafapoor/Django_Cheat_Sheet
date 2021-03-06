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
Uses the url method in a way that’s very similar to the `@app.route` method in Flask. The `r` after the `(` tells Python to interpret the following as a raw string, so it won't escape any special characters -- useful when dealing with regex strings! We'll talk about regex in a bit more depth in the next module.
In this case, our regex will exactly match an empty string. That means if you were to go to `localhost:8000/`, Django (after removing the `'/'` automatically) will check if your url matches any of the patterns in the urlpatterns list.
In this case, it does! An empty string is what `r'^$'` looks for. Since the pattern matches, we run the `views.index` method.
`url()` will eventually take another parameter, something like `name='index'`, which we’ll introduce when we talk about named routes.
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

- `'^'` Matches the following characters at the beginning of a string. Example: '^a' matches 'anna' but not 'banana'.
- `'$'` Matches the previous characters at the end of a string. Example: 'a$' matches 'anna' and 'banana' but not 'fan'.
- `'[]'` Matches any value in a range. Example: '[0-9]' matches '9' and '9s'.
- `'{n}'` Matches n number or more repetitions of the preceding pattern. Example: '[0-9]{2}' matches '91' and '9834' but not '9'
- `\d` Matches digits.  Example: "\d" matches "8" and "877x"
- `\d+` matches a string with one or more digits
- `\w` Matches characters.
- `\w+` matches a string with one or more character/word
All of the above examples are pretty simple. You can combine any of the above patterns to match complex strings. You'll see lots of examples of regex in Django urls, but they follow a simple pattern.

Use Python's regex documentation as a reference if you need anything more complex than the above. You most likely won't. If you're curious and you have some spare time try Regex One or Regex 101 to practice your patterns.

#### Examples of Regular Expression
- `url(r'^articles/(?P\d+)$', views.show)`
- `url(r'^articles/(?P\w+)$', views.show_word)`
- `url(r'^articles/2003/$', views.special_case_2003)`
- `url(r'^articles/(?P[0-9]{4})$', views.year_archive)`
- `url(r'^articles/(?P[0-9]{4})/(?P[0-9]{2}$', views.month_archive)`

## Templates and Static Files Review

In our views file, Django knows to look in the templates folder first for whatever path we pass to our render method.
```python
# views.py
from django.shortcuts import render, HttpResponse, redirect
def index(request):
    context = {
        "email" : "blog@gmail.com",
        "name" : "mike"
    }
    return render(request, "ourApp/index.html", context)
```
So this code actually tells the index method to look in templates/ourApp and then at the `index.html` file.

The behavior for static files is very similar: Django’s template rendering system knows (if it's told) to look in the static and then at the path for the specific file relative to that folder!

What this looks like in HTML is:
```HTML
<!DOCTYPE html>
  <html>
    <head>
      <meta charset="utf-8">
      <title>Index</title>
      {% load static %}
      <!-- The line above tells Django to be ready to listen for static files -->
      <link rel="stylesheet" href="{% static 'ourApp/css/main.css' %}">
      <!-- Put the static files in the static folder inside your app.  
           Django collects files within all static folders and puts them within a single folder -->    
    </head>
    <body>
        <h1>Hello World!</h1>
        <p>{{email}} and {{name}}</p>
    </body>
  </html>
```
In other words, loading static files, whether it's CSS or JavaScript or images, will start the search path inside of ourApp’s static folder, and after that, the rest of that path is up to us! A good habit to get into is separating our JavaScript, CSS, and images into three folders (and specifying which app we are retrieving these files from, as outlined in the structure at the top of this tab!

Note the Django templating call inside the link tag too!

References
[Django Documentation](https://docs.djangoproject.com/en/1.11/howto/static-files/)

## Form Data

As you’ve already seen, getting information from a user via forms is an extremely important part of web development. When we were using Flask, we used the form property of the request object to access input values.

Django behaves very similarly, except the property is `request.POST` if the method that hits the route is a `POST` and `request.GET` if the method that hits the route is a `GET`.

We are adding a form to `.../blogs/templates/blogs/index.html`
```HTML
<form action="/blogs/create" method="post">
  {% csrf_token %}
  <label for="name">Blog Name:</label>
  <input type="text" id="name" name="name" placeholder="blog name">
  <label for="desc">Description:</label>        
  <textarea id="desc" name="desc" placeholder="description"></textarea>
  <button type="submit">Submit</button>
</form>
```
Don't forget to add the new url to `.../blogs/urls.py`:
```python
url(r'^create$', views.create)
```
Which means we'll need the create() function in `.../blogs/views.py`

```python
from django.shortcuts import render, HttpResponse, redirect
def create(request):
    if request.method == "POST":
        print("*"*50)
        print(request.POST)
        print(request.POST['name'])
        print(request.POST['desc'])
        request.session['name'] = "test"  # more on session below
        print("*"*50)
        return redirect("/")
    else:
        return redirect("/")
```

### Key Terms
- `request.POST`
Data from POST request
- `request.GET`
Data from GET request
- `request.method`
Returns the method/HTTP verb associated with the request
- `{% csrf_token %}`
Prevents cross-site request forgery (place it in a form on the HTML/template side of your project)


## Session

Now let’s set up to use session!

In our terminal, head to the directory where `manage.py` resides and run the following commands:
```
   # Need to be in same directory as manage.py file
   > python manage.py makemigrations
   > python manage.py migrate
```
Excellent. Not only does that annoying warning you’ve been suffering disappear, but now session is now available to us (as seen in that last line: Applying sessions.0001_initial... OK).

Now we can restart our server and use session:
```
request.session` # It's a dictionary, so you can attach key/value pairs
```

As request.session is just a dictionary, you can attach any key/value pairs.  For example, in the views.py file, you could do:
```
request.session['name'] = request.POST['name']
request.session['counter'] = 100
```
In the html file, Django lets you directly access request.session.  For example to access request.session['name'], simply do:

`{{request.session.name}}`

### Useful session methods:
- `request.session['key']`
-- This will retrieve (get) the value stored in key
- `request.session['key'] = 'value'`
-- Set the value that will be stored by key
- `del request.session['key']`
-- Deletes a session key if it exists, throws a keyError if it doesn’t. Use along with try and except since it’s better to ask for forgiveness than permission
- `'key' in request.session`
-- Returns a boolean of whether a key is in session or not
- `{{ request.session.name }}`
-- Use dot notation (.) to access request.session keys from templates since square brackets ([]) aren’t allowed there

## ORM's

We've mentioned ORM's previously, and your instructor may have even explained a little more. Let's take a moment to get a better understanding of what an ORM does.

ORMs are used to create a language-specific object oriented representation of a table. When tables are objects, attributes of these objects represent the columns in the database, while methods will correspond to common queries.

If the terms methods and attributes don't sound familiar to you, go back and review that section from OOP.

The reason that ORMs are useful is so that we can write pure Python code without having to manage long SQL query strings in our logic. You know from experience how ugly SQL queries can get when doing complex selects. Given clearly named table methods our code becomes much more clear and easy to read with the help of an ORM.

Next, we'll get deeper into models and how they work before giving you the tools to try them out!

Models in the MTV architecture do all the work of data manipulation. They control which data are released to the controllers. Because of this, the phrase skinny controllers and fat models is often used, and is an important design principle:

Any heavy logic including database queries is the purview of a Model. If a Controller (in Django's case, a View) needs to perform logic or get information from a database, it should use a Model method to do so.
Blogs, Comments, Admins Example
For our blogs app, let's say that we wanted people to not only create blogs but also add comments.  Let's also allow a blog to be owned by multiple admins and where an admin can also have multiple blogs (in other words, a many to many relationship).

Note that a blog can have many comments (one to many relationship) and a blog can have many admins as well as one admin having multiple blogs (many to many relationship). 

To implement this in Django, you would have the following code in your models.py:  (as Django likes to keep the model class singular, we'll follow their convention)

```python
# Inside models.py
from __future__ import unicode_literals
from django.db import models
# Create your models here.
class Blog(models.Model):
      name = models.CharField(max_length=255)
      desc = models.TextField()
      created_at = models.DateTimeField(auto_now_add = True)
      updated_at = models.DateTimeField(auto_now = True)
  class Comment(models.Model):
      comment = models.CharField(max_length=255)
      created_at = models.DateTimeField(auto_now_add = True)
      updated_at = models.DateTimeField(auto_now = True)
      # Notice the association made with ForeignKey for a one-to-many relationship
      # There can be many comments to one blog
      blog = models.ForeignKey(Blog, related_name = "comments")
  class Admin(models.Model):
      first_name = models.CharField(max_length=255)
      last_name = models.CharField(max_length=255)
      email = models.CharField(max_length=255)
      blogs = models.ManyToManyField(Blog, related_name = "admins")
      created_at = models.DateTimeField(auto_now_add = True)
      updated_at = models.DateTimeField(auto_now = True)
```
That `ForeignKey` statement is the equivalent of the one-to-many relationship shown in the ERD diagram.

### Column types
You can find a full list of allowed column types in the documentation, but here are some of the main ones you'll be using.

- `CharField`

Any text that a user may enter. This has one additional required parameter, max_length, that (unsurprisingly) is the maximum length of text that can be saved.

- `TextField`

Like a CharField, but with no maximum length. Your user could copy the entire text of the Harry Potter series into the field, and it would save in the database correctly.

- `IntegerField`, `BooleanField`

Holds integers or booleans, respectively

`DateTimeField`

Used for date and times, such as timestamps or when a flight is scheduled to depart. This field can take two very useful optional parameters, auto_now_add=True, which adds the current date/time when object is created, and auto_now=True, which automatically updates any time the object is modified.

`ForeignKey`, `ManyToManyField`, `OneToOneField`

Used to indicate a relationship between models (anything that would require a JOIN statement in SQL). ForeignKey is for one-to-many relationships and goes in the model on the "many" side, just as the foreign key column goes in the SQL table on the "many" side.

### Migrations

Now think back to Flask. Creating an ERD diagram didn't actually create the database table(s). We had to forward engineer the diagram into SQL code, and then run that SQL code in a MySQL environment. Instead of using a separate program to do this Django can do the whole job for us with minimal code.

To do the equivalent of forward engineer we are going to run a couple of commands from the terminal.

```
  > python manage.py makemigrations
  > python manage.py migrate
```
makemigrations is a kind of staging. When this command runs Django is looking at the code we wrote, finding any changes, and formulating the correct Python code to move on to the next step.

migrate actually applies the changes made above. This step is where the SQL queries are actually built and executed and is equivalent to forward engineering an ERD.

The migration process is split into two steps so that Django can check and make sure you wrote code it can understand before moving on to the next step.

### Note:

1. Never delete migration files, and always makemigrations and migrate anytime you change something in your models.py files – that's what updates the actual database so it reflects what's in your models.

2. For now we are going to be using SQLite - a SQL database that comes packaged with Django and has much of the functionality of MySQL (but not all of it) and is stored as local memory in a file, and as such is very fast. This type of database is best used in development, like we're doing now.

3. For deployment you'll have the option to use SQLite, but we'll also show you how to add a MySQL database, which you've already used with Flask.

4. Django ORM models and queries will always be the same no matter which database we are using.

Now we've seen how to build a model and migrate in order to translate our code to SQL.

## ForeignKey Relationships 

To show a one-to-many relationship between models, Django uses a special field, 'ForeignKey'. Consider these models: 

```python
class Author(models.Model):
    name = models.CharField(max_length=255)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
class Book(models.Model):
    title = models.CharField(max_length=255)
    author = models.ForeignKey(Author, related_name="books")
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```
Notice that the `Book` model has this line: `models.ForeignKey(Author, related_name="books")`. This establishes a one-to-many relationship, that one author can have many books, and that information about a book's author can be accessed as `some_book.author`. To create a record that has this foreign key relationship, you'd pass it into the `create` function, like with any other field:

```python
this_author = Author.objects.get(id=2)
my_book = Book.objects.create(title="Little Women", author=this_author)
# or on one line...
my_book = Book.objects.create(title="Little Women", author=Author.objects.get(id=2))
```
Joins in Django happen automatically. If you have a book object, you don't need to run any additional query to get information about the author. my_book.author.name will give "Louisa May Alcott", in this case.

You can search based off of a ForeignKey relationship. This code will find all of the books by the author with ID 2:

```python
this_author = Author.objects.get(id=2)
books = Book.objects.filter(author=this_author)
# one-line version:
books = Book.objects.filter(author=Author.objects.get(id=2))
```
You can also search by a field in the related object by using a double underscore:

```python
books = Book.objects.filter(author__name="Louise May Alcott")
books = Book.objects.filter(author__name__startswith="Lou")
```


