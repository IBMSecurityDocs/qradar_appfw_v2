# Migrating an application built with the QRadar App Editor to the App SDK

In order to migrate your application from the QRadar App Editor to the App SDK you must complete the procedure outlined in this documentation.

**1. Export the application from the App Editor**

1. In the development tab for the application click on **Actions > Export App > As Zip.**
2. When prompted, type the version of the exported app.  
**Note**: If you do not provide a version, the exported application is set with a default version of 1.0.0.
3. Click **Export App.**

**2. [Install the latest QRadar App SDK](../tutorials/installing_the_qradar_app_sdk.md)**

**3. Read the documentation provided with the App SDK**
	
To view the documentation for the App SDK at any time after installing the App SDK you can type the following command.  
```qapp -d```
	
As stated in the documentation there are two steps to run an application locally:

1. Build an application image, if one does not already exist.
2. Run a container using the application image.

**4. Build an application image for the application using the App SDK**

1. Extract the application zip file you exported from the App Editor in step 1.
2. Navigate to the folder with the extracted contents of the zip file.
3. To build a docker image from your extracted application zip file, type.
  
	```qapp build```

   **Example output**
   
	```bash
	Supplied manifest image version is 2.0.5, selecting base image 2.1.16
	Found base image docker-release.secintel.intranet.ibm.com/gaf/qradar-app-base:2.1.16
	Preparing image build directory /home/user/qradarappsdk/docker/build
	Using /home/user/qradarappsdk/docker/build/Dockerfile
	Creating Supervisor program entry for Flask
	Building image [helloworldcustomtab]
	Using user ID 501 and group ID 20
	DOCKER BUILD LOG: START
	Step 1/14 : FROM docker-release.secintel.intranet.ibm.com/gaf/qradar-app-base:2.1.16
	Step 2/14 : LABEL com.ibm.si.app.origin=SDK
	Step 3/14 : ARG APP_USER_ID
	Step 4/14 : ARG APP_GROUP_ID
	Step 5/14 : ARG APP_USER_NAME=appuser
	Step 6/14 : ARG APP_GROUP_NAME=appuser
	Step 7/14 : ENV APP_ROOT /opt/app-root
	Step 8/14 : ENV APP_USER_ID $APP_USER_ID
	Step 9/14 : ENV APP_GROUP_ID $APP_GROUP_ID
	Step 10/14 : ENV PATH $APP_ROOT/bin:$PATH
	Step 11/14 : COPY / $APP_ROOT
	Step 12/14 : RUN groupadd -o -g $APP_GROUP_ID $APP_GROUP_NAME && \
	useradd -l -u $APP_USER_ID -g $APP_GROUP_ID $APP_USER_NAME && \
	mkdir -p /etc/supervisord.d && \
	if [ -f $APP_ROOT/init/supervisord.conf ]; then mv $APP_ROOT/init/supervisord.conf /etc; fi && \
	rm -rf $APP_ROOT/init/* && \
	if [ -d $APP_ROOT/bin ]; then chmod -R 755 $APP_ROOT/bin; fi && \
	if [ -d $APP_ROOT/container/build ]; then chmod -R 755 $APP_ROOT/container/build; fi && \
	if [ -d $APP_ROOT/container/run ]; then chmod -R 755 $APP_ROOT/container/run; fi && \
	if [ -d $APP_ROOT/container/clean ]; then chmod -R 755 $APP_ROOT/container/clean; fi && \
	if [ -d $APP_ROOT/container/service ]; then chmod -R 755 $APP_ROOT/container/service; fi && \
	if [ -d $APP_ROOT/startup.d ]; then chmod -R 755 $APP_ROOT/startup.d; fi && \
	if [ -d $APP_ROOT/container/conf/supervisord.d ]; then mv $APP_ROOT/container/conf/supervisord.d/*.conf /etc/supervisord.d; fi && \
	if [ -d /etc/supervisord.d ]; then chmod -R 755 /etc/supervisord.d ; fi && \
	echo -e "appuser ALL=(ALL) NOPASSWD:ALL\n" >> /etc/sudoers && \
	visudo -cf /etc/sudoers
	/etc/sudoers: parsed OK
	Step 13/14 : USER $APP_USER_NAME
	Step 14/14 : ENTRYPOINT ["sh", "/opt/app-root/bin/start.sh"]
	Successfully built c4d0adf426e4
	Successfully tagged helloworldcustomtab:latest
	DOCKER BUILD LOG: END
	Image [helloworldcustomtab] build completed successfully
	```

**5. Run a container using the application image**

Type the following command to run the application in docker locally:  
```qapp run```

**Example using an exported version of the default helloworld app in the App Editor**
	
```bash	
Starting container [qradar-helloworldcustomtab] using image [helloworldcustomtab]
Configuring container environment
No qenv.ini file found in workspace
Mounting /home/user/Hello_World_-_Custom_Tab/manifest.json to /opt/app-root/manifest.json
Mounting /home/user/Hello_World_-_Custom_Tab/app to /opt/app-root/app
Creating store directory /home/user/Hello_World_-_Custom_Tab/store
Mounting /home/user/Hello_World_-_Custom_Tab/store to /opt/app-root/store
Setting memory limit 100MB
Container [qradar-helloworldcustomtab] started with ID 501f4eaabd12
Host port 64104 is mapped to container port 5000/tcp
Flask is running in production mode
Access Flask endpoints at http://localhost:64104
Use docker ps to monitor container status
Log files are located in /home/user/Hello_World_-_Custom_Tab/store/log
```

**Note:** The output supplies the location of the endpoints for your app, for example, `http://localhost:64104`. You can navigate to this endpoint in your browser to see your app run locally in docker.

**Results**

The application is successfully exported from the IBM App Editor. If you have multiple applications that you developed with the App Editor repeat the process above for each app. You can then edit the app(s) normally using the functions as outlined in the App SDK documentation and uninstall the IBM App Editor from QRadar.
