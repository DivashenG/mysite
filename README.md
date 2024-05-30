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
---
## Database setup
* Default configuration uses SQLite.
* This is configured under `DATABASES` -> `ENGINE`.
* Default `INSTALLED_APPS` contain the following:
    * django.contrib.admin – The admin site. You’ll use it shortly.
    * django.contrib.auth – An authentication system.
    * django.contrib.contenttypes – A framework for content types.
    * django.contrib.sessions – A session framework.
    * django.contrib.messages – A messaging framework.
    * django.contrib.staticfiles – A framework for managing static files.
* To create tables in the database (ie. run migrations): 
```
$ python manage.py migrate
```
* **Migrate** command looks at **INSTALLED_APPS** setting and creates db tables according to db settings in `mysite/settings.py` file.

## Creating models
* Models are created in app directories eg.`polls/models.py`.
* Each model subclasses `django.db.models.Model`.
* Field structure:
    * <field_name> = models.<Field class/datatype>(<additionl properties>)
    * **CharField** requires you give it a `max_length`. Other fields can have similar nuances.

## Activating models
* To include apps in our project, we need to add reference to its configuration class in `INSTALLED_APPS` setting.
* `apps.py` in each app contains your AppConfig to include in `INSTALLED_APPS`.
* To prepare migrations (generate the migrations - store changes as a migration):
```
$ python manage.py makemigrations polls

# Use sqlmigrate command to see the SQL a migration will run
$ python manage.py sqlmigrate polls 0001

# To check if any problems exist on your project
$ python manage.py check

# Run migration
$ python manage.py migrate
```
* The 3 step guide to making model changes:
    *  Change your models (in `models.py`). 
    * Run `python manage.py makemigrations` to create migrations for those changes. 
    * Run `python manage.py migrate` to apply those changes to the database.

### Playing with the API
* Invoke the Python shell with: `$ python manage.py shell`
    * Using the above sets the `DJANGO_SETTINGS_MODULE` environment variable which gives Django the Python import path to your `mysite/settings.py` file.
```python
>>> from polls.models import Choice, Question  # Import the model classes we just wrote.

# No questions are in the system yet.
>>> Question.objects.all()
<QuerySet []>

# Create a new Question.
# Support for time zones is enabled in the default settings file, so
# Django expects a datetime with tzinfo for pub_date. Use timezone.now()
# instead of datetime.datetime.now() and it will do the right thing.
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# Save the object into the database. You have to call save() explicitly.
>>> q.save()

# Now it has an ID.
>>> q.id
1

# Access model field values via Python attributes.
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=datetime.timezone.utc)

# Change values by changing the attributes, then calling save().
>>> q.question_text = "What's up?"
>>> q.save()

# objects.all() displays all the questions in the database.
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]>
```
* Since `<Question: Question object (1)>` isn't human-readable, we should add a `__str__()` method.
* Now in the python shell again
```python
>>> from polls.models import Choice, Question

# Make sure our __str__() addition worked.
>>> Question.objects.all()
<QuerySet [<Question: What's up?>]>

# Django provides a rich database lookup API that's entirely driven by
# keyword arguments.
>>> Question.objects.filter(id=1)
<QuerySet [<Question: What's up?>]>
>>> Question.objects.filter(question_text__startswith="What")
<QuerySet [<Question: What's up?>]>

# Get the question that was published this year.
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
<Question: What's up?>

# Request an ID that doesn't exist, this will raise an exception.
>>> Question.objects.get(id=2)
Traceback (most recent call last):
    ...
DoesNotExist: Question matching query does not exist.

# Lookup by a primary key is the most common case, so Django provides a
# shortcut for primary-key exact lookups.
# The following is identical to Question.objects.get(id=1).
>>> Question.objects.get(pk=1)
<Question: What's up?>

# Make sure our custom method worked.
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
True

# Give the Question a couple of Choices. The create call constructs a new
# Choice object, does the INSERT statement, adds the choice to the set
# of available choices and returns the new Choice object. Django creates
# a set (defined as "choice_set") to hold the "other side" of a ForeignKey
# relation (e.g. a question's choice) which can be accessed via the API.
>>> q = Question.objects.get(pk=1)

# Display any choices from the related object set -- none so far.
>>> q.choice_set.all()
<QuerySet []>

# Create three choices.
>>> q.choice_set.create(choice_text="Not much", votes=0)
<Choice: Not much>
>>> q.choice_set.create(choice_text="The sky", votes=0)
<Choice: The sky>
>>> c = q.choice_set.create(choice_text="Just hacking again", votes=0)

# Choice objects have API access to their related Question objects.
>>> c.question
<Question: What's up?>

# And vice versa: Question objects get access to Choice objects.
>>> q.choice_set.all()
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
>>> q.choice_set.count()
3

# The API automatically follows relationships as far as you need.
# Use double underscores to separate relationships.
# This works as many levels deep as you want; there's no limit.
# Find all Choices for any question whose pub_date is in this year
# (reusing the 'current_year' variable we created above).
>>> Choice.objects.filter(question__pub_date__year=current_year)
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# Let's delete one of the choices. Use delete() for that.
>>> c = q.choice_set.filter(choice_text__startswith="Just hacking")
>>> c.delete()
```

## Introducing the Django Admin
### Creating an admin user
* Create a superuser with: `$ python manage.py createsuperuser`.
* Django Admin is available at `http://127.0.0.1:8000/admin/`.
* Groups & Users under Authentication and Authorization is provided by `django.contrib.auth` (authentication framework shpped by Django).

### Make poll app modifiable in the admin.
* Edit `<app>/admin.py` to tell admin that an object has an admin interface.
---
## Writing more views
* URLconfs maps URL patterns to views.
* To add a view, do the following:
  * add a method to `views.py`
  * add the path in `urls.py` under urlpatterns.

## Writing view that actually do something
* A view is responsible for one of 2 outputs:
  * returning `HttpResponse` object - content for the requested page.
  * reaising an exception like `Http404`.
* View can read from db, use template system and generate various outputs (eg pdf, xml, zip) using various libraries.
* `Templates` allow us avoid page designs being hardcoded in views by seperating the design.
* For templates:
  * Create a /templates directory in your app.
  * TEMPLATES in settings.py describes how Django loads and renders templates.
  * Create another directory /polls and an index.html inside so you have `polls/templates/polls/index.html`.
    * `polls/templates` can work but Django chooses the first template it finds so we want to be verbose.
* A shortcut `render()`:
  * To load template, fill a context and return `HttpResponse` use `render()`.
  *  render() function takes:
    * request object as its first argument.
    * a template name as its second argument.
    * a dictionary as its optional third argument. 
    * It returns an HttpResponse object of the given template rendered with the given context.

### Raising a 404 error
* Add following to view:
```python
...
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
...
```
* Shortcut is ` get_object_or_404()`

### Namespacing URL names
* It's important to namespace our urls in each app. 
* In an app's urls.py add an app_name.