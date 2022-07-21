# Setting app log level

If you use qpylib logging and a log_level endpoint like the one in the sample helloworld app, you can create your own targeted web requests to the app for the following route:

*Table 1. Request route*

<table>
    <thead>
        <tr>
            <th>Route</th>
            <th>Format</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><code>POST /log_level</code></td>
            <td>
                <code>POST https://{console_ip}/console/plugins/{application_id}/app_proxy/log_level</br>
                form body: level = ‘INFO’</br> ‘DEBUG’</br> ‘ERROR’</br> ‘WARNING’</br> ‘CRITICAL’</td></code>
            <td>Dynamically define the level of logging that you want your app to capture. Post a form with an
                attribute level that is set to one of the log level values to this endpoint. QRadar® dynamically resets
                the log collection levels in your<code>/opt/app-root/store/log/app.log</code> file.</td>
        </tr>
    </tbody>
</table>

## Viewing logs within the host directory

All logs are located in the `/opt/app-root/store/log` directory of the container.
