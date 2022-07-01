# Build and runtime dependencies

If your app requires additional configuration at image build or container runtime, you can add them in the container
subdirectory of the app folder.

The container directory can contain these optional subdirectories:

## Build

Use the build folder to perform additional configuration changes during the docker build process for the app. In order
for your app to be available for use as quickly as possible after installation, move as many steps as
possible to image build time. Similar to the source dependency folders, you must create an `ordering.txt` file under
this folder. This text file must include cli commands separated with a new line (UNIX line endings) in the order you
want them to run.

**Example:**

Post configuration after installing nginx via an rpm install in a container, container/build - ordering.txt file
contents:

```txt
/opt/app-root/container/build/deploy.sh
```

The `ordering.txt` above runs one shell script that completes the configuration of nginx after
the rpm installation.

**Example `deploy.sh`:**

```bash
#!/bin/bash

rm /etc/nginx/conf.d/default.conf
cp ${APP_ROOT}/container/build/nginx.conf /etc/nginx/nginx.conf
cp ${APP_ROOT}/container/build/server.conf /etc/nginx/conf.d/server.conf
```

In the above example, `deploy.sh` runs as part of the docker image build process. The app's
version of `nginx.conf` and `server.conf` are copied from the container build folder to the correct places inside the image for nginx.

> 1. `APP_ROOT` is available as an environment variable inside the container and can be used to refer to the app root directory.
> 2. As the persistent store for the app (`/opt/app-root/store`) is only available at container runtime, you cannot alter anything in that directory using this method. You must add commands to the run section outlined below

## Runtime

Use the run folder to perform additional configuration changes during docker container startup. To specify
which commands to run, you must create a file called `ordering.txt` under this folder. This text file must include
cli commands separated with a new line (UNIX line endings) in the order you want them to run.

For example, to load certificates into the trusted ca certificate bundle previously uploaded via the app, use the following command:

```txt
as_root ${APP_ROOT}/bin/update_ca_bundle.sh
```

In this example, the `ordering.txt` file runs a shell script that exists in the container by default for loading
certificates stored under `/opt/app-root/store/certs` into the CA trust certificate bundle.

> 1. All commands now run as appuser by default, but the above command requires root permissions, so you must run it with a special script called `as_root`, which is available during container startup. 
> **Tip:** Only use the `as_root` command when necessary.
> 2. If `as_root` is not needed or potentially causes a security issue, the app may fail the validation process.
