# GUI application framework fundamentals

QRadar® GUI application framework apps are stand-alone web applications that are served from a docker container.

## Installation overview

Every app runs in its own unique web server (Flask by default). Each web server, in turn, runs within a secure Linux®
container. Docker is the implementation stack for the secure containment of the web app codebase.

Each app is installed by using the RESTful API endpoints. The installation endpoint handles these tasks:

- Validates the manifest of the app.
- Automatically creates a Docker image (asynchronous) with the app code that is bundled within it.
- Registers the app (asynchronous) with QRadar to enable web traffic proxy and the HTTP request/response lifecycle from
QRadar to the app.
- Automatically runs a Docker container from the Docker image (asynchronous) mounting in persistent storage from the
QRadar host.

For more details, see [GUI app framework REST endpoints page](./gui_application_framework_rest_endpoints.md).

## Python

The default web language that is used to author an application is Python, and the Flask framework is integrated and
available for use by the application.

For more information, see the [Python website](https://www.python.org/doc/).

### Flask

Flask is a micro web application framework that is written in Python.

Flask is the web server from which the app-coded endpoints are served. You use Python functions to deliver use cases.
You can use route annotations for each Python method in the Flask application. After the Flask web server starts,
HTTP- and HTTPS-bound requests are serviced by Flask for that route, and the Python functions run.

Each Flask server that runs from within the Docker container uses port 5000. Outwardly from the container, Docker
maps that internal port 5000 to the next free port from the 32768-60999 ephemeral range. During the registration phase,
this outward mapped port is stored by QRadar, so that web requests for an app through QRadar are proxied to the correct
container.

The following code is a sample Python route:

```python
@viewsbp.route('/')
def hello_world():
    return 'Hello World!'
```

In a standalone Flask web server, a web request through a browser to <http://localhost:5000> returns: `Hello World!`

> **Note:** Other web application framework platforms can be used, such as Node. Flask is only the default framework.

### Jinja2

Jinja2 is a Python library that enables you to create templates for various output formats from a core template text
file. HTML is the format that is used for QRadar apps. Jinja2 has a rich API and large array of syntactic directives
(statements, expressions, variables, tags) that you can use to dynamically inject content into the template file.

Flask's built-in `render_template()` method is the easiest way to inject data from a Python method, served by the route,
into a Jinja2 HTML template, as shown in the following example:

```python
@viewsbp.route('/')
def hello_world():
    return render_template('hello.html', title='QRadar')
```

The template hello.html contains the following code:

```html
<!doctype html>
<title>Hello from Flask</title>
  <h1>Hello {{ title }}!</h1>
```

The following HTML output is produced:

```html
<!doctype html>
<title>Hello from Flask</title>
  <h1>Hello QRadar!</h1>
```

For more information, see the [Jinja2 website](http://jinja.pocoo.org/docs/dev/).

## HTTP request response lifecycle

When an app is successfully installed, requests to the app are proxied only by using an established connection to
QRadar. The app cannot be directly accessed by using direct URL requests or any other method.

Apps can establish a secure authenticated and authorized session to QRadar. Any authorization tokens that are created
to verify that the integrity of session can be reused. The app obtains all the capabilities, security, and authenticity
facets of QRadar. The app can use the user session state to get access to all of QRadar RESTful API endpoints to pull
or push data to or from the QRadar system.

## Containerized apps and the network

In the GUI application framework, traffic flows from container to container, from container to host on its public IP
address (not localhost), and from containers to the outside world.

When each app is passed as an archive (`.zip` file) of source code to the QRadar endpoints, QRadar builds the initial
image specific to your app codebase. Each image runs as an individual container. As the container is run or started,
QRadar maps the internal flask server port (`5000`) to an external ephemeral port. This external ephemeral port is
registered to QRadar, so that proxied requests to your app code are routed to the correct container.

## /debug endpoints

All apps must include a `/debug` endpoint or route that runs locally on port `5000`. This endpoint is used to ensure
live apps are active and reachable.

If you disable flask (the default webserver) to allow the app to use its own web server, you must include a `/debug`
endpoint on port `5000`. If you do not include the endpoint, your app will restart once per minute, as a result of
QRadar not receiving a response from the app.
