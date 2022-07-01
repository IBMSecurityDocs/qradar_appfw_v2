# Blueprints

QRadar apps run by default with Flask, allowing apps to use the concept of *blueprints*.

> *From the [Flask blueprints
> documentation](https://flask.palletsprojects.com/en/1.1.x/blueprints/#modular-applications-with-blueprints).*
>
> "Flask uses a concept of blueprints for making application components and supporting common patterns within an
> application or across applications. Blueprints can greatly simplify how large applications work and provide a central
> means for Flask extensions to register operations on applications. A Blueprint object works similarly to a Flask
> application object, but it is not actually an application. Rather it is a blueprint of how to construct or extend an
> application."

Blueprints provide modularization and reusability across the app Flask endpoints. Blueprints allow endpoint functionality
to be grouped, reused, and collectively configured.

> Blueprints are not mandatory, and app developers can choose not to use them. However, their use is recommended for apps that
> are not extremely simple.

Find out more about blueprints from [the Flask blueprint
documentation](https://flask.palletsprojects.com/en/1.1.x/blueprints/).

## Using blueprints

The QRadar App SDK v2 scaffolds a simple hello world application that uses blueprints, outlined below is an
explanation of the code that defines and registers the app blueprints.

### Defining blueprints

In the hello world sample `app/views.py` defines the `viewsbp` blueprint:

```python
from flask import Blueprint, render_template, current_app, send_from_directory
from qpylib import qpylib

viewsbp = Blueprint('viewsbp', __name__, url_prefix='/')

# A simple "Hello" endpoint that demonstrates use of render_template
# and qpylib logging.
@viewsbp.route('/')
@viewsbp.route('/<name>')
def hello(name=None):
    qpylib.log('name={0}'.format(name), level='INFO')
    return render_template('hello.html', name=name)

# The presence of this endpoint avoids a Flask error being logged when a browser
# makes a favicon.ico request. It demonstrates use of send_from_directory
# and current_app.
@viewsbp.route('/favicon.ico')
def favicon():
    return send_from_directory(current_app.static_folder, 'favicon-16x16.png')
```

The `viewsbp` blueprint is defined with two endpoints assigned to it; `hello` and `favicon`.

```python
viewsbp = Blueprint('viewsbp', __name__, url_prefix='/')
```

In the `viewsbp` blueprint declaration, the `url_prefix` for the blueprint is set to `/` meaning the endpoints
in this blueprint will be prefixed by `/`:

```python
@viewsbp.route('/')
@viewsbp.route('/<name>')
def hello(name=None):
    qpylib.log('name={0}'.format(name), level='INFO')
    return render_template('hello.html', name=name)
```

In the `hello` endpoint, the following is configured:

- The endpoint `hello` can be accessed from two routes, `/` and `/<name>`.
- The `<name>` part of the route defines a path variable that is exposed to the endpoint function.
- The endpoint will use the variable `<name>` to populate a Jinja template from the file `hello.html`.

### Registering blueprints

QRadar Flask apps register blueprints from the `app/__init__.py` startup file:

```python
__author__ = 'IBM'

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
    from . import dev
    qflask.register_blueprint(dev.devbp)

    return qflask
```

The `__init__.py` handles app startup, creating the `qflask` Flask app and configuring it; including registering two
blueprints called `viewsbp` and `devbp`.

```python
from . import views
qflask.register_blueprint(views.viewsbp)
```

The blueprint in `views.py` called `viewsbp` is imported and registered to `qflask`.
