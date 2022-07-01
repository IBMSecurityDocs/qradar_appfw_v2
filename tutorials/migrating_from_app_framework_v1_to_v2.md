# Migrating from app framework V1 to V2

In response to end-of-life for CentOS 6 and Python 2, a new version of the QRadar app framework is now available.

*App framework version 2* uses a new app base image derived from
[Red Hat Universal Base Image](https://www.redhat.com/en/blog/introducing-red-hat-universal-base-image),
and app code executes in Python 3 (v3.6.8 or later).

More specifically, the V2 app base image is the RHEL8 `ubi-minimal` image, plus a number of add-ons, including Python 3,
OpenSSL, systemd, and SQLite.

If you want to run on both V1 and V2 of the framework, you must make two different versions of your app available. 
You can migrate an app from V1 to V2 of the app framework using these steps. To learn more about the new features in V2, 
see [New features in QRadar app framework v2](../documentation/new_features_appfw_v2.md)

## Migrating your application

Migrate an app from V1 to V2 of the app framework.

### 1. Update manifest.json

#### image

By default, applications continue to install using the CentOS 6 base image, unless you specify otherwise in your
app's `manifest.json` file. A new manifest field named `image` identifies the base image (`qradar-app-base`) and
version (`2.0.0` or later) that you wish to use.

**Example:**

```json
{
  "name": "Hello World",
  "description": "Simple example app",
  "version": "1.0",
  "image": "qradar-app-base:2.0.0",
  "areas": [{
    "id": "QHelloWorld",
    "text": "Hello World",
    "description": "Hello World area",
    "url": "index",
    "required_capabilities": ["SEM"]
  }]
}
```

#### dependencies

If your manifest includes a `dependencies` entry for specifying the location of RPMs and Python packages inside your
app, you should remove it, as it is no longer supported. For more details on dependency location, see step 5 (Update
app layout).

#### environment_variables

In app framework V1, the environment variable `PATHSTART` was used to alter the container `PATH` setting to use
Python 2.7 instead of Python 2.6. In V2, all processing is done using Python 3. `PATHSTART` is no longer
required for this purpose and can be removed, if not needed for any other `PATH` customization.

### 2. Check qpylib usage

The new app base image has qpylib pre-installed as a Python package. If your app has a qpylib directory containing
an older version of qpylib source code, remove the directory and use the version in the base image instead.

The source code for qpylib is now maintained in an open source repository on Github: <https://github.com/IBM/qpylib>.

Implementation changes in qpylib:

- Due to the introduction of new features in [App framework SDK V2](#app-framework-sdk-v2), most of the sdk/live
abstraction was removed.
- Most functions are the equivalent of their pre-Github counterparts, although there are some minor changes to the
parameters of these functions: `get_root_path`, `get_store_path`, `REST`.
- The encryption module `encdec.py` has been updated.
    - The encryption algorithm was strengthened.
    - Support for backward-compatible decryption of old secrets was added.
    - Error-handling has changed, and all errors are reported using EncryptionError.
- The new `ariel.py` module adds support for performing Ariel searches.

### 3. Convert Python 2 code to Python 3

The V2 app base image supports Python 3 only, so you must convert any Python 2 code in your app to Python 3.
You can use a porting tool, such as `2to3`, to help with this process.

For more details about porting code to Python 3, see <https://docs.python.org/3/howto/pyporting.html>.

### 4. Check qjslib usage

qjslib now supports usage as a module or as a browser script.

Like qpylib, the source code for qjslib is now maintained in an open source repository on Github:
<https://github.com/IBM/qjslib>. Instructions on how to use qjslib in your app can be found there.

### 5. Update app layout

The V1 `src_deps` directory was replaced with a directory named `container`. It includes the following subdirectories:

- `container/pip`: replaces src_deps/pips, holds Python package dependencies
- `container/rpm`: replaces src_deps/rpms, holds RPM dependencies
- `container/build`: replaces src_deps/init for scripts that can be executed at image build time
- `container/run`: replaces src_deps/init for scripts that can only be executed at container start time
- `container/clean`: holds cleanup.sh script to be run when the app container is shut down
- `container/service`: holds scripts that start app background processes
- `container/conf`: holds app configuration files
- `container/conf/supervisord.d`: The contents of this directory are copied to `/etc/supervisord.d/` and included as a
part of the supervisord configuration.

To enable fast container startup, best practice dictates that as far as possible, **all dependencies must be installed
and all startup scripts must be run when the app image is built**. Do not use `container/run` unless you have to.

Also, the app manifest can no longer override the location of pip and rpm dependencies. You must use
`container/pip` and `container/rpm`.

### 6. Check for location-dependent code

In the app container, all app-related files and directories are now located under `/opt/app-root` rather than `/`.

The subdirectories of `/opt/app-root` include:

- `app`: the source code for your app
- `container`: as described in section 5
- `store`: the directory for persisting app data, particularly log files
- `bin` and `startup.d`: These directories hold container plumbing scripts.

The `manifest.json` file is now located at the top level of the deployed app (in `/opt/app-root`), rather than inside the `app` directory as it was in V1.

If you have any source code that directly accesses app files or directories using a hard-coded path location, you might
need to update it. Python-based apps are abstracted from these changes if they use qpylib utility functions to access
items within an app.

### 7. Update app dependencies

The V2 app base image has a different operating system and a different version of Python than V1, so any RPM
dependencies must be `el8` versions compatible with Red Hat UBI 8, and any Python packages must be compatible with
Python 3.6.8 or later.

**RPM example:** `nginx-1.11.13-1.el6.ngx.x86_64.rpm` from CentOS 6 can be replaced by
`nginx-1.17.8-1.el8.ngx.x86_64.rpm`.

> **Note:** The package manager in the V2 base image is `microdnf`. The `yum` package manager is not installed.

Also, the V2 base image contains updated versions of software such as Flask and supervisor. Your app
must be compatible with the new versions.

### 8. Check permissions and privileges

For security reasons, processes within the app container no longer execute as root, but instead use a non-privileged
user account. Supervisord runs Flask-based apps as this user, and any other processes must run as this user if
possible. The same non-privileged user owns all app-related files and directories inside the container.

If you need to run something as root, use the `as_root` script provided in `/opt/app-root/bin`.

### 9. Debug endpoint

All apps must provide a debug endpoint on path `/debug` with method `GET` exposed through the default app port
`5000`. This debug endpoint should return a `200 OK` response when queried. The debug endpoint should have a minimal
response, it should not return a large amount of data. For example, Flask apps return `Pong!`.

This debug endpoint is used to perform a 'health check' on the app to determine if the app is up and running. If apps
do not return `200 OK` to queries on the `/debug` endpoint, the health check fails, and QRadar assumes the
app is not functioning and takes action. For example, QRadar might fail app installations and put the app into an
error state.
