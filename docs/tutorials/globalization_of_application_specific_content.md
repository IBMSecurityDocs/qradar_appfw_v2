# Globalizing application-specific content

Globalize application-specific content by using Python Babel, Flask Babel, and Jinja2 templates.

Use the following technologies to help you globalize your application-specific content:

- [Babel](http://babel.pocoo.org/) is the standard globalization package for Python.
- [Flask-Babel](https://pythonhosted.org/Flask-Babel/) is an extension to Flask.
- [Jinja2/Babel integration](http://jinja.pocoo.org/docs/dev/integration/) provides instructions on how to use Babel with Jinja2 templates.

## Key concepts

For this example, you install the Flask-Babel pip package and its dependents into your app's `container/pip/` directory.
The Flask-Babel package provides the pybabel tool, which you can use to create translation files for your Flask-based
app.

QRadar® passes the user's preferred locale in the `Accept-Languages` attribute in the request header to
your app.

Use the Flask-Babel package to create globalization text values that your app employs.

1. Use the pybabel tool to extract locale keys, typically to a `.pot` file.
2. Use the pybabel tool to build templated `.po` files for each language set you want to support.
3. Edit and complete the `.po` file, translating all of the keys to the language-specific variant of the text
value.
4. Use the pybabel tool to compile the completed `.po` files into a binary set of `.mo` files that can be employed by
your Flask python code, or your Jinja2 templates.

## Prerequisites

To work through this example, you must install the following Python packages into your app's `container/pip/` directory:

- pytz
- Babel
- speaklater
- Flask-Babel

You must also create an ordering.txt file in the `container/pip/` directory that contains the above python packages
listed in the order they are to be installed.

Bundle python wheel (whl) files instead of `.tar` file (`tar.gz`) source files wherever possible. Some raw python source
package `.tar` files need to use the gcc compiler or other tools, which the base docker container that hosts your app
code might not have.

Build python wheel files from package source tarballs on your local system. 

**Example:**

```bash
tar -xvzf some_package.tar.gz
python setup.py sdist bdist_wheel
```

## Jinja2 template: app/templates/hello.html

This example builds on the HelloWorld sample app. The original HTML template was built with hardcoded English
language-specific text values. The following example wraps the English locale strings values with Jinja2 directives
that use the `gettext` functions from Flask-Babel. 

**Example:**

```html
<!DOCTYPE html>
<html>
  <head>
    <title>{{ _( 'Hello from Flask' ) }}</title>
    <link href="static/styles.css" rel="stylesheet">
  </head>
  <body>
    {% if name %}
    <p>{{ _( 'Hello' ) }}, {{ name }}!</p>
    {% else %}
    <p>{{ _( 'Hello, World!' ) }}</p>
    {% endif %}
  </body>
</html>
```

This example uses the shorthand alias for the `gettext` function. You can also use the full form. 

**Example:**

```html
{{ gettext( '...') }}
```

This method provides a useful mechanism to quickly build and prototype your app. You can return later to make it locale-aware. 
Use the actual initial text as keys to keep the template readable.

> **Note:** Eliminate white space around the directive. For example, consider the use of an HTML `<span>`, `<div>`, or
> other element. The pybabel tool might have some difficulty extracting all key values.

## Configure Pybabel

You must configure the pybabel tool to recognize which source files to examine. Create a file called
`babel.cfg` with the following contents and add it to the app directory:

```conf
[python: **.py]
[jinja2: **/templates/**.html]
extensions=jinja2.ext.autoescape,jinja2.ext.with_
```

In this example, pybabel is configured to examine all Python source files in the `app/` folder, and all HTML files in
any sub directory of the `app/templates/` folder.

The pybabel tool uses the babel.cfg file to know which directories or files to examine within your app for potential
translatable entries. It looks for `gettext(..)` and `_(..)` entries in your `*.py` files and your Jinja2 templates to
build into a local `.pot` file.

## Create the `.pot` file

To create a `.pot` file, in the command line, navigate to the `app/` folder, and type the following command:

```bash
pybabel extract -F babel.cfg -o messages.pot .
```

A `message.pot` file is created in the `app/` folder. Its content is similar to the following file:

```pot
# Translations template for PROJECT.
# Copyright (C) 2020 ORGANIZATION
# This file is distributed under the same license as the PROJECT project.
# FIRST AUTHOR <EMAIL@ADDRESS>, 2020.
#
#, fuzzy
msgid ""
msgstr ""
"Project-Id-Version: PROJECT VERSION\n"
"Report-Msgid-Bugs-To: EMAIL@ADDRESS\n"
"POT-Creation-Date: 2020-09-03 14:53+0000\n"
"PO-Revision-Date: YEAR-MO-DA HO:MI+ZONE\n"
"Last-Translator: FULL NAME <EMAIL@ADDRESS>\n"
"Language-Team: LANGUAGE <LL@li.org>\n"
"MIME-Version: 1.0\n"
"Content-Type: text/plain; charset=utf-8\n"
"Content-Transfer-Encoding: 8bit\n"
"Generated-By: Babel 2.8.0\n"

#: templates/hello.html:4
msgid "Hello from Flask"
msgstr ""

#: templates/hello.html:9
msgid "Hello"
msgstr ""

#: templates/hello.html:11
msgid "Hello, World!"
msgstr ""
```

You can use the `msgid` entries as your keys.

## Create the .po Files

You create individual language-specific `.po` files for Spanish, French, and English. In the command line, navigate to the `app/` folder, and type the following commands:

```bash
pybabel init -i messages.pot -d translations -l es
pybabel init -i messages.pot -d translations -l fr
pybabel init -i messages.pot -d translations -l en
pybabel init -i messages.pot -d translations -l ja
```

These commands are used to create the translation files in the following locations:

- `app/translations/es/LC_Messages/messages.po`
- `app/translations/fr/LC_Messages/messages.po`
- `app/translations/en/LC_Messages/messages.po`
- `app/translations/ja/LC_Messages/messages.po`

The following example is the `app/translations/es/LC_Messages/messages.po` file that is generated:

```po
# Spanish translations for PROJECT.
# Copyright (C) 2020 ORGANIZATION
# This file is distributed under the same license as the PROJECT project.
# FIRST AUTHOR <EMAIL@ADDRESS>, 2020.
#
msgid ""
msgstr ""
"Project-Id-Version: PROJECT VERSION\n"
"Report-Msgid-Bugs-To: EMAIL@ADDRESS\n"
"POT-Creation-Date: 2020-09-03 14:53+0000\n"
"PO-Revision-Date: 2020-09-03 14:59+0000\n"
"Last-Translator: FULL NAME <EMAIL@ADDRESS>\n"
"Language: es\n"
"Language-Team: es <LL@li.org>\n"
"Plural-Forms: nplurals=2; plural=(n != 1)\n"
"MIME-Version: 1.0\n"
"Content-Type: text/plain; charset=utf-8\n"
"Content-Transfer-Encoding: 8bit\n"
"Generated-By: Babel 2.8.0\n"

#: templates/hello.html:4
msgid "Hello from Flask"
msgstr ""

#: templates/hello.html:9
msgid "Hello"
msgstr ""

#: templates/hello.html:11
msgid "Hello, World!"
msgstr ""
```

## Edit the  .po files

Edit the `.po` files to add the language-specific text strings that QRadar uses to translate your app's content.
For each msgid in the `.po` file, you must enter a corresponding `msgstr` value in the target language.

The following example is from the `app/translations/es/LC_Messages/messages.po` file:

```po
#: templates/index.html:11
msgid "Hello World!"
msgstr "Hola Mundo!"
```

## Create the .mo Files

To create the `.mo` files, in the command line, navigate to the `app/` folder, and type the following command:

```bash
pybabel compile -d translations
```

This command compiles all your `.po` files into `.mo` files in the following locations:

- `app/translations/es/LC_Messages/messages.mo`
- `app/translations/fr/LC_Messages/messages.mo`
- `app/translations/en/LC_Messages/messages.mo`
- `app/translations/ja/LC_Messages/messages.mo`

The QRadar GUI app framework provides a default Flask environment that looks for locale-specific files in the sub
directories of the `app/translations/` folder.

To specify the UTF-8 encoded locales that your app supports, you can add a `config.py` file to the `app/` folder. 

**Example:**

```python
# -*- coding: utf-8 -*-
# ...
# available languages
LANGUAGES = {
    'en': 'English',
    'es': 'Español',
    'fr': 'Français',
    'ja': '日本語'
}
```

This globalization support file helps QRadar find the `.mo` file for each locale you specify.

After you create the `.mo` files, you can remove the `.po`, `.pot`, and `babel.cfg` files if you do not want these
resources to be packaged with your app.

## Update __init__.py

```python
__author__ = 'IBM'

# pylint: disable=import-outside-toplevel, unused-variable

from flask import Flask
from flask import request
from qpylib import qpylib
from flask_babel import Babel
from .config import LANGUAGES

babel = Babel()

def get_locale():
    return request.accept_languages.best_match(list(LANGUAGES.keys()))

# Flask application factory.
def create_app():
    # Create a Flask instance.
    qflask = Flask(__name__)
    babel.init_app(qflask)
    babel.localeselector(get_locale)

    # Retrieve QRadar app id.
    qradar_app_id = qpylib.get_app_id()

    # Create unique session cookie name for this app.
    qflask.config['SESSION_COOKIE_NAME'] = 'session_{0}'.format(qradar_app_id)

    # Hide server details in endpoint responses.
    @qflask.after_request
    def obscure_server_header(resp):
        resp.headers['Server'] = 'QRadar App {0}'.format(qradar_app_id)
        return resp

    # Register q_url_for function for use with Jinja2 templates.
    qflask.add_template_global(qpylib.q_url_for, 'q_url_for')

    # Import blueprint endpoints.
    from . import views
    qflask.register_blueprint(views.views_blueprint)
    from . import dev
    qflask.register_blueprint(dev.dev_blueprint)

    # Initialize logging.
    qpylib.create_log()

    # To enable app health checking, the QRadar App Framework
    # requires every Flask app to define a /debug endpoint.
    # The endpoint function should contain a trivial implementation
    # that returns a simple confirmation response message.
    @qflask.route('/debug')
    def debug():
        return 'Pong!'

    return qflask
```

The following list describes content from the `__init__.py` code snippet:

1. The Flask app is injected into a Babel context so that your app can render locale-specific text.
2. This code applies the Babel `localeselector` decorator pattern across all your routes (any request
that comes from QRadar). The decorator uses the locales that are defined in the `app/config.py` file to connect the
best-fit language-specific keys file to the incoming request.
