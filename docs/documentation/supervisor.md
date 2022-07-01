# Supervisor

QRadar apps use [Supervisor](https://github.com/Supervisor/supervisor) to manage processes, which allows
developers to provide configuration files to define how processes in apps should run.

## Supervisor use cases

Use Supervisor if your app requires background processes that are not exposed outside of the container.
For example, a PostgreSQL database that runs in the app container, or a background script that processes app data in an
asynchronous manner.

### Supervisor and named services

A named service allows apps to register a service that other apps and the QRadar UI can use. Named service
definitions provide the ability to specify a command to execute. QRadar uses the named service definition to generate
a Supervisor configuration file.

**Important:** Do not use both named services and Supervisor configuration. If the process can be fully defined by a
named service, use the named service definition instead of an additional Supervisor configuration.

## Supervisor overview

QRadar apps use Supervisor version `4.1.0`.

QRadar apps have the following components of Supervisor installed:

- The Supervisor server, called `supervisord` - this handles starting and managing processes.
- The Supervisor command line, called `supervisorctl` - this provides a shell interface for interacting with
Supervisor.

## Providing a Supervisor configuration

Supervisor configuration is provided by creating `.conf` configuration files in the `container/conf/supervisord.d`
directory. This `container/conf/supervisord.d` directory holds Supervisor configuration, and all configuration in this
directory is copied into `/etc/supervisord.d` on startup. These configuration files are linked in to the QRadar app
master Supervisor configuration using the [`include` configuration option of
Supervisor](http://supervisord.org/configuration.html#include-section-settings), allowing all configuration
to be picked up and processed.

The configuration is then processed by Supervisor. Processes are started, configured, and continuously managed,
allowing for automatic restarts if the process exits.

Any Supervisor configuration can be added to this directory, allowing flexiblity in how apps manage processes.

## Examples

QRadar apps support any valid configuration of Supervisor, beyond running simple programs. Some
simple examples of Supervisor configuration are provided here. To learn more about how to configure Supervisor, see the [Supervisor documentation](http://supervisord.org/configuration.html).

> Please note these are only examples of the Supervisor configuration. They are not standalone, fully
> functional configurations. They require more configuration, dependencies, and code.

### Running a PostgreSQL database

This example runs a PostgreSQL database:

```conf
[program:postgres]
command=postgres -D /opt/app-root/store/data
directory=/opt/app-root/app
autostart=true
autorestart=true
stderr_logfile=/opt/app-root/store/log/psql.log
stdout_logfile=/opt/app-root/store/log/psql.log
```

This example configures how standard out and error are handled, the working directory of the process, and handling startup
and restart.

### Running a background script

This example runs a bash script in the background to process data:

```conf
[program:background]
command=/bin/bash /opt/app-root/background_script.sh
directory=/opt/app-root/app
autostart=true
autorestart=true
stderr_logfile=/opt/app-root/store/log/background_script.log
stdout_logfile=/opt/app-root/store/log/background_script.log
```

This example configures how standard out and error are handled, the working directory of the process, and handling startup
and restart.

### Set Supervisord log level

This example does not run a program, but instead changes the Supervisord log level:

```conf
[supervisord]
loglevel=debug
```

This example sets the Supervisord log level to `debug`.
