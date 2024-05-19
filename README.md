# First Python Project

---
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
---

## Running Project
```commandline
$ python manage.py runserver

# Specify different port
$ python manage.py runserver 8080
```