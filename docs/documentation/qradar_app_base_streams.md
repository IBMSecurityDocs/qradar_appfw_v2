# QRadar app base image streams

## Base image streams

There are three QRadar _base image streams_ for app development.

The latest releases of each stream are always based upon the same Red Hat Universal Base Image 8 Minimal (ubi8-minimal)
image version, and differ only in their pre-installed version of Python and associated packages.

Originally, only a single base image stream existed, containing Python 3.6.
This is the v2 stream, where releases have major version 2, e.g. `2.1.18`.

QRadar 7.4.3 FP8 and 7.5.0 UP3 introduced a second base image stream containing Python 3.8 in place of 3.6.
This is the v3 stream, where releases have major version 3, e.g. `3.0.11`.

QRadar 7.5.0 UP7 introduced a third stream containing Python 3.11.
This is the v4 stream, where releases have major version 4, e.g. `4.0.2`.

**Note**: The v4 stream contains fewer pre-installed Python packages than its predecessors. The reason for this is
to reduce base image exposure to security and dependency issues, and to reduce redundancy (many apps don't use
Python). If you want to convert a v2 or v3 Python-based app to use a v4 image, you may have to bundle extra
Python packages with your app to satisfy dependencies.

## Base image support

Python 3.6 is now end-of-life, and many Python libraries no longer support Python 3.6 in their latest versions,
so the v2 image will soon be deprecated.

App developers are encouraged to use a v3 or v4 image, but should be aware of the following:

- An app that uses a v3 base image can only be installed on a **7.4 QRadar server with version 7.4.3 FP8 or later**,
or a **7.5 QRadar server with version 7.5.0 UP3 or later**.
- An app that uses a v4 base image can only be installed on a **7.5 QRadar server with version 7.5.0 UP7 or later**.

In summary:
- v2 stream: Python 3.6, all QRadar versions
- v3 stream: Python 3.8, QRadar 7.4.3 FP8+, 7.5.0 UP3+
- v4 stream: Python 3.11, QRadar 7.5.0 UP7+

## How to specify a base image version

Each QRadar release ships with the most up-to-date release of each base image stream, i.e. the latest v2, v3 and v4
releases.

When you install an app, QRadar uses the `image` field value from the app's `manifest.json` to determine which base image
version to use.

For example, to specify a v3 image, supply an `image` value like this:

```json
  "image": "qradar-app-base:3.0.0"
```

The available base images on the QRadar server may not correspond exactly with the version specified in
your `manifest.json`. During app installation, QRadar chooses the base image based on the major version of
your supplied `image` field.
