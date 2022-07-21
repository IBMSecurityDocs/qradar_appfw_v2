# App names, GUI action groups, and page IDs

App names, GUI action groups, and page IDs are identifiers that IBM® QRadar® uses for QRadar products, GUI actions, and
UI pages.

The following manifest blocks use these identifiers.

The groups field of the gui_actions block uses GUI action group identifiers to specify the toolbar or right-click menu
where the GUI action is added:

```json
"gui_actions": [
    {
        "id":"sampleToolbarButton",
        "text":"Sample Toolbar Button",
        "description":"Sample toolbar button that calls a REST method, passing an offense ID along",
        "icon":null,
        "rest_method":"sampleToolbarMethod",
        "javascript":"alert(result)",
        "groups":[ "OffenseListToolbar" ],
        "required_capabilities":[ "ADMIN" ]
    }
]
```

The `app_name` and `page_id` fields of the `page_scripts` block use the app name and page ID identifiers to specify the
QRadar application tab and sub page into which the page script is added.

```json
"page_scripts": [
    {
        "app_name":"SEM",
        " page_id":"OffenseList",
        "scripts":["/static/sampleScriptInclude.js"]
    }
],
```

## Supported app names

The following table shows a list of supported app names with descriptions.

*Table 1. Supported app names*

| App Name     	| Description                                                                  	|
|--------------	|------------------------------------------------------------------------------	|
| assetprofile 	| Asset Profile (for example, Vulnerabilities: Manage Vulnerabilities, search) 	|
| Assets       	| Assets Manager                                                               	|
| EventViewer  	| Event Viewer (for example, syslogdestination)                                	|
| Forensics    	| Incident Forensics                                                           	|
| QRadar       	| QRadar (for example, Reference Data)                                         	|
| QVM          	| QRadar Vulnerability Manager                                                 	|
| Reports      	| Reports                                                                      	|
| Sem          	| Offense Management                                                           	|
| SRM          	| QRadar Risk Manager                                                          	|
| Surveillance 	| Network Surveillance (Flows)                                                 	|

## Supported GUI actions and corresponding page IDs

The following table describes the supported GUI Actions and corresponding page IDs.

*Table 2. Supported GUI Actions and Page IDs*

<table>
    <thead>
        <tr>
            <th>GUI Action group</th>
            <th>App name</th>
            <th>Page ID</th>
            <th>Location</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>AssetDetailsToolbar</td>
            <td>Assets</td>
            <td>AssetDetailsVulnList</td>
            <td>Click on an IP Address on the Assets tab or on the Vulnerabilities tab, click **Manage Vulnerabilities &gt; By Asset** and then click on an IP Address.
            </td>
        </tr>
        <tr>
            <td>AssetListToolbar</td>
            <td>Assets</td>
            <td>AssetList</td>
            <td>Assets tab list</td>
        </tr>
        <tr>
            <td>AssetOwnerToolbar</td>
            <td>assetprofile</td>
            <td>AssetOwner</td>
            <td>Click **Vulnerability Assignment** on the Vulnerabilities tab.</td>
        </tr>
        <tr>
            <td>AttackerList</td>
            <td>SEM</td>
            <td>OffenseSummary</td>
            <td>On the Offenses Tab, click **All Offenses**, double-click an offense row, then right-click a row in Top 5 Source IPs table.</td>
        </tr>
        <tr>
            <td>AttackerListSmallToolbar</td>
            <td>SEM</td>
            <td>OffenseAttackerList</td>
            <td>On the Offenses Tab, click **All Offenses**, double-click an offense row, then click the **Sources** button on Top 5 Source IPs
                toolbar.</td>
        </tr>
        <tr>
            <td>&nbsp;</td>
            <td>NetworkAttackerList</td>
            <td>On the Offenses Tab, click **By Network**, select a row, and click the **Sources** button on the toolbar to view a list of sources.</td>
            <td>&nbsp;</td>
        </tr>
        <tr>
            <td>&nbsp;</td>
            <td>TargetAttackerList</td>
            <td>On the Offenses Tab, click **By Destination IP**, select a row, and click the **Sources** button on the toolbar to view a list of sources.
            </td>
            <td>&nbsp;</td>
        </tr>
        <tr>
            <td>AttackerListToolbar</td>
            <td>SEM</td>
            <td>AttackerList</td>
            <td>On the Offenses tab, click **By Source IP**</td>
        </tr>
        <tr>
            <td>ByAssetListFormToolbar</td>
            <td>assetprofile</td>
            <td>ByAssetListForm</td>
            <td>On the Vulnerabilities tab, click **Manage vulnerabilities &gt; By Asset**.</td>
        </tr>
        <tr>
            <td>ByNetworkListToolbar</td>
            <td>assetprofile</td>
            <td>ByNetworkList</td>
            <td>On the Vulnerabilities tab, click **Manage vulnerabilities &gt; By Network**.</td>
        </tr>
        <tr>
            <td>ByOpenServiceListToolbar</td>
            <td>assetprofile</td>
            <td>ByOpenServiceList</td>
            <td>On the Vulnerabilities tab, click **Manage vulnerabilities &gt; By Open Service**.</td>
        </tr>
        <tr>
            <td>ByVulnerabilityInstanceListToolbar</td>
            <td>assetprofile</td>
            <td>ByVulnerabilityInstanceList</td>
            <td>On the Vulnerabilities tab, click **Manage vulnerabilities**.</td>
        </tr>
        <tr>
            <td>ByVulnerabilityListToolbar</td>
            <td>assetprofile</td>
            <td>ByVulnerabilityList</td>
            <td>On the Vulnerabilities tab, click **Manage vulnerabilities &gt; By Vulnerability**.</td>
        </tr>
        <tr>
            <td>CategoryList</td>
            <td>SEM</td>
            <td>OffenseSummary</td>
            <td>On the Offenses tab, click **All Offenses**, double-click a row in the Offenses table, then right-click a row in the Top 5 Categories table.</td>
        </tr>
        <tr>
            <td>CategoryListToolbar</td>
            <td>SEM</td>
            <td>OffenseCategoryList</td>
            <td>On the Offenses tab, click **All Offenses**, double-click a row in the Offenses table, then click **Display &gt; Categories** on the toolbar. See the List of Event Categories table.</td>
        </tr>
        <tr>
            <td>DomainListToolbar</td>
            <td>QRadar</td>
            <td>DomainList</td>
            <td>On the Admin tab, click **Domain Management**.</td>
        </tr>
        <tr>
            <td>EventDetailsToolbar</td>
            <td>Event Viewer</td>
            <td>EventDetails</td>
            <td>On the Log Activity tab, click **Pause**, then click a row in the Events table.</td>
        </tr>
        <tr>
            <td>ExceptionRulesListToolbar</td>
            <td>assetprofile</td>
            <td>ExceptionRulesList</td>
            <td>On the Vulnerabilities tab, click **Vulnerability exception**.</td>
        </tr>
        <tr>
            <td>FlowDetailsToolbar</td>
            <td>Surveillance</td>
            <td>FlowDetails</td>
            <td>On the Network Activity tab, double-click on a flow.</td>
        </tr>
        <tr>
            <td>FlowsourceListToolbar</td>
            <td>Surveillance</td>
            <td>FlowsourceList</td>
            <td>On the Admin tab, click **Flow Sources**.</td>
        </tr>
        <tr>
            <td>MyAssignedVulnerabilitiesListToolbar</td>
            <td>assetprofile</td>
            <td>MyAssignedVulnerabilitiesList</td>
            <td>On the Vulnerability tab, click **My Assigned Vulnerabilities**.</td>
        </tr>
        <tr>
            <td>NetworkHierarchyListToolbar</td>
            <td>QRadar</td>
            <td>NetworkHierarchyList</td>
            <td>On the Admin tab, click **Network Hierarchy**.</td>
        </tr>
        <tr>
            <td>NetworkListSmallToolbar</td>
            <td>SEM</td>
            <td>OffenseNetworkList</td>
            <td>On the Offenses tab, click **All Offenses**, double-click a row, click **Display &gt; Networks** on the toolbar, then right-click a row in the Destination Networks table.</td>
        </tr>
        <tr>
            <td>NetworkListToolbar</td>
            <td>SEM</td>
            <td>NetworkList</td>
            <td>On the Offenses tab, click **By Network**.</td>
        </tr>
        <tr>
            <td>NetworkSummaryToolbar</td>
            <td>SEM</td>
            <td>NetworkOffenseList</td>
            <td>On the Offenses tab, click **By Network**, then double-click an offense.</td>
        </tr>
        <tr>
            <td>ObfuscationProfileContextMenu</td>
            <td>QRadar</td>
            <td>ObfuscationProfiles</td>
            <td>On the Admin tab, click **Data Obfuscation Management**.</td>
        </tr>
        <tr>
            <td>ObfuscationRightClick</td>
            <td>QRadar</td>
            <td></td>
            <td>Deprecated</td>
        </tr>
        <tr>
            <td>OffenseListSmallToolbar</td>
            <td>SEM</td>
            <td>AttackerOffenseList</td>
            <td>On the Offenses tab, double-click a row in the Offenses table, double-click a row in the Top 5 SourceIPs table, then right-click a row in the List of Offenses.</td>
        </tr>
        <tr>
            <td>&nbsp;</td>
            <td>SEM</td>
            <td>TargetOffenseList</td>
            <td>On the Offenses tab, double-click a row in the Offenses table, double-click a row in the Top 5 SourceIPs table, double-click a row in the List of Offenses, then right click.</td>
        </tr>
        <tr>
            <td>OffenseListToolbar</td>
            <td>SEM</td>
            <td>OffenseList</td>
            <td>Offenses tab main page</td>
        </tr>
        <tr>
            <td>OffenseSummaryToolbar</td>
            <td>SEM</td>
            <td>OffenseSummary</td>
            <td>On the Offenses tab, double-click a row in the Offenses table to see the toolbar.</td>
        </tr>
        <tr>
            <td></td>
            <td></td>
            <td>OffenseDeviceList</td>
            <td>On the toolbar, click **Display &gt; Log Sources**.</td>
        </tr>
        <tr>
            <td></td>
            <td></td>
            <td>OffenseUserList</td>
            <td>On the toolbar, click **Display &gt; Users**.</td>
        </tr>
        <tr>
            <td></td>
            <td></td>
            <td>OffenseRuleList</td>
            <td>On the toolbar, click **Display &gt; Rules**.</td>
        </tr>
        <tr>
            <td>OffenseSummaryToolbar</td>
            <td></td>
            <td>OffenseAttackerList Added in QRadar V.7.3.0</td>
            <td>On the Offenses tab, double-click a row in the Offenses table, then click **Display &gt; Sources** on the toolbar.</td>
        </tr>
        <tr>
            <td></td>
            <td></td>
            <td>OffenseCategoryList Added in QRadar V.7.3.0</td>
            <td>On the Offenses tab, double-click a row in the Offenses table, then click **Display &gt; Categories** on the toolbar.</td>
        </tr>
        <tr>
            <td></td>
            <td></td>
            <td>OffenseNetworkList Added in QRadar V.7.3.0</td>
            <td>On the Offenses tab, double-click a row in the Offenses table, then click **Display &gt; Networks** on the toolbar.</td>
        </tr>
        <tr>
            <td>OffenseSummaryToolbar</td>
            <td>SEM</td>
            <td>OffenseAnnotationList Added in QRadar V.7.3.2</td>
            <td>On the Offenses tab, double-click a row in the Offenses table, then click **lick Display &gt; Annotations** on the toolbar.</td>
        </tr>
        <tr>
            <td>OffenseSummaryToolbar</td>
            <td>SEM</td>
            <td>OffenseTargetList Added in QRadar V.7.3.2</td>
            <td>On the Offenses tab, double-click a row in the Offenses table, then click **Display &gt; Destinations** on the toolbar.</td>
        </tr>
        <tr>
            <td>OffenseSummaryToolbar</td>
            <td>SEM</td>
            <td>NotesList Added in QRadar V.7.3.2</td>
            <td>On the Offenses tab, double-click a row in the Offenses table, then click **Display &gt; Notes** on the toolbar.</td>
        </tr>
        <tr>
            <td>ReferenceSetElemsContextMenu</td>
            <td>QRadar</td>
            <td>ReferenceSetElems</td>
            <td>On the Admin tab, click **Reference Set Management**. Double-click a reference set, then right-click a row on the Content tab.</td>
        </tr>
        <tr>
            <td>ReferenceSetElemsToolbar</td>
            <td>QRadar</td>
            <td>ReferenceSetElems</td>
            <td>On the Admin tab, click **Reference Set Management**. Double-click a reference set to see the Content tab.</td>
        </tr>
        <tr>
            <td>ReferenceSetRulesContextMenu</td>
            <td>QRadar</td>
            <td>ReferenceSetRules</td>
            <td>On the Admin tab, click **Reference Set Management**. Double-click a reference set, select the References tab, then right-click a row.</td>
        </tr>
        <tr>
            <td>ReferenceSetRulesToolbar</td>
            <td>QRadar</td>
            <td>ReferenceSetRules</td>
            <td>On the Admin tab, click **Reference Set Management**. Double-click a reference set, select the References tab, select a row, then click the **Toolbar** button</td>
        </tr>
        <tr>
            <td>ReferenceSetsContextMenu</td>
            <td>QRadar</td>
            <td>ReferenceSets</td>
            <td>On the Admin tab, click **Reference Set Management**, then right-click a reference set.</td>
        </tr>
        <tr>
            <td>ReferenceSetsToolbar</td>
            <td>QRadar</td>
            <td>ReferenceSets</td>
            <td>On the Admin tab, click **Reference Set Management**.</td>
        </tr>
        <tr>
            <td>ReportTemplateListToolbar</td>
            <td>Reports</td>
            <td>ReportTemplateListAll</td>
            <td>Reports tab toolbar</td>
        </tr>
        <tr>
            <td>ScanPolicyVulnerabilityListToolbar</td>
            <td>assetprofile</td>
            <td>ScanPolicyVulnerabilityList</td>
            <td>On the Vulnerabilities tab, click **Administrative &gt; Scan Policies &gt; Add &gt; Scan Type Patch >
                Vulnerabilities Tab**, then click **Add**. The GUI action appears on the toolbar.</td>
        </tr>
        <tr>
            <td>ScanResultsListToolbar</td>
            <td>QVM</td>
            <td>ScanResultsList</td>
            <td>On the Vulnerabilities Tab, click **Scan Results**.</td>
        </tr>
        <tr>
            <td>SensorDeviceListToolbar</td>
            <td>EventViewer</td>
            <td>SensorDeviceList</td>
            <td>On the Admin tab, click **Log Sources**.</td>
        </tr>
        <tr>
            <td>TargetList</td>
            <td>SEM</td>
            <td>OffenseSummary</td>
            <td>On the Offenses tab, double-click a row in the Offenses table, then right-click a row in the Top 5 Destination IPs table.</td>
        </tr>
        <tr>
            <td>TargetListToolbar</td>
            <td>SEM</td>
            <td>TargetList</td>
            <td>On the Offenses tab, click **By Destination IP**.</td>
        </tr>
        <tr>
            <td>TargetSummaryToolbar</td>
            <td>SEM</td>
            <td>TargetOffenseList</td>
            <td>On the Offenses tab, click **By Destination IP**, then double-click an offense.</td>
        </tr>
        <tr>
            <td>TargetNotesList</td>
            <td>On the Offenses tab, click **By Destination IP**, double-click an offense, then click **Notes** on the toolbar.</td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td>TargetAttackerList</td>
            <td>On the Offenses tab, click **By Destination IP**, double-click an offense, then click **Sources** on the toolbar.</td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td>TenantListToolbar</td>
            <td>QRadar</td>
            <td>TenantList</td>
            <td>On the Admin tab, click **Tenant Management**.</td>
        </tr>
        <tr>
            <td>VaScannerSchedulesListToolbar</td>
            <td>Assets</td>
            <td>VaScannerSchedulesList</td>
            <td>On the Admin tab, click **Schedule VA Scanners**.</td>
        </tr>
        <tr>
            <td>VaScannersListToolbar</td>
            <td>Assets</td>
            <td>VaScannersList</td>
            <td>On the Admin tab, click **VA Scanners**.</td>
        </tr>
        <tr>
            <td>ViewNotesToolbar</td>
            <td>SEM</td>
            <td>OffenseSummary</td>
            <td>On the Offenses tab, double-click a row in the Offenses table. See the toolbar on the Last 5 Notes table.</td>
        </tr>
        <tr>
            <td>ViewOffenseDevicesToolbar</td>
            <td>SEM</td>
            <td>OffenseSummary</td>
            <td>On the Offenses tab, double-click a row in the Offenses table. See the toolbar on the Top 5 Log Sources table.</td>
        </tr>
        <tr>
            <td>ViewoffenseusersToolbar</td>
            <td>SEM</td>
            <td>OffenseSummary</td>
            <td>On the Offenses tab, double-click a row in the Offenses table. See the toolbar on the Top 5 Users table.</td>
        </tr>
        <tr>
            <td>VulnerabilityManagementListPopup</td>
            <td>assetprofile</td>
            <td>ByAssetListForm, ByNetworkList, ByOpenServiceList, ByVulnerabilityList, MyAssignedVulnerabilitiesList,
                ByVulnerabilityInstanceList</td>
            <td> On the Vulnerabilities tab, see the following options:
                <ul>
                    <li>Manage Vulnerabilities</li>
                    <li>By Network</li>
                    <li>By Asset</li>
                    <li>By Vulnerability</li>
                    <li>By Open Service</li>
                    <li>My Assigned Vulnerabilities</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td>arielListToolbar</td>
            <td>Surveillance</td>
            <td>FlowList</td>
            <td>Network Activity tab</td>
        </tr>
        <tr>
            <td>customEventListToolbar</td>
            <td>EventViewer</td>
            <td>EventList</td>
            <td>Log Activity tab</td>
        </tr>
        <tr>
            <td>ipPopup</td>
            <td>Assets assetprofile assetprofile assetprofile assetprofile</td>
            <td>AssetList MyAssignedVulnerabilitiesList ByVulnerabilityInstanceList ByAssetListForm ExceptionRulesList
            </td>
            <td>Right-click an IP address on the Assets tab, or on the Vulnerability tab, click **Manage Vulnerabilities &gt; Vulnerability
                Exception**.</td>
        </tr>
        <tr>
            <td>userNamePopup</td>
            <td>Assets</td>
            <td>AssetDetailsVulnList</td>
            <td>On the Assets Tab, click an IP Address to view Asset Details. Select a row in the Vulnerabilities table,
                right-click **Technical User**, then right-click **View Assets**. View Events Also used in Offenses and Log
                Activity.</td>
        </tr>
    </tbody>
</table>

## Deprecated GUI actions

In QRadar V7.3.0 and later, the following GUI actions are deprecated:

- OffenseList
- NetworkList
- HistoricalCorrelationToolbar
- OffenseRuleList
- OffenseDeviceList
- OffenseUserList
- NotesList
- ByAssetListForm
