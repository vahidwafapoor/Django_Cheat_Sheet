# H1 Django Cheat Sheet 

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
