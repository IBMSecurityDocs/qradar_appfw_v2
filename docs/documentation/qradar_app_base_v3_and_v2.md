# QRadar app base version 3 and version 2

In QRadar 7.5.0 Update Pack 3 and above, there is a new QRadar app base image version stream called version 3. This new
version stream uses Python 3.8 rather than Python 3.6 as the version 2 stream does.

Both version streams are supported, and the default is still version 2, since it is supported in 7.5.0 and earlier
versions, while version 3 is only supported in 7.5.0 Update Pack 3 and above.

The new QRadar app base version 3 stream was introduced because Python 3.6 is now officially end of life. While
QRadar app base version 2 can continue to safely use Python 3.6, because of official Red Hat maintenance, some external
libraries might begin dropping support for Python 3.6, which might cause issues if an app relies on them.

## Differences between version 2 and version 3

The differences between the two version streams are:

- Version 2 uses Python 3.6 and comes with Python 3.6 Python packages installed.
- Version 3 uses Python 3.8 and comes with the Python 3.8 Python package equivalents installed.

Everything else about the two base image version streams are the same. They are both based on the same Universal Base
Image and outside of Python packages have the same libraries and tools installed.

## When to use each version

Use QRadar app base version 3 when your app requires Python 3.8, for example if you need to use a Python library that
has dropped support for Python 3.6, making version 2 not suitable.

Use QRadar app base version 2 when your app must support versions other than QRadar 7.5.0 Update Pack 3 and above.

## How to specify an app's base image version

QRadar uses the app `manifest.json` to determine which QRadar app base image version to use, reading the major version
from the version in the `image` field.

To specify QRadar app base image version 2 use an image value like this:

```json
  "image": "qradar-app-base:2.1.6",
```

To specify QRadar app base image version 3 use an image value like this:

```json
  "image": "qradar-app-base:3.0.0",
```
