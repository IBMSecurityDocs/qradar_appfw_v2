# Adding logging to your app

The IBM® QRadar® Python helper library (qpylib) contains two useful functions that you can use to add logging to your
app.

## The `log()` function

Import the qpylib helper library into your app's views.py to use the `log()` function. This function writes messages at
your chosen log level to the `/opt/app-root/store/log/app.log` file.

To turn on logging, your app must call `qpylib.create_log()` as part of its initialisation. Logging is set to
`INFO` level by default. To add the ability to set the log level, you
must add a `log_level` endpoint similar to the Hello World template sample app.


```python
# This endpoint sets the app's minimum level for qpylib logging.
# Example call using curl:
#   curl -X POST -F "level=DEBUG" http://localhost:<port>/dev/log_level
@devbp.route('/log_level', methods=['POST'])
def log_level():
    level = request.form['level'].upper()
    levels = ['DEBUG', 'INFO', 'WARNING', 'ERROR', 'CRITICAL']

    if level in levels:
        qpylib.set_log_level(level)
        return 'log level set to {0}'.format(level)

    return 'level value {0} missing or unsupported. Use one of {1}'.format(level, levels), 42
```

After you set up the log level function, you can perform a `POST` to `/log_level` endpoint to
change the log level.

The `log()` function uses the following format:

```python
def log(message, level='info'):
```

```python
from qpylib import qpylib

#in precedence order from lowest level to highest
qpylib.log('debug message', level='debug')
qpylib.log('info message', level='info')
qpylib.log('warning message', level='warning')
qpylib.log('error message', level='error')
qpylib.log('critical message', level='critical')
```

## The `set_log_level()` function

You can use this function to set the current log level. This function is used by the `POST` `/log_level` endpoint but
can also be called programmatically.

```python
def set_log_level(log_level='info'):
```
