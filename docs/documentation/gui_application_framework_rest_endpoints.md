# GUI application framework REST endpoints

The key interface between lifecycle management of an app, during both the creation and running phases, is the QRadar
GUI app framework REST API endpoints.

**Tip:** Set the version header explicitly to the version of the API you are developing with. If you do
not set the version header, you will fail app validation when you submit an app to IBM.

<https://www.ibm.com/support/knowledgecenter/en/SS42VS_7.3.2/com.ibm.qradar.doc/c_rest_api_getting_started.html>

The following table describes the QRadar RESTful API endpoints:

<table>
    <tr>
        <th>Endpoint</th>
        <th>Parameters</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>`GET /gui_app_framework/application_creation_task`</td>
        <td></td>
        <td>Retrieves a list of status details for all asynchronous requests to create applications.</td>
    </tr>
    <tr>
        <td>`GET /gui_app_framework/application_creation_task/{_application_id_}`</td>
        <td>Application Instance ID</td>
        <td>Retrieves status details for the asynchronous request to create the application with the specified ID<./td>
    </tr>
    <tr>
        <td>`POST /gui_app_framework/application_creation_task`</td>
        <td>Application (`.zip`) bundle file</td>
        <td>Creates an application within the application framework and registers it with QRadar. The application
            is created asynchronously. A reference to the application instance ID is returned and must be used in
            subsequent API calls to determine the status of the app installation. 
            </br>**Note:** This endpoint creates both the
            instance and definition to maintain backward compatibility in the API versions.</td>
    </tr>
    <tr>
        <td>`POST /gui_app_framework/application_creation_task/{_application_id_}`</td>
        <td>Application Instance ID, cancel status</td>
        <td>Updates a new application installation within the application framework. This is used to cancel the creation
            of an application by updating its status to `CANCELLED`.</td>
    </tr>
    <tr>
        <td>`GET /gui_app_framework/application_creation_task/{_application_id_}/auth`</td>
        <td>Application Instance ID</td>
        <td>Retrieves an authorization request for an application installation that specifies which capabilities are 
            requested by the application.</td>
    </tr>
    <tr>
        <td>`POST /gui_app_framework/application_creation_task/{_application_id_}/auth`</td>
        <td>Application Instance ID</td>
        <td>Responds to an authorization request for an application installation. This is used to send a user ID with
            capabilities corresponding to those requested by the application, so that an authorized service can be
            created with that user ID and used by the application for background processing tasks.</td>
    </tr>
    <tr>
        <td>`GET /gui_app_framework/application_definitions`</td>
        <td><br></td>
        <td>Retrieves a list of application definitions that are installed on QRadar, along with their manifest JSON
            structures and status.</td>
    </tr>
    <tr>
        <td>`POST /gui_app_framework/application_definitions`</td>
        <td><br></td>
        <td>Creates an application definition within the application framework and stores the docker image required to
            instantiate an instance of the application. The application definition ID that is returned 
            must be used in subsequent API calls to determine status of the definition creation.</td>
    </tr>
    <tr>
        <td>`GET /gui_app_framework/application_definitions/{_application_definition_id_}`</td>
        <td>Application Definition ID</td>
        <td>Retrieves a specific application definition that is installed on QRadar along with its manifest JSON
            structure and status</td>
    </tr>
    <tr>
        <td>`POST /gui_app_framework/application_definitions/{_application_definition_id_}`</td>
        <td>Application Definition ID</td>
        <td>Updates a new application definition installation with the application framework. This is used to cancel the
            creation of the application definition by updating its status to `CANCELLED`.</td>
    </tr>
    <tr>
        <td>`PUT /gui_app_framework/application_definitions/{_application_definition_id_}`</td>
        <td>Application Definition ID</td>
        <td>Upgrades an application definition.
        </br>**Note:** If the application definition has instances associated with it, all
            of the instances are upgraded to the new image specified by the definition.</td>
    </tr>
    <tr>
        <td>`DELETE /gui_app_framework/application_definitions/{_application_definition_id_}`</td>
        <td>Application Definition ID</td>
        <td>Deletes an application definition and any associated instances.</td>
    </tr>
    <tr>
        <td>`GET - /gui_app_framework/application_definitions/{_application_definition_id_}/user_role_id`</td>
        <td>Application Definition ID</td>
        <td>Retrieves all user roles associated with an application definition. 
        </br>**Note:** This only shows user roles added by using the following POST endpoint.</td>
    </tr>
    <tr>
        <td>`POST - /gui_app_framework/application_definitions/{_application_definition_id_}/user_role_id/{_user_role_id_}`
        </td>
        <td>Application Definition ID, User Role ID</td>
        <td>Adds a user role to the list associated with an application definition.</td>
    </tr>
    <tr>
        <td>`DELETE - /gui_app_framework/application_definitions/{_application_definition_id_}/user_role_id/{_user_role_id_}`
        </td>
        <td>Application Definition ID, User Role ID</td>
        <td>Removes a user role from the list associated with an application definition.</td>
    </tr>
    <tr>
        <td>`GET /gui_app_framework/applications`</td>
        <td><br></td>
        <td>Retrieves a list of application instances that are installed on QRadar, along with their manifest JSON
            structures and status.</td>
    </tr>
    <tr>
        <td>`POST /gui_app_framework/applications`</td>
        <td>Application Definition ID, Security Profile ID</td>
        <td>Creates an application instance using the image corresponding to the specified application definition ID.
            The security profile ID controls who can view this instance of the application. Only users that have this
            security profile ID associated with their user account are able to view this application instance. A
            reference of the application instance ID is returned and must be used in subsequent API calls to determine
            status of the instance creation.</td>
    </tr>
    <tr>
        <td>`GET /gui_app_framework/applications/{_application_id_}`</td>
        <td>Application Instance ID</td>
        <td>Retrieves a specific application instance that is installed on QRadar and its manifest JSON structure and
            status.</td>
    </tr>
    <tr>
        <td>POST /gui_app_framework/applications/{application_id}</td>
        <td>Application Instance ID, start/stop status</td>
        <td>Updates the status of an application instance. Starts or stops an application instance by setting status to
            `RUNNING` or `STOPPED`, respectively.</td>
    </tr>
    <tr>
        <td>`PUT /gui_app_framework/applications/{application_id}`</td>
        <td>Application Instance ID</td>
        <td>Upgrades an application instance.
        </br>**Note:** If the associated application definition has other instances, they are
            also upgraded as a part of this operation.</td>
    </tr>
    <tr>
        <td>`DELETE /gui_app_framework/applications/{_application_id_}`</td>
        <td>Application Instance ID</td>
        <td>Deletes an application instance.</td>
    </tr>
    <tr>
        <td>`GET /gui_app_framework/named_services`</td>
        <td><br></td>
        <td>Retrieves a list of all named services registered with the application framework for the current user. 
        </br>**Note:** If you have multiple instances, this endpoint shows the named services for the user you made the `GET`
            request with.</td>
    </tr>
    <tr>
        <td>`GET /gui_app_framework/named_services/{_uuid_}`</td>
        <td>uuid</td>
        <td>Retrieves a named service registered with the application framework by using the supplied uuid.</td>
    </tr>
</table>
