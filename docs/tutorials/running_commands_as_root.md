# Running commands as root

You can configure your app to run commands as the root (sudo) user at app startup.

Create a simple QRadar app that displays a list of UNIX users in the QRadar app's
container. The `adduser` command adds a new user at startup (`testuser`) that appears in the UNIX user list.
Because the `adduser` command requires root (sudo) priviledges, the app must use the `as_root` feature.

> **Tip:** Running commands or doing work at app startup is not a best practice. Instead, design apps to run commands or do work
> at image build time.

## Prerequisites

This tutorial requires the following dependencies:

- QRadar app SDK version 2

## Create the app

Create a new folder for the app:

```bash
mkdir AsRootApp && cd AsRootApp
```

Use the QRadar app SDK to initialize the app code:

```bash
qapp create
```

## Write the manifest

Edit the `manifest.json` file to make it more relevant to the app:

```json
{
  "name": "As Root",
  "description": "App showing using the as_root feature",
  "version": "1.0",
  "image": "qradar-app-base:2.0.0",
  "areas": [
    {
      "id": "QAsRoot",
      "text": "As Root",
      "description": "As Root area showing list of all UNIX users",
      "url": "index",
      "required_capabilities": []
    }
  ],
  "uuid": "<your unique app UUID>"
}
```

This manifest defines an area called `QAsRoot` that will serve the app user interface.

## Write the Jinja template

Delete `app/templates/hello.html` and create a new file called `app/templates/users.html` using the following code:

```html
<!DOCTYPE html>
<html>
    <head>
    <title>As Root</title>
    </head>

    <body>
    <p id="description">
        This app has added a new user called 'testuser' using 'as_root', the UNIX users in the app container are:
    </p>
    <ul id="user-list">
        {% for user in users %}
        <li>{{user}}</li>
        {% endfor %}
    </ul>
    </body>
</html>
```

This Jinja template provides the entire user interface for the app, primarily displaying all UNIX users in a list in the app's
Docker container.

## Create the app endpoint

Add the app HTTP endpoints by editing `app/views.py`:

```python
import pwd
from flask import Blueprint, render_template

# pylint: disable=invalid-name
viewsbp = Blueprint('viewsbp', __name__, url_prefix='/')

# Simple endpoint that renders and serves 'users.html', displaying all UNIX
# users on the system
@viewsbp.route('/')
@viewsbp.route('/index')
def users():
    # Get all UNIX users and store their names in a list
    user_list = []
    for user in pwd.getpwall():
        user_list.append(user[0])
    # Render users.html using the retrieved user list
    return render_template('users.html', users=user_list)
```

This sets up the app endpoint to retrieve the list of UNIX users and then injects the user list into the Jinja
template.

## Write the Create User startup script

Add a new script called `container/run/add_user.sh`:

```bash
# Create a new user called 'testuser', with a home directory (/home/testuser)
as_root adduser testuser -m
```

Add a new file called `container/run/ordering.txt`:

```txt
/opt/app-root/container/run/add_user.sh
```

These two files define the startup behaviour of the app. The `ordering.txt` file
points to the file to run on startup (`add_user.sh`), and
`add_user.sh` adds a new user called `testuser`.

The `add_user.sh` script uses the `as_root` feature, which allows apps to run
commands as the root (sudo) user at startup.

## Test the app locally

You can now test the app by running the app locally using the QRadar App SDK:

```bash
qapp run
```

The QRadar app SDK reports the port that the app is running on, so
you can access the app at `http://localhost:<app port>`.

## as_root

The `as_root` feature provides app developers the ability to run commands as the
root user at app startup.

### Limitations

The `as_root` option is only available at app start up, if it is used during
normal runtime operation, it will fail.

### Considerations

Only use the `as_root` option when necessary. Its use is subject to
strict validation when submitted to X-Force Exchange. You might need to 
justify a necessary reason for using it.
