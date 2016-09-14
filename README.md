# Django Project Boilerplate

Guide to create and setup a Django Project

## Requirements
* Python 2.7.12
* pip 8.1.2
* django 1.10
* django rest framework 3.4

## SET VIRTUAL ENVIRONMENT AND START A DJANGO PROJECT

### VIRTUALENVS

Create a folder to contain the project
```
$ mkdir django_project
```
Create a python virtualenvs for development and production environments
```
$ mkvirtualenv --python=/usr/bin/python django_dev
$ mkvirtualenv --python=/usr/bin/python django_prod
```
Install django inside the virtualenv
```
(django_dev)$ pip install django
```
### START DJANGO PROJECT
Inside project folder, create a new Django project
```
$ django-admin.py startproject django_boilerplate . # Note the trailing '.' character
$ cd django_boilerplate
$ django-admin.py startapp app
```
Go to the project folder and test the project
```
$ cd ..
$ python manage.py runserver
```

## SETTINGS FILES

### REQUIREMENTS


Create a requirements file to unify all the packages and their versions in 3 separated files for each environment.
```
$ mkdir requirements && cd requirements/
$ touch {base.txt,development.txt,production.txt}
$ pip freeze > base.txt
$ echo "-r base.txt" | tee -a development.txt production.txt
```
Install the requirements in the different virtual environments
```
$ workon django_dev
$ pip install -r requirements/development.txt
```
```
$ workon django_prod
$ pip install -r requirements/production.txt
```

### SETTINGS FOR EACH ENVIRONMENT


Inside the ```django_boilerplate``` folder create a settings folder to contain the separated files
```
$ mkdir django_boilerplate/settings && cd django_boilerplate/settings
$ touch __init__.py development.py production.py
```
Make them inherit from the base.py file
```python
# -*- coding: utf-8 -*-
from .base import *
```
Move the settings file and rename it as base.py
```
$ mv ../settings.py base.py
```
Specify to the virtual environments to work with the correct settings file (do the same for all environments)
```
$ workon django_dev
(django_dev)$ sudo nano $VIRTUAL_ENV/bin/postactivate
```
```bash
export DJANGO_SETTINGS_MODULE="django_boilerplate.settings.development"
```
```
(django_dev)$ sudo nano $VIRTUAL_ENV/bin/predeactivate
```
```bash
unset DJANGO_SETTINGS_MODULE
```

### DEBUG Variable
For the production environment you need to set the variable ```DEBUG``` to ```False```.  
Cut the ```DEBUG``` variable from *base.py* and paste it into the *development.py* file:
```
DEBUG = True
```
and into the *production.py* file:
```
DEBUG = False
```


### DJANGO SECRET_KEY

Copy the SECRET_KEY from the base.py file and copy in the specific files from the virtual environment (do the same for all environments)
```
(django_dev)$ sudo nano $VIRTUAL_ENV/bin/postactivate
```
```bash
export SECRET_KEY="your_secret_django_key"
```
```
(django_dev)$ sudo nano $VIRTUAL_ENV/bin/predeactivate
```
```bash
unset SECRET_KEY
```
Edit the base.py file, remove the SECRET_KEY  and add the following
```python
from django.core.exceptions import ImproperlyConfigured

def get_env_variable(var_name):
    try:
        return os.environ[var_name]
    except KeyError:
        error_msg = "Set the %s environment variable" % var_name
        raise ImproperlyConfigured(error_msg)

SECRET_KEY = get_env_variable('SECRET_KEY')
```
Deactivate and activate the Virtualenv and test the server to check if it's working
```
(django_dev)$ deactivate
$ workon django_dev
(django_dev)$ python manage.py runserver
```

## Credits
[Marina Mele](http://www.marinamele.com/taskbuster-django-tutorial)
