# App logs

App logs are stored in the `/opt/app-root/store/log` directory of your application's Docker container.

The `/opt/app-root/store/log` directory contains 6 log files by default.

The first 3 log files contain stdout and stderr from startup shell scripts that now execute during container startup:

- `A0000_start_container.sh.log` contains the output from `A0000_start_container.sh`
- `A0001_kubernetes.sh.log` contains the output from `A0001_kubernetes.sh`
- `A9800_configure.sh.log` contains the output from `A9800_configure.sh`

The next 3 files are for supervisord and the app itself:

- `app.log` is the log file that is created by the qpylib library. Logging calls to the `qpylib.log()` method are written
in the `app.log` file.
- `startup.log` is the initial start-up log for the application. This log is useful for confirming that initialization of the
app completed successfully. For example, it indicates whether flask has started without errors in this log.
- `supervisord.log` contains the stderr and stdout from supervisord process.

[**Adding logging to your app**](./adding_logging_to_your_app.md)

The IBM® QRadar® Python helper library (qpylib) contains two useful functions that you can use to add logging to your
app.

[**Setting app log level**](./setting_app_log_level.md)

Use built-in routes to create HTTP requests download, view, and set log collection levels.
