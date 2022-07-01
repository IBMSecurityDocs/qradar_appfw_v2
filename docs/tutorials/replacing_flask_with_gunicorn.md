# Replacing Flask with Gunicorn

You can replace Flask with Gunicorn as an alternative HTTP server. Create a simple
Hello World app to learn how.

## Prerequisites

- QRadar app SDK version 2
- Python 3
- pip

## Create the app

Create a new folder for your app:

```bash
mkdir GunicornApp && cd GunicornApp
```

Use the QRadar app SDK to initialise the app code:

```bash
qapp create
```

## Add the Gunicorn pip Dependency

Create a new folder called `container/pip` for storing the Gunicorn pip dependencies that are installed.

```bash
mkdir container/pip
```

Download the Gunicorn pip and any of its dependencies by using `pip download`:

```bash
pip download                \
    --only-binary=:all:     \
    --platform linux_x86_64 \
    --dest container/pip    \
    gunicorn
```

This code downloads the Gunicorn pip and any of its dependencies into the `container/pip` folder.

## Add the Supervisord configuration

Create a new folder called `container/conf/supervisord.d` to hold supervisord configuration files:

```bash
mkdir -p container/conf/supervisord.d
```

This `container/conf/supervisord.d` folder holds all Supervisord configurations, which are
copied into `/etc/supervisord.d` on startup.

Create a Gunicorn configuration file `container/conf/supervisord.d/gunicorn.conf` for your app:

```conf
[program:gunicorn]
directory=/opt/app-root
command=/usr/local/bin/gunicorn -b 0.0.0.0:5000 --timeout 120 --access-logfile - --workers=4 --preload "app:create_app()"
autostart=true
autorestart=unexpected
stdout_logfile=/opt/app-root/store/log/gunicorn.log
stderr_logfile=/opt/app-root/store/log/gunicorn.log
```

This configuration file defines how Supervisord should run Gunicorn as part of this app, with log files defined,
automatic restarts and the command to start Gunicorn (with all of its command line parameters) stated.

## Write the manifest

Edit the `manifest.json` file to edit some values to make it more relevant to the app.

**Example:**

```json
{
  "name": "Gunicorn App",
  "description": "Custom supervisord configuration which starts gunicorn as a replacement for flask and exposes port 5000 as the webserver port",
  "version": "1.0.0",
  "image": "qradar-app-base:2.0.0",
  "load_flask": "false",
  "areas": [
    {
      "id": "GunicornSupervisordTab",
      "text": "Gunicorn supervisord",
      "description": "Dummy tab that displays text from the flask app",
      "url": "index",
      "required_capabilities": []
    }
  ],
  "services": [
    {
      "name": "gunicorn",
      "port": 5000,
      "version": "1.0"
    }
  ],
  "uuid": "<your unique app UUID>"
}
```

This manifest code overrides the Flask webserver and informs QRadar that it should not be loaded:

```json
"load_flask": "false",
```

The manifest then defines an `area` in the QRadar UI to display the app, called `GunicornSupervisordTab` that is
populated by calling the `/index` endpoint:

```json
"areas": [
  {
    "id": "GunicornSupervisordTab",
    "text": "Gunicorn supervisord",
    "description": "Dummy tab that displays text from the flask app",
    "url": "index",
    "required_capabilities": []
  }
],
```

Finally, the manifest describes and defines a named service that will run the Supervisord `gunicorn` program that you configured
previously, along with the port it serves on, exposing the path `/index`:

```json
"services": [
  {
    "name": "gunicorn",
    "port": 5000,
    "version": "1.0"
  }
],
```

Because this named service is running on port 5000, the area does not need to include a reference to the named service
that it is querying; it can be accessed directly. This is only the case if the named service is running on port 5000.

## Run and package the app

Use the following command to run the app locally from the project root:

```bash
qapp run
```

Use the following commands to package and deploy the app

```bash
qapp package -p <app zip name>

qapp deploy -p <app zip name> -q <qradar console> -u <qradar user>
```
