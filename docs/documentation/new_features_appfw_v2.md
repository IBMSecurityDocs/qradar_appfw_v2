# New features in QRadar app framework v2

IBM QRadar app framework contains the following new features.

## App framework SDK v2

SDK v2 supports the new app base image and Python 3. These are some of the SDK's new features:

- Using Docker, you can now test your app locally by building an app image and running an app container, effectively
using the same environment as an app that is deployed to a QRadar server.
- Root privileges are no longer required to run the SDK v2 installation script. The SDK is installed in a sub-directory of
the user's home directory, and has its own Python 3 virtual environment. Any existing Python installation on the user's
machine is left untouched, as is any installation of SDK v1.
- SDK v2 actions are invoked using the command `qapp`, rather than the v1 entry point `qradar_app_creator`.
- The combination of different installation locations and different entry points means that SDK v1 and v2 can coexist. This
allows developers to maintain an older CentOS version of an app while working on a newer Red Hat UBI version.
- The updated SDK example app demonstrates best practices for writing a Flask-based app for App Framework v2.

## Container cleanup script

If your app has a `container/clean/cleanup.sh` script, that script will be executed when the app container is stopped.
The script is triggered when either a `SIGINT` or `SIGTERM` signal is received. A cleanup script provides graceful
shutdown of processes such as database connections. Without a cleanup script, the container process is forcefully killed
after 10 seconds.

## supervisord configuration

If you place `.conf` files in your app's `container/conf/supervisord.d` directory, those files are copied to
`/etc/supervisord.d` in the container, and are picked up due to the inclusion of this entry in
`/etc/supervisord.conf`:

```conf
[include]
files = /etc/supervisord.d/*.conf
```

> **Note:** Only `.conf` files are copied; subdirectories are ignored.

## Named service access

For apps that don't use Flask and instead use, for example, nginx: if you add nginx as a named service on port 5000, it is accessible by using the standard app base url `/console/plugins/<_app id_>/app_proxy` as well as the named
service url `/console/plugins/<_app id_>/app_proxy:<_service name_>`.
