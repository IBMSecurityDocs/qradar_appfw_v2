# Using SQLite

Because QRadar Apps have built-in support for SQLite, you can use SQLite without installing extra packages.

Use a JSON configuration file in your app to set Flask configuration options, while also using this Flask
configuration to store database configuration (such as the DB name).

## Prerequisites

- QRadar app SDK version 2
- Docker

## Create the app

Create a new directory for your app:

```bash
mkdir SQLiteApp && cd SQLiteApp
```

Use the QRadar App SDK to initialise the app code:

```bash
qapp create
```

## Write the manifest

Edit the `manifest.json` file to make it more relevant to the app:

```json
{
  "name": "Sqlite Storage App",
  "description": "Save and read data from an sqlite database using an html form",
  "version": "1.0.0",
  "image": "qradar-app-base:2.0.0",
  "areas": [
    {
      "id": "SqliteStorageTab",
      "text": "SqliteStorage",
      "description": "Tab with html form to save data to database",
      "url": "index",
      "required_capabilities": []
    }
  ],
  "uuid": "<your unique app UUID>"
}
```

## Set up the SQLite database at app startup

The SQLite database must be configured and set up at startup.

> This startup setup of the database must be repeatable, as the
> `/opt/app-root/store` directory is persisted when apps are stopped and started. Use startup scripts to accomplish this.

### Writing the Database SQL schema

Create the directories to contain app SQL schemas from the top-level directory of your app workspace:

```bash
mkdir -p container/conf && mkdir -p container/conf/db
```

Create a new SQL file called `schema.sql` in the `container/conf/db` directory:

```sql
CREATE TABLE IF NOT EXISTS entries (
  id integer primary key autoincrement,
  title text not null
);
```

This SQL schema creates a new simple table `entries` if it does not yet exist, which contains only an ID and title per row.

> **Tip:** To maintain SQL that must work across app updates and schema changes, consider using a
> SQL version control system, such as [FlywayDB](https://flywaydb.org/) or
> [golang-migrate](https://github.com/golang-migrate/migrate).

### Database and Flask configuration

Create a new JSON configuration file (`container/conf/config.json`) to contain some configuration values that is loaded into Flask at startup
and made available at runtime.

```json
{
  "DEBUG": false,
  "DB_NAME": "mystore"
}
```

This loads two keys into Flask:

- `DEBUG = false` - Run Flask in non-debug mode.
- `DB_NAME = mystore` - The name of the DB the app uses, which can be accessed at runtime.

### Startup script to set up database directory

Create a directory to contain app startup scripts from the top-level directory of your app workspace:

```bash
mkdir -p container/run
```

At startup, if no database directory exists, it must be created. This directory inside the `/opt/app-root/store`
directory contains the SQLite DB files. Create a new script called `startup.sh` inside `container/run`:

```bash
#!/bin/bash

mkdir -p "${APP_ROOT}"/store/db
```

The app needs to know what script it should run at startup, so create a new `ordering.txt` file inside `container/run`
that points to the path of the startup script:

```txt
/opt/app-root/container/run/startup.sh
```

## Create interface with the database

Create the directory for holding the database interface code from the top-level directory of your app workspace:

```bash
mkdir -p app/db
```

Now create some helper methods for interacting with the database, handling database creation, connection, and executing
SQL schema files. Create a file called `database.py` inside the directory `app/db` using the following code:

```python
import os
import sqlite3
from contextlib import closing
from qpylib import qpylib

DB_STORAGE_PATH = qpylib.get_store_path('db')


# Create the database specified in the parameters provided
def create_db(db_name):
    get_db_connection(db_name)


# Execute the specified sql file against the database in the parameters provided
def execute_schema_sql(db_name, schema_file_path):
    conn = get_db_connection(db_name)
    with conn:
        with open(schema_file_path, mode='r') as schema_file:
            cur = conn.cursor()
            with closing(cur):
                cur.executescript(schema_file.read())
                conn.commit()


# Get db connection to sqlite database using the parameter provided
def get_db_connection(db_name):
    db_path = os.path.join(DB_STORAGE_PATH, db_name)
    conn = sqlite3.connect(db_path)
    return conn
```

## Create the Python initialization code

Edit the Python initialisation code in `app/__init__.py` to include loading the custom configuration and initializing
the SQLite database by executing the SQL schema:

```python
__author__ = 'IBM'

import json
from .db.database import create_db, execute_schema_sql
from flask import Flask
from qpylib import qpylib


# Flask application factory.
def create_app():
    # Create a Flask instance.
    qflask = Flask(__name__)

    # Retrieve QRadar app id.
    qradar_app_id = qpylib.get_app_id()

    # Create unique session cookie name for this app.
    qflask.config['SESSION_COOKIE_NAME'] = 'session_{0}'.format(qradar_app_id)

    # Initialize database settings and flask configuration options via json file
    with open(qpylib.get_root_path(
            "container/conf/config.json")) as config_json_file:
        config_json = json.load(config_json_file)

    qflask.config.update(config_json)

    # Hide server details in endpoint responses.
    # pylint: disable=unused-variable
    @qflask.after_request
    def obscure_server_header(resp):
        resp.headers['Server'] = 'QRadar App {0}'.format(qradar_app_id)
        return resp

    # Register q_url_for function for use with Jinja2 templates.
    qflask.add_template_global(qpylib.q_url_for, 'q_url_for')

    # Initialize logging.
    qpylib.create_log()

    # To enable app health checking, the QRadar App Framework
    # requires every Flask app to define a /debug endpoint.
    # The endpoint function should contain a trivial implementation
    # that returns a simple confirmation response message.
    @qflask.route('/debug')
    def debug():
        return 'Pong!'

    # Import additional endpoints.
    # For more information see:
    #   https://flask.palletsprojects.com/en/1.1.x/tutorial/views
    from . import views
    qflask.register_blueprint(views.viewsbp)

    # create db by loading schema
    db_name = qflask.config["DB_NAME"]
    schema_file_path = qpylib.get_root_path("container/conf/db/schema.sql")
    create_db(db_name)
    execute_schema_sql(db_name, schema_file_path)

    return qflask
```

There are two important additions to the `__init__.py` file:

```python
# Initialize database settings and flask configuration options via json file
with open(qpylib.get_root_path(
        "container/conf/config.json")) as config_json_file:
    config_json = json.load(config_json_file)

qflask.config.update(config_json)
```

This code loads the `config.json` into Flask, allowing these values to be retrieved at runtime.

```python
# create db by loading schema
db_name = qflask.config["DB_NAME"]
schema_file_path = qpylib.get_root_path("container/conf/db/schema.sql")
create_db(db_name)
execute_schema_sql(db_name, schema_file_path)
```

This code creates the database by executing the `schema.sql` defined above.

## Create the app endpoints

Update `app/views.py` to add two new endpoints for serving the app UI and adding values to the app database:

```python
from contextlib import closing
from flask import Blueprint, current_app, g, redirect, render_template, request, url_for
from .db.database import get_db_connection

# pylint: disable=invalid-name
viewsbp = Blueprint('viewsbp', __name__, url_prefix='/')


# get a db connection before request
def before_request():
    # Retrieve database settings from application configuration
    db_name = current_app.config["DB_NAME"]
    g.conn = get_db_connection(db_name)


# close db connection after request
def after_request(response):
    if g.conn is not None:
        g.conn.close()
    return response


viewsbp.before_request(before_request)
viewsbp.after_request(after_request)


@viewsbp.route('/')
@viewsbp.route('/index')
def show_entries():
    cur = g.conn.cursor()
    with closing(cur):
        cur.execute('SELECT TITLE FROM entries ORDER BY id DESC')
        entries = [dict(title=row[0],) for row in cur.fetchall()]
    return render_template('hello.html', entries=entries)


@viewsbp.route('/add_entry', methods=['POST'])
def add_entry():
    cur = g.conn.cursor()
    with closing(cur):
        insert_query = 'INSERT INTO entries (title) VALUES (?)'
        cur.execute(insert_query, (request.form['title'],))
        g.conn.commit()
    return redirect(url_for('viewsbp.show_entries'), code=303)
```

## Write the app HTML

Edit `app/templates/hello.html` to display both a list of stored entries, and a form for submitting a new entry:

```html
<!DOCTYPE html>
<title>Sqlite Storage App</title>
<link rel="stylesheet" type="text/css" href="static/styles.css">
<div class="page">
    <h1>SQLite Storage App</h1>
    <ul class="entries">
        {% for entry in entries %}
        <li><h2>{{ entry.title }}</h2>
        {% else %}
        <li><em>No entries.</em>
        {% endfor %}
    </ul>
    <form action="add_entry" method="post" class="add-entry">
        <div class="row">
            <div class="right-col">
            <input type="text" id="title" name="title" placeholder="Title..">
            </div>
        </div>
        <br/>
        <div class="row">
            <input type="submit" value="Save">
        </div>
        <br/>
    </form>
</div>
```

## Run and package the app

Use the following command to run the app locally from the project root:

```bash
qapp run
```

Use the following commands to package and deploy the app:

```bash
qapp package -p <app zip name>

qapp deploy -p <app zip name> -q <qradar console> -q <qradar user>
```
