# First Python Project

## Default project structure
```commandline
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```
- The outer `mysite/` root directory is a container for your project. Its name doesn’t matter to Django (can be renamed).
- `manage.py`: Command line utility letting you interact with this Django project.
- The inner `mysite/` directory is the actual Python package for your project. Used to import things (e.g. mysite.urls).
- `mysite/__init__.py`: An empty file telling Python this directory should be considered a Python package.
- `mysite/settings.py`: Settings/configuration for this Django project. 
- `mysite/urls.py`: The URL declarations for this Django project. (like routes.rb)
- `mysite/asgi.py`: An entry-point for ASGI-compatible web servers to serve your project.
- `mysite/wsgi.py`: An entry-point for WSGI-compatible web servers to serve your project.

## Creating a Project
```
$ django-admin startproject mysite
```

## Running Project
```commandline
$ python manage.py runserver

# Specify different port
$ python manage.py runserver 8080
```

## Creating an app
> [!NOTE]
> Difference between a project & app
> App - web application that does something.
> Project - collaboration of configuration and apps for a particular website.
> A project can contain multiple apps. An app can be in multiple projects.

```
$ python manage.py startapp polls
```

* Creates a directory *polls*
```
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```

## Writing my first view
> [!NOTE]
> A **View** in Django is the same as a **Controller** in Rails.
* In `views.py` we create an index method - the simplest possible view in Django.
* To call the view, it needs to be mapped to a URL. This is done by a `URLconf`.
    * Create a file called `urls.py` in the polls directory.
* Lastly, point the root URLconf to the **polls.urls.py**.
    * In **mysite/urls.py**, add an import for `django.urls.include` and and insert an `include()` in the urlpatterns list.
```
# mysite/urls.py
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("polls/", include("polls.urls")),
    path("admin/", admin.site.urls),
]      
```
* `include()` function allows referencing other URLconfs.
