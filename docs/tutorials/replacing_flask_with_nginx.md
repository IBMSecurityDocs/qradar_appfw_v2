# Replacing Flask with NGINX

You can replace Flask with NGINX as an alternative HTTP server, using Supervisor
configuration to handle running NGINX.

NGINX is a more scalable webserver than Flask, which might be a better option for an app that is
expected to handle higher loads and more requests.

## Prerequisites

- QRadar app SDK version 2
- Docker

## Create the app

Create a new directory for your app:

```bash
mkdir NginxApp && cd NginxApp
```

Use the QRadar app SDK to initialise the app code:

```bash
qapp create
```

## Add the NGINX RPM dependency

NGINX must be installed from an RPM into the image at build time to make it available for the app at run time.

Create the directory for NGINX RPM from the top-level directory of your app workspace:

```bash
mkdir -p container/rpm
```

Download the NGINX RPM and all dependencies for RHEL UBI 8 using Docker:

```bash
docker run                                                    \
    -v $(pwd)/container/rpm:/rpm                              \
    registry.access.redhat.com/ubi8/ubi                       \
    yum download --resolve nginx-1.14.1 --downloaddir=/rpm
    ```
This will use the RHEL UBI 8 OS running inside Docker to download the NGINX RPM, which QRadar uses to install them into the app.

This command pulls down OpenSSL, which is already installed in the QRadar base app image, so you can remove it.

Remove the OpenSSL RPM by running the following command from the top-level directory of your app workspace:

```bash
rm container/rpm/openssl-1.1.1c-*.rpm
```

Make a new file called `container/rpm/ordering.txt`, which instructs QRadar where to find the RPMs to install:

```bash
ls container/rpm/ | grep -v "ordering.txt" > container/rpm/ordering.txt
```

This file contains all RPMs required for NGINX. The `grep` command filters out the `ordering.txt` file
itself.

## Add the Supervisor configuration

The NGINX process must be configured with Supervisor to handle starting the process, restarting if it exits, and
configuring process options such as where to send standard output and standard error.

Create the directory for the Supervisor configuration from the top-level directory of your app workspace:

```bash
mkdir -p container/conf/supervisord.d
```

This `container/conf/supervisord.d` directory contains all Supervisor configurations, which are copied into `/etc/supervisord.d` on startup.

Create the NGINX Supervisor configuration file `container/conf/supervisord.d/nginx.conf`:

```conf
[program:nginx]
command=/usr/sbin/nginx -c /opt/app-root/container/conf/nginx/nginx.conf
directory=/opt/app-root/app
autostart=true
autorestart=true
stderr_logfile=/opt/app-root/store/log/nginx.log
stdout_logfile=/opt/app-root/store/log/nginx.log
```

This Supervisor configuration sets up a few key properties of how NGINX is run:

- The configuration file resides in `/opt/app-root/container/conf/nginx/nginx.conf`, specified with the `-c` flag.
- The process starts automatically, and automatically restarts if it exits.
- Standard error and standard output are both written to `/opt/app-root/store/log/nginx.log`.

## Add the NGINX configuration

NGINX must itself be configured to define how the NGINX server operates (routing traffic, handling cookies, etc.).

Create the directory for NGINX configuration from the top-level directory of your app workspace:

```bash
mkdir -p container/conf/nginx
```

Create the NGINX configuration file `container/conf/nginx/nginx.conf`:

```conf
worker_processes  1;
daemon off;

# Log files should be in /opt/app-root/store/log
error_log  /opt/app-root/store/log/nginx_error.log warn;
pid        /opt/app-root/store/log/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    #log format adds x_forwarded_for in access log
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /opt/app-root/store/log/nginx_access.log  main;
    sendfile        on;
    tcp_nopush     on;
    tcp_nodelay on;
    keepalive_timeout  65;
    server {
        # Running on default port (5000)
        listen       5000;
        server_name  localhost;
        merge_slashes on;

        # Log files should be in /opt/app-root/store/log
        error_log /opt/app-root/store/log/nginx_error.log warn;
        access_log /opt/app-root/store/log/nginx_access.log;

        # Temporary NGINX files should be in /opt/app-root/tmp
        client_body_temp_path /opt/app-root/tmp/client_body;
        proxy_temp_path /opt/app-root/tmp/proxy;
        fastcgi_temp_path /opt/app-root/tmp/fastcgi;
        uwsgi_temp_path /opt/app-root/tmp/uwsgi;
        scgi_temp_path /opt/app-root/tmp/scgi;

        location / {
            # Serve files out of /opt/app-root/app
            root   /opt/app-root/app;
            index  index.html index.htm;
            add_header Set-Cookie "QRadarUserLanguage=$http_accept_language; Secure";
            add_header Set-Cookie "QRadarServerTimeISO8601=$time_iso8601; Secure";

            try_files $uri $uri/ /index.html;
        }
    }
}
```

This is an NGINX configuration file. The key elements for running as a QRadar app are:

- Log files output is set to files in `/opt/app-root/store/log`.
- Temporary files are written in `/opt/app-root/tmp`.
- The server runs on the default port (`5000`).

The restrictions on where files (log files, temporary files) are stored override NGINX default directory 
(`/etc/nginx`), which the runtime user for QRadar apps does not have permission to write to.

## Add the build scripts

The app requires some build time setup, specifically the `/opt/app-root/tmp` directory must be created for NGINX to store temporary files.

Create the directory for the build-time scripts from the top-level directory of your app workspace:

```bash
mkdir -p container/build
```

Create a new Bash script `container/build/build.sh`:

```bash
#!/bin/bash

mkdir -p /opt/app-root/tmp
```

This script creates the `/opt/app-root/tmp` directory for storing temporary files.

Create a new file called `container/build/ordering.txt` to hold paths to the build scripts the app runs at build-time:

```txt
/opt/app-root/container/build/build.sh
```

## Write the manifest

Edit the `manifest.json` file to make it more relevant to the app:

```json
{
  "name": "NGINX App",
  "description": "App using supervisor configuration to replace Flask with NGINX",
  "version": "1.0.0",
  "uuid": "<your unique app UUID>",
  "image": "qradar-app-base:2.0.0",
  "load_flask": "false",
  "areas": [
    {
      "id": "NGINXArea",
      "text": "Area served through NGINX",
      "description": "Area that serves content from NGINX",
      "url": "index.html",
      "required_capabilities": []
    }
  ],
  "services": [
    {
      "name": "nginx",
      "port": 5000,
      "version": "1.0"
    }
  ]
}
```

This manifest defines the following items:

- Disables Flask from running.
- An `area` in the QRadar UI (a tab on the main QRadar console) to display the app `index.html`.
- A named service called `nginx`, which exposes the NGINX server running on port `5000`. This named service is required,
as it informs QRadar that despite Flask being disabled, a service is still running on port `5000`. This allows health
checks and interaction with the NGINX server.

## Add content to serve from NGINX

The NGINX app serves a simple HTML webpage, but you can modify it to provide any static assets that an app requires.

The NGINX app provides content in the form of static assets (in this example, a simple HTML file). The app does not
need the Python files generated by the QRadar App SDK, so you can remove them by running the following command from the
top-level directory of your app workspace:

```bash
rm -r app/*
```

Add a simple HTML webpage called `app/index.html` to be served when the user accesses the app area in the QRadar UI:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Hello from NGINX</title>
  </head>
  <body>
      <h1>This page is being served from NGINX</h1>
  </body>
</html>
```

Create a debug file to respond to QRadar app framework healthchecks. This is a requirement of QRadar apps. It is the only way QRadar 
can determine if an app is up and running: a `200 OK` response to the `/debug` path on the default port (`5000`). Create the file 
`app/debug`. This can be any minimal content. The only requirements are that it
must return `200 OK` when queried and return a small amount of data.

**Example:**

```txt
File to return 200 to the /debug endpoint
```

## Run and package the app

Use the following command to run the app locally from the project root:

```bash
qapp run
```

Use the following commands to package and deploy the app:

```bash
qapp package -p <app zip name>

qapp deploy -p <app zip name> -q <qradar console> -u <qradar user>
```
