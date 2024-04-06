<!-- README.md{% comment %} -->
# Django Project Package Template

This is a template for a [Django](https://www.djangoproject.com/) project >=4.x but should still work on newer versions.

The project has a layout so that it can be build as a [wheel](https://github.com/pypa/wheel), one type of [Python packages](https://pypi.org/help/#packages).

## Features

* The version is defined in `{{ project_name }}.__version__`.
* Source code is located in a `src` directory to prevent side effects.
* All apps use the `{{ project_name }}.apps` namespace.
* All configuration modules use the `{{ project_name }}.conf` namespace.
* Environment variables are configured via [python-decouple](https://github.com/HBNetwork/python-decouple)
* All templates, static files and locales will be included in the wheel.
* [Django Debug Toolbar](https://github.com/jazzband/django-debug-toolbar),
  [IPython](https://ipython.org) are already added to the development dependencies.
* [Django Extensions](django-extensions/django-extensions) has been added to installed apps by
  default

## Usage

Use the following [startproject](https://docs.djangoproject.com/en/stable/ref/django-admin/#django-admin-startproject) command to create a new project using this template:

```console
python3 -m django startproject --extension=gitignore,gitkeep,md,toml \
    --exclude=docs \
    --template=https://github.com/yujinyuz/django-as-a-package-layout-template/archive/master.zip \
    <project_name> [directory]
```

_Tip: If you want to create the project in your current working directory use `.` as directory argument._

## License

Distributed under the [MIT License](https://opensource.org/licenses/MIT).

Copyright 2024 Eugene Oliveros

## Acknowledgements

- [keimlink/django-project-package-template](https://github.com/keimlink/django-project-package-template)

  This template was based from this repository but I made a few changes to conform with new build
  packages such as hatch instead of setuptools, pyproject.toml instead of setup.cfg

_All text below the horizontal line is the template for the new project's README._

---
<!-- {% endcomment %} -->

# {{ project_name|title }}

Describe your project in one sentence.

## Quickstart

Install the project and the development dependencies into a [virtual environment](https://docs.python.org/3/tutorial/venv.html):

```console
python3 -m venv .venv
source .venv/bin/activate

python3 -m pip install --editable ".[dev]"

./manage.py migrate
./manage.py createsuperuser
./manage.py runserver
```

## Starting a New App

Since we store all our apps inside `src/` folder, we need to create the app directory beforehand.

Run this command to create the app directory

```console
mkdir src/{{ project_name }}/apps/<app_name>
```

Then pass the path to the new directory to the [startapp](https://docs.djangoproject.com/en/{{ docs_version }}/ref/django-admin/#django-admin-startapp) command:

```console
./manage.py startapp name src/{{ project_name }}/apps/<app_name>
```

## Deployment

The following list describes only the absolute necessary steps to outline a deployment for a Django project wheel. For example a component to serve static files is missing - you could use [WhiteNoise](https://github.com/evansd/whitenoise/) to do this.

Also see [How to use Django with Gunicorn](https://docs.djangoproject.com/en/{{ docs_version }}/howto/deployment/wsgi/gunicorn/) and [Deployment Checklist](https://docs.djangoproject.com/en/{{ docs_version }}/howto/deployment/checklist/) for more information.

1.  Add your favorite WSGI HTTP server, e.g.  [Gunicorn](https://gunicorn.org/), to `project.dependencies` in `pyproject.toml`.
2.  [Build](https://packaging.python.org/tutorials/packaging-projects/#generating-distribution-archives) a [wheel](https://github.com/pypa/wheel) of the project.
    ```console
    pip install build
    python -m build
    ```
4.  Copy the wheel file from the `dist` directory to the server to be deployed.
5.  Create a minimal configuration on the server using environment variables.
    ```bash
    export DJANGO_SETTINGS_MODULE={{ project_name }}.conf.settings
    export DJANGO_ALLOWED_HOSTS=www.example.com
    export DJANGO_DEBUG=False
    ```
6.  Install the wheel and [collect the static files](https://docs.djangoproject.com/en/{{ docs_version }}/ref/contrib/staticfiles/#django-admin-collectstatic):
    ```console
    python3 -m pip install --find-links=/path/to/wheel_dir {{ project_name }}
    django-project collectstatic --no-input
    ```
7.  Start Gunicorn like this:
    ```console
    gunicorn {{ project_name }}.wsgi
    ```
