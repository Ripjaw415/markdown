
1.  Scope and Purpose 
=====================

This document contains the procedure to use Vodafone Service Manager to
create or manage ELINE and ELAN services.

2.  Change History
==================

 -------------------------------------------------------------------------------------------------------------------
|  Version   |Date          |Reason For Change
|------------|-----------------------|-----------------|  
|  1.0       |Nov 21, 2012      |Document created |
|  1.1       |Dec.17, 2012       |ELIINE Service Manager software is updated.|
|  1.2       |Dec.20, 2012       |ELAN Service Manager software is updated.|
| 1.3       |January 28, 2013   |Software change. VC/VS name is changed.|
|1.4|       April 14, 2013 |    Software change. GUI menu and templates are updated.|
|1.5|       October 27, 2013|   Software updated to support ENNI functionality.
|  1.6      | February 25, 2014     | Threshold.conf file description is added to the Requirements section.
|  1.7      | July 8, 2014      | Software updated to support NID Only functionality.
|  1.8      | Sept 24, 2014     | Software update for EPLAN/EVPLAN functionality.
|  1.9      | May 25, 2015          | New features added to Vodafone service provisioning templates
|  2.0      | May 26, 2015          | Updated device selection procedure for each service creation procedure. Added screen shots for SCos modification.
|2.1      | May 26, 2015          | Added screen shots of SCos modification for EPL service.
 -------------------------------------------------------------------------------------------------------------------


4.  Requirements
================

* User should have root access to the ESM server.

* User should have an account to log into a Ciena device to verify the configurations or provision.

* Aggregation devices are pre-configured to allow user to use the ELINE service creation template to create a service. This includes that two AGG groups are created and name end with either l2ss or mpls.

* Ensure the management vlan is removed from the UNI ports on the edge devices. Otherwise, you will get this type of error no available UNI ports on \<device type\> \<device IP address\> 

* The EPIPE-BW.txt & EVPN-BW.txt file is located under /opt/Ciena/ESM/provisioningtemplates/scripts/provisioning directory after the ELINE and ELAN Service Manager installation. They define the bandwidth to be used by the ELINE and ELAN services. This file can be adjusted according to customers needs.

* The TRANSPORT-VLANs.txt file is located under  /opt/Ciena/ESM/provisioningtemplates/scripts/provisioning directory.  It needs to be pre-configured with the transport vlan ID configured on the AGG nodes.

* The AGG-IPs.txt file is located under  /opt/Ciena/ESM/provisioningtemplates/scripts/provisioning directory.  It needs to be updated with the Aggregation nodes IP subnet used by the customer.

* Multiple links between the core and the NID devices are in place and agg configuration is created on the devices.

* Threshold.conf file is created under the  \$ESM\_HOME/provisioningtemplates/scripts/provisioning/ directory.  It defines the Delay and Jitter default values for Vodafone's services based on the regions.

* NNI-Ports.txt file is created under the \$ESM\_HOME/provisioningtemplates/scripts/provisioning/ directory. It defines the NNI ports for different device type including 3902,3916 and 3930.

5.  VFNZ Service Creation Phase I 
=================================

    5.1  Create EPIPE E-Line/Access End to End Service 
      ------
        5.1.1   Create an EPIPE E-Line/Access EPL Service 
     
    a.  Log into the ESM GUI. Go to  Applications -\> Network Database
    -\> Nodes, press Ctrl + F.

    b.  In the Search window, change the first and the second search filter
    to Display Name, contains. Type in the device IP in the matching
    field. Click on More to add more search entry as needed to search
    for the devices. Make sure you use the same searching filters and
    uncheck the Match All of the Above Criteria check box. Click on
    the Search button to get the devices.

    c.  Seclt all the devices, right click and select Snmp-Node -\> VFNZ
    EPIPE E-Line/Access End to End Service Creation.

    d.  In the drop down menu, select a UNI Service Type. Type in a
    Subscriber Name. Click on Next to continue.

    e.  Configure the service parameters as required.

Note:
--

1.  Deploy Generated Configuration to devices  if this box is checked,
    the configuration will be pushed down to the devices right away. The
    device augment files are saved on the ESM server under
    /tftpboot/provisioning/deployed directory. Otherwise, the
    configuration files will be generated and stored on the ESM server
    under the /tftpboot/provisioning/pending directory.

2.  Any undeployed configuration is saved on the ESM server under the
    /tftpboot/provisioning/pending/\<service\_type\>-\<service\_name\>
    directory.

3.  Transport SVLAN id  The available transport SVLAN IDs are defined
    in the TRANSPORT-VLANs.txt file located under the
    /opt/Ciena/ESM/provisioningtemplates/scripts/provisioning directory.
    The Transport SVLAN configuration needs to be pre-configured on the
    aggregation nodes. The parameter is displayed if there two
    Aggregation nodes existed between the two NIDs.

4.  The traffic shaper configuration (Shaper Rate and Burst Size) has
    been added for each NID for this service.

5.  If the Shaper Rate field is left empty, no traffic shaper
    configuration will be applied to the device.

6.  By default, the Burst Size is 10240 KB.

Click on Next to continue.

f.  Configure the CIR value.

Note: The CIR values are defined in the EPIPE-BW.txt file located
under /opt/Ciena/ESM/provisioningtemplates/scripts/provisioning
directory. It can be changed if required.

Click on Next to create the service. Close the window when the service
is created successfully.

g.  Telnete to each NID device and verify


        1.  The vs is created on both devices.
        2.  The port description is set correctly for each UNI port.
        3.  Traffic profiling is created correctly
        4.  CBS/EBS value is set correctly
        5.  Dot1p classifier configuration is configured correctly
        6.  Traffic shaper configuration is set correctly
        7.  CFM service is up and no CFM alarms
        8.  Verify the SVLAN-ID is added to the Aggregation port on the NID
            connected to the aggregation node.

See the output attached.



h.  Verify the subscriber identifier is stored in the database.

Log into the MySQL database and select the database. For example:

**\# mysql -u root -p** 

  Enter password:                                                     
  Welcome to the MySQL monitor. Commands end with ; or \g.  
    Your MySQL connection id is 683304  
    Server version: 5.0.77 Source distribution
    
  Type 'help;' or '\\h' for help. Type '\\c' to clear the buffer.

  mysql\> **use ESMDB61;**  
  Reading table information for completion of table and column names    
  You can turn off this feature to get a quicker startup with -A

  Database changed
  
  --------------------------------------------------------------------



Run a MySQL query to search for the subscriber identifier from
ManagedObject table. For example:

  --------------------------------------------------------------
  mysql\> select \* from TOPOUSERPROPS where name='EPL000100';

 #

  | NAME | OWNERNAME | PROPNAME | PROPVAL |

 

  | EPL000100 | NULL | NodeZ | VFNZ-3930\_226 |

  | EPL000100 | NULL | Traffic Type | Real Time |

  | EPL000100 | NULL | Service Type | epline |

  | EPL000100 | NULL | NodeA | VFNZ\_3916\_104 |

  | EPL000100 | NULL | CIR | 6 |

  | EPL000100 | NULL | Subscriber Name | VODAFONE |

  | EPL000100 | NULL | Region | Metro |

  +-----------+-----------+-----------------+---------------+

  7.  7 rows in set (0.00 sec)

  --------------------------------------------------------------

1.  #### Create an EPIPE E-Line/Access EVPL Service {.western}

1.  Log into the ESM GUI. Go to Applications -\> Network Database
    -\> Nodes, press Ctrl + F.

\

2.  In the Search window, change the first and the second search filter
    to Display Name, contains. Type in the device IP in the matching
    field. Click on More to add more search entry as needed to search
    for the devices. Make sure you use the same searching filters and
    uncheck the Match All of the Above Criteria check box. Click on
    the Search button to get the devices.

\

\

3.  Seclt all the devices, right click and select Snmp-Node -\> VFNZ
    EPIPE E-Line/Access End to End Service Creation.

\
\

4.  In the drop down menu, select a service type. Type in a Subscriber
    name. Click on Next to continue.

\
\

\
\

5.  Configure the service parameters as required.

\
\

Click on Next to continue.

6.  Configure the PIR CIR value.

\
\

Note: The PIR values are defined in the EPIPE-BW.txt file located
under /opt/Ciena/ESM/provisioningtemplates/scripts/provisioning
directory. It can be changed if required.

Click on Next to create the service. Close the window when the service
is created successfully.

7.  Telnete to each NID device and verify

1.  The vs is created on both devices.

2.  The port description is set correctly for each UNI port.

3.  Traffic profiling is created correctly.

4.  CBS/EBS value is set correctly.

5.  Dot1p classifier configuration is configured correctly.

6.  Traffic shaper configuration is set correctly.

7.  CFM service is up and no CFM alarms.

\

See the output attached.

\

\
\

1.  Create EVPN E-Line/Access End to End Service {.western}
    --------------------------------------------

    1.  #### Create an EVPN E-Line/Access EPL Service {.western}

1.  Log into the ESM GUI. Go to Applications -\> Network Database
    -\> Nodes, press Ctrl + F.

\

2.  In the Search window, change the first and the second search filter
    to Display Name, contains. Type in the device IP in the matching
    field. Click on More to add more search entry as needed to search
    for the devices. Make sure you use the same searching filters and
    uncheck the Match All of the Above Criteria check box. Click on
    the Search button to get the devices.

\

\

3.  Seclt all the devices, right click and select Snmp-Node -\> VFNZ
    EVPN E-Line/Access End to End Service Creation.

\

4.  In the drop down menu, select EPL/Access EPL (UNI is port based) as
    service type. Type in a Subscriber Name. Click on Next to
    continue.

\
\

5.  Configure the service parameters as required.

\
\

Note: User can type in a different port as showed up in the I-NNI port
field. This will overwrite the NNI port pre-configured in the
/opt/Ciena/ESM/provisioningtemplates/scripts/provisioning/NNI-Ports.txt
file.

Click on Next to continue.

6.  Choose the Classification Type and the Traffic Type.

\
\

7.  Configure the PIR and CIR values..

Click on Next to create the service. Close the window when the service
is created successfully.

8.  Telnete to each NID device and verify

1/ The vs is created on both devices.

2/ The port description is set correctly for each UNI port.

3/ Traffic profiling is created correctly.

4/ CBS/EBS value is set correctly.

5/ Dot1p classifier configuration is configured correctly.

6/ Traffic shaper configuration is set correctly.

7/ CFM service is up and no CFM alarms.

8/ Verify the SVLAN-ID is added to the Aggregation port on the NID
connected to the aggregation node.

\

See the output attached.

\

\

1.  #### Create an EVPN E-Line/Access EVPL Service {.western}

1.  Log into the ESM GUI. Go to Applications -\> Network Database
    -\> Nodes, press Ctrl + F.

\

2.  In the Search window, change the first and the second search filter
    to Display Name, contains. Type in the device IP in the matching
    field. Click on More to add more search entry as needed to search
    for the devices. Make sure you use the same searching filters and
    uncheck the Match All of the Above Criteria check box. Click on
    the Search button to get the devices.

\

\

3.  Seclt all the devices, right click and select Snmp-Node -\> VFNZ
    EVPN E-Line/Access End to End Service Creation.

\

4.  In the drop down menu, select EVPL/Access EVPL (UNI is cvid based)
    as the service type. Type in a Subscriber name. Click on Define
    Service values to continue.

\
\

5.  Configure the service parameters as required.

\
\

Click on Define Service values to continue.

6.  Choose a classification type and a traffic type. Click on Define
    Service values to continue.

\
\

7.  Configure the PIR CIR values.

\
\

Click on Define Service values to create the service. Close the window
when the service is created successfully.

8.  Telnete to each NID device and verify

1/ The vs is created on both devices.

2/ The port description is set correctly for each UNI port.

3/ Traffic profiling is created correctly.

4/ CBS/EBS value is set correctly.

5/ Dot1p classifier configuration is configured correctly.

6/ Traffic shaper configuration is set correctly.

7/ CFM service is up and no CFM alarms.

\

See the output attached.

\

6.  VFNZ Service Creation Phase II {.western}
    ==============================

    1.  Create NID Only Service {.western}
        -----------------------

        1.  #### Create an EVPN EPL NID Only Service {.western}

1.  Log into the ESM GUI. Go to Applications -\> Network Database
    -\> Nodes, press Ctrl + F.

\

2.  In the Search window, change the first and the second search filter
    to Display Name, contains. Type in the device IP in the matching
    field. Click on More to add more search entry as needed to search
    for the devices. Make sure you use the same searching filters and
    uncheck the Match All of the Above Criteria check box. Click on
    the Search button to get the devices.

\

\

3.  Seclt all the devices, right click and select Snmp-Node -\> VFNZ
    EVPN E-Line/Access End to End Service Creation.

\

\

4.  In the EVC/OVC Service type selection window, select EPL/Access
    EPL (UNI is port based from the drop down menu. Type in a
    Subscriber Name. Check the Provision NIDs only check box. Click
    on Define Service values to continue.

\

\

\

Note: You can create a NID Only and E-NNI service at the same time by
checking both Provision NIDs only and E-Access OVC Service (E-NNI to
UNI) check box.

5.  Configure the service options. Clikc on Next to continue.

\

Note: User can type in a different port as showed up in the I-NNI port
field. This will overwrite the NNI port pre-configured in the
/opt/Ciena/ESM/provisioningtemplates/scripts/provisioning/NNI-Ports.txt
file.

For NID only service, each UNI port will have a port description.

6.  Select the Classification Type and the Traffic Type.

\

Click on Define Service values to continue.

7.  Configure the PIR and CIR values. Click on Define Service values
    to create the service.

\

\

8.  Telnete to each NID device and verify

1/ The vs is created on both devices.

2/ The port description is set correctly for each UNI port.

3/ Traffic profiling is created correctly.

4/ CBS value is set correctly.

5/ Dot1p classifier configuration is configured correctly.

6/ Traffic shaper configuration is set correctly.

7/ CFM service is up and no CFM alarms.

\

See the output attached.

\

\

\

1.  Create eAccess Service {.western}
    ----------------------

    1.  #### Create an EPIPE EVPL eAccess Service {.western}

1.  Log into the ESM GUI. Go to Applications -\> Network Database
    -\> Nodes, press Ctrl + F.

\

2.  In the Search window, change the first and the second search filter
    to Display Name, contains. Type in the device IP in the matching
    field. Click on More to add more search entry as needed to search
    for the devices. Make sure you use the same searching filters and
    uncheck the Match All of the Above Criteria check box. Click on
    the Search button to get the devices.

\

\

3.  Seclt all the devices, right click and select Snmp-Node -\> VFNZ
    EPIPE E-Line/Access End to End Service Creation.

\

\

\

4.  Select EVPL/Access EVPL (UNI is cvid based) from the drop down
    menu. Type in a Subscriber Name. Check the E-Access OVC Service
    (E-NNI to UNI) check box. Click on Next to continue.

\

\

\

5.  Select the E-NNI node. Check either A or Z end as the E-NNI node.

\

\

\

Click on Next to continue.

\

6.  Configure the service options. Click on Next to continue.

\

\

\

Note: The Ethernet type option is available for eAccess service only. It
has three options: 88A8, 8100, 9100.

7.  Configure the PIR value. Click on Next to create the service.

\

\

\

8.  Telnete to each NID device and verify

1/ The vs is created on the UNI device.

2/ The vlan is added to the E-NNI and the UNI device.

3/ The port description is set correctly for the UNI port.

4/ Traffic profiling is created correctly.

5/ CBS/EBS value is set correctly.

6/ Dot1p classifier configuration is configured correctly.

7/ Traffic shaper configuration is set correctly.

8/ CFM service is up and no CFM alarms.

9/ The Ethernet type is configured as expected on the ENNI device.

\

See the output attached.

\

1.  Create eAccess NID only Service {.western}
    -------------------------------

    1.  #### Create an EVPN EPL eAccess NID only Service {.western}

1.  Log into the ESM GUI. Go to Applications -\> Network Database
    -\> Nodes, press Ctrl + F.

2.  In the Search window, change the first and the second search filter
    to Display Name, contains. Type in the device IP in the matching
    field. Click on More to add more search entry as needed to search
    for the devices. Make sure you use the same searching filters and
    uncheck the Match All of the Above Criteria check box. Click on
    the Search button to get the devices.

\

3.  Seclt all the devices, right click and select Snmp-Node -\> VFNZ
    EVPN E-Line/Access End to End Service Creation.

\
\

4.  Select EPL/Access EVPL (UNI is port based) from the drop down menu.
    Type in a Subscriber Name. Check the E-Access OVC Service (E-NNI to
    UNI) check box. Click on Define Service values to continue.

\

\

\

5.  Select the E-NNI node. Check either A or Z end as the E-NNI node.

\

\

\

Click on Define Service values to continue.

\

6.  Configure the service options. Click on Define Service values to
    continue.

\

\

\

Note: The Ethernet type option is available for eAccess service only. It
has three options: 88A8, 8100, 9100.

7.  Choose the Classification Type and the Traffic Type. Click on
    Define Service values to create the service.

\

8.  Configure the PIR value. Click on Define Service values to create
    the service.

\

\

\

9.  Telnete to each NID device and verify

1/ The vs is created on the UNI device.

2/ The vlan is added to the E-NNI and the UNI device.

3/ The port description is set correctly for the UNI port.

4/ Traffic profiling is created correctly.

5/ CBS/EBS value is set correctly.

6/ Dot1p classifier configuration is configured correctly.

7/ Traffic shaper configuration is set correctly.

8/ CFM service is up and no CFM alarms.

9/ The Ethernet type is configured as expected on the ENNI device.

10/ Verify the l2 configuration is added correctly on the UNI device.

11/ Verify the tunnel mode is set correctly on the UNI device.

See the output attached.

\

4.  Create ELAN Service {.western}
    -------------------

    1.  #### Create an EVPN EPLAN Service {.western}

\

Note when creating the ELAN service:

\

For both EPLAN & EVPLAN service creation, please ensure that the central
node which will be used as the head node for CFM (as part of the Hub and
Spoke structure), is selected as the first node when creating the
service. This will ensure that this node is set with the appropriate CFM
Mep id.

\

To do this, select all the NIDs that you plan to create a service on,
and then sort the list to ensure that this nid is at top of the list.
Once that is achieved, launch the Service creation template. This will
ensure that this NID is the first one to be picked for service creation.

\

Removing this head-end node with-out replacing with a new NID will cause
the SLA Portal data reporting to get disrupted.

\

1.  Log into the ESM GUI. Go to Applications -\> Network Database
    -\> Nodes, press Ctrl + F.

2.  In the Search window, change the first and the second search filter
    to Display Name, contains. Type in the device IP in the matching
    field. Click on More to add more search entry as needed to search
    for the devices. Make sure you use the same searching filters and
    uncheck the Match All of the Above Criteria check box. Click on
    the Search button to get the devices.

\

3.  Seclt all the devices, right click and select Snmp-Node -\> VFNZ
    EVPN E-LAN End to End Service Creation.

\

4.  In the drop down menu, select

-   Action   Create a new Service

-   UNI Service type   EPLAN (UNI is port based)

-   Subscriber name   VODAFONE (for example)

-   Provision NIDs only is checked by default and it needs to be
    checked for ELAN service creation.

Click on Define Service values to continue.

\

\

\

5.  Configure the Subscriber identifier, UNI ports and other service
    parameters.

\

\

6.  Choose a Classification Type and a Traffic Type for each of the
    selected NID. Click on Define Service values to continue.

\

\

\

Click on Define Service values to create the service.

\

7.  Configure the individual NID level parameters as defined.

\

Notable Items:

1.  Node 1 Shaper Rate   If this field is left blank then no traffic
    shaper value will be applied

2.  Node 1 Burst Size   If this field is changed from default (10240)
    then it is shaper config is not applied

\

Click on Define Service values to create the service.

\

\

8.  Telnete to each NID device and verify

1/ The vs is created on the UNI devices.

2/ The vlan is added to the E-NNI and the UNI device.

3/ The port description is set correctly for the UNI port.

4/ Traffic profiling is created correctly.

5/ CBS/EBS value is set correctly.

6/ Dot1p classifier configuration is configured correctly.

7/ Traffic shaper configuration is set correctly.

8/ CFM service is up and no CFM alarms.

9/ The Ethernet type is configured as expected on the ENNI device.

10/ Verify the l2 configuration is added correctly on the UNI device.

See the output attached.

\

1.  #### Add NID to EVPN E-LAN EPLAN Service {.western}

1.  Log into the ESM GUI. Go to Applications -\> Network Database
    -\> Nodes, press Ctrl + F.

2.  In the Search window, change the first and the second search filter
    to Display Name, contains. Type in the device IP in the matching
    field. Click on More to add more search entry as needed to search
    for the devices. Make sure you use the same searching filters and
    uncheck the Match All of the Above Criteria check box. Click on
    the Search button to get the devices.

\
\

3.  Seclt all the devices, right click and select Snmp-Node -\> VFNZ
    EVPN E-LAN End to End Service Creation.

\

4.  In the drop down menu,

-   Action   Add a NID/NIDs to a deployed service (EPLAN/EVPLAN Only)

-   UNI Service type   EPLAN (UNI is port based)

-   Subscriber name   VODAFONE (for example)

-   Provision NIDs only check box is checked.

Click on Define Service values to continue.

\

\

\

5.  Configure the service parameters as required.

\

\

\

6.  Choose a Classification Type and a Traffic Type for each of the
    selected NID. Click on Define Service values to continue.

\

\

\

7.  Configure the individual NID level parameters as defined. This step
    repeats for all the selected NIDs

Click on Define Service values to create the service.

\

\

8.  Telnete to each NID device and verify

1/ The vs is created on both devices.

2/ The port description is set correctly for each UNI port.

3/ Traffic profiling is created correctly.

4/ CBS/EBS value is set correctly.

5/ Dot1p classifier configuration is configured correctly.

6/ Traffic shaper configuration is set correctly.

7/ CFM service is up and no CFM alarms.

See the output attached.

\

\

3.  #### Create an EVPN E-LAN EVPLAN Service {.western}

\

Note when creating the ELAN service:

\

For both EPLAN & EVPLAN service creation, please ensure that the central
node which will be used as the head node for CFM (as part of the Hub and
Spoke structure), is selected as the first node when creating the
service. This will ensure that this node is set with the appropriate CFM
Mep id.

\

To do this, select all the NIDs that you plan to create a service on,
and then sort the list to ensure that this nid is at top of the list.
Once that is achieved, launch the Service creation template. This will
ensure that this NID is the first one to be picked for service creation.

\

Removing this head-end node with-out replacing with a new NID will cause
the SLA Portal data reporting to get disrupted.

1.  Log into the ESM GUI. Go to Applications -\> Network Database
    -\> Nodes, press Ctrl + F.

2.  In the Search window, change the first and the second search filter
    to Display Name, contains. Type in the device IP in the matching
    field. Click on More to add more search entry as needed to search
    for the devices. Make sure you use the same searching filters and
    uncheck the Match All of the Above Criteria check box. Click on
    the Search button to get the devices.

\

3.  Seclt all the devices, right click and select Snmp-Node -\> VFNZ
    EVPN E-LAN End to End Service Creation.

\

\

4.  In the drop down menu, select

-   Action   Create a new Service

-   UNI Service type   EVPLAN (UNI is cvid based)

-   Subscriber name   VODAFONE (for example)

-   Provision NIDs only is checked by default and it needs to be
    checked for ELAN service creation.

Click on Define Service values to continue.

\

\

5.  Configure the service parameters as required.

\

\

\

6.  Choose a Classification Type and a Traffic Type for each of the
    selected NID. Click on Define Service values to continue.

\

\

\

7.  Configure the individual NID level parameters as defined.

\

Click on Define Service values to create the service.

\

\

\

\

\

8.  Telnete to each NID device and verify

1/ The vs is created on the UNI devices.

2/ The vlan is added to the E-NNI and the UNI device.

3/ The port description is set correctly for the UNI port.

4/ Traffic profiling is created correctly.

5/ CBS/EBS value is set correctly.

6/ Dot1p classifier configuration is configured correctly.

7/ Traffic shaper configuration is set correctly.

8/ CFM service is up and no CFM alarms.

9/ The Ethernet type is configured as expected on the ENNI device.

10/ Verify the l2 configuration is added correctly on the UNI device.

\

See the output attached.

\

1.  #### Add NID to an EVPN E-LAN EVPLAN Service {.western}

1.  Log into the ESM GUI. Go to Applications -\> Network Database
    -\> Nodes, press Ctrl + F.

2.  In the Search window, change the first and the second search filter
    to Display Name, contains. Type in the device IP in the matching
    field. Click on More to add more search entry as needed to search
    for the devices. Make sure you use the same searching filters and
    uncheck the Match All of the Above Criteria check box. Click on
    the Search button to get the devices.

\
\

3.  Seclt all the devices, right click and select Snmp-Node -\> VFNZ
    EVPN E-LAN End to End Service Creation.

\

4.  In the drop down menu, select

-   Action   Add a NID/NIDSs to a deployed service (EPLAN/EVPLAN Only)

-   UNI Service type   EVPLAN (UNI is cvid based)

-   Subscriber name   VODAFONE (for example)

-   Provision NIDs only is checked by default and it needs to be
    checked for ELAN service creation.

Click on Define Service values to continue.

\

\

\

5.  Configure the service parameters as required. Select the subscriber
    identifier from the drop down menu. Select the UNI ports for the
    NIDs and configure CFM configurations.

\

\

\

Click on Define Service values to continue.

\

6.  Choose a Classification Type and a Traffic Type for each of the
    selected NID. Click on Define Service values to continue.

\

\

\

7.  Configure the individual NID level parameters as defined. This step
    repeats for all the selected NIDs to be added to the service.

\

\

\

\

\

Click on Define Service values to create the service.

\

8.  Telnete to each NID device and verify

1/ The vs is created on the UNI devices.

2/ The vlan is added to the E-NNI and the UNI device.

3/ The port description is set correctly for the UNI port.

4/ Traffic profiling is created correctly.

5/ CBS/EBS value is set correctly.

6/ Dot1p classifier configuration is configured correctly.

7/ Traffic shaper configuration is set correctly.

8/ CFM service is up and no CFM alarms.

\

See the output attached.

\

\

7.  VFNZ Service Manager {.western style="page-break-before: always"}
    ====================

    1.  Deploy Service {.western}
        --------------

Assume the service is already created on the ESM server. Use the Service
Manager template to deploy the service configuration onto the devices.

\

1.  Highlight a device, right click and select VFNZ Service Manager.

\

\

\

2.  In the drop down menu, select deploy action to perform and a
    service type. For example:

\

\

Click on Next to continue.

\

3.  In the drop down menu, select the service name that you want to
    deploy the configuration. Click on Next to deploy the service.

\

\

4.  Telnete to each NID device and verify

1/ The vs is created on the UNI devices.

2/ The vlan is added to the E-NNI and the UNI device.

3/ The port description is set correctly for the UNI port.

4/ Traffic profiling is created correctly.

5/ CBS/EBS value is set correctly.

6/ Dot1p classifier configuration is configured correctly.

7/ Traffic shaper configuration is set correctly.

8/ CFM service is up and no CFM alarms.

\

See the output attached.

\

\

2.  Modify EPL Service {.western}
    ------------------

For all service modification, it only applys to the service is already
created and deployed on the devices.

There is only one option for EPL service modification, that is to modify
the bandwidth of the service.

1.  #### Modify an EPL SCOS service to change bandwidth {.western}

1.  Highlight a device, right click and select Snmp-Node -\> VFNZ
    Service Manager.

\

\

\

2.  From the drop down menu select edit action, select EP-LINE as
    service type.

\

\

\

Click on Next to continue.

\

3.  Select the service to be modified from the drop down menu.

\

\

\

Click on Next to continue.

\

4.  Select the Service Edit Action Modify CIR/EIR from the drop down
    menu.

\

\

Click on Next to continue.

5.  Specify the new SCOS value. Change the traffic shaper configuration
    for each NID device if needed.

\

Click on Next to make the change.

NOTE: Due to the issue found in JD-80057 for MCos service modification
is not currently supported. This behavior will be addressed as JD-80113

6.  Telnet to each NID device and verify the bandwidth and the traffic
    shaper configuration has been changed on the devices as expected.
    See the output attached.

\

  ----------------------------------------------------- ---
  [](https://product-jira.ciena.com/browse/JD-80057)\   \
                                                        
  ----------------------------------------------------- ---

3.  Modify EVPL Service {.western}
    -------------------

There are four options available for EVPL service modification.

-   Modify CIR/EIR

-   Add CVID

-   Remove CVID

-   Modify ENNI EtherType (for eAccess Service only)

1.  #### Modify an EVPL service to add CVID {.western}

\

Note: Cvid 1 adds untagged frames and/or VLAN 1 to the service can only
be added to the service via EVPL service modification  Add CVID.

\

1.  Highlight a device, right click and select Snmp-Node -\> VFNZ
    Service Manager.

\

\

2.  From the pull down menu select action edit and select EVP-LINE
    as the service type. Click on Next to continue.

\

\

\

3.  Select the service from the drop down menu that you want to modify.
    Click on Next to continue.

\

\

\

4.  In the drop down menu, select Add CVID. Click on Next to
    continue.

\

\

5.  Add one CVID or a CVID range, click on Next to add the cvid.

\

\

\

6.  Verify the configuration on the device. The new cvid has been added
    to the service. See the output attached.

\

2.  #### Modify an EVPL service to remove CVID {.western}

Note: Cvid 1 can not be removed from an EVPL service. Attempting to
modify CVID 1 will result in a failure. Ciena is tracking this as
JD-80111.

1.  Highlight a device, right click and select Snmp-Node -\> VFNZ
    Service Manager.

\

\

2.  From the pull down menu select action edit and select EVP-LINE
    as the service type. Click on Next to continue.

\

\

\

3.  Select the service from the drop down menu that you want to modify.
    Click on Next to continue.

\

\

\

4.  In the drop down menu, select Remove CVID. Click on Next to
    continue.

\

\

5.  Type in one CVID or a CVID range, click on Next to remove the
    cvid.

\

\

6.  Verify the configuration on the device. The cvid has been removed
    from the service. See the output attached.

\

\

3.  #### Modify an EVPL service to change EtherType {.western}

\

Note: The Ethertype modification action only modifies the Ethertype for
a single EVC. The action does not modify the Ethertype for all EVCs
within the ENNI port. This defect is being tracked as: JD-80112.

1.  Highlight a device, right click and select Snmp-Node -\> VFNZ
    Service Manager.

\

\

2.  From the pull down menu select action edit and select EVP-LINE
    as the service type. Click on Next to continue.

\

\

\

3.  Select the service from the drop down menu that you want to modify.
    Click on Next to continue.

\

\

\

4.  In the drop down menu, select Modify ENNI EtherType. Click on
    Next to continue.

\

\

5.  Choose an EtherType from the drop down menu, click on Next to make
    the change.

\

\

6.  Verify the EtherType has been updated on the E-NNI device:

\

**Before the change:**

VFNZ-3930\_73\> conf search string tpid

vlan set vlan 444 egress-tpid 9100

virtual-circuit ethernet set port 1 vlan-ethertype 9100
vlan-ethertype-policy vlan-tpid

\

**After the change:**

VFNZ-3930\_73\> conf search string tpid

vlan set vlan 444 egress-tpid 88A8

virtual-circuit ethernet set port 1 vlan-ethertype 88A8
vlan-ethertype-policy vlan-tpid

Note: The 8100 is the default value. Here is an example, when the
EtherType is set to 8100. The configuration will be set as below. To
confirm the proper setting, a vlan show vlan 444 can be executed

**virtual-circuit ethernet set port 1 vlan-ethertype-policy vlan-tpid**

1.  #### Modify an EVPL SCOS service to change bandwidth {.western}

7.  Highlight a device, right click and select Snmp-Node -\> VFNZ
    Service Manager.

\

\

8.  From the pull down menu select action edit and select EVP-LINE
    as the service type. Click on Next to continue.

\

\

\

9.  Select the service from the drop down menu that you want to modify.
    Click on Next to continue.

\

\

\

10. In the drop down menu, select Modify CIR/EIR. Click on Next to
    continue.

\

\

11. Specify the new SCOS value. Change the traffic shaper configuration
    for each NID device if needed.

\

Click on Next to make the change.

NOTE: Due to the issue found in JD-80057 for MCos service modification
is not currently supported. This behavior will be addressed as JD-80113

12. Telnet to each NID device and verify the bandwidth and the traffic
    shaper configuration has been changed on the devices as expected.
    See the output attached.

\

  ----------------------------------------------------- ---
  [](https://product-jira.ciena.com/browse/JD-80057)\   \
                                                        
  ----------------------------------------------------- ---

4.  Modify EPLAN Service {.western}
    --------------------

There is only one option for EPLAN service modification, that is to
modify the bandwidth of the service.

1.  #### Modify an EPLAN service to change bandwidth {.western}

1.  Highlight a device, right click and select Snmp-Node -\> VFNZ
    Service Manager.

\

\

2.  From the pull down menu select action edit and select a service
    type for the service that you want to modify. Choose EP-LAN. Click
    on Next to continue.

\

\

\

3.  Select the service from the drop down menu that you want to modify.
    Click on Next to continue.

\

\

\

4.  In the drop down menu, select the device that you wanted to modify
    the service from. Click on Next to continue.

\

\

5.  Select Modify CIR/EIR option, click on Next.

\

\

\

6.  Specify the new SCOS value. If multiple values are presented, this
    is an MCOS service and is not supported.

\

Click on Next to make the change.

NOTE: Due to the issue found in JD-80057 for MCos service modification
is not currently supported. This behavior will be addressed as JD-80113

7.  Telnet to each NID device and verify the bandwidth and the traffic
    shaper configuration has been changed on the devices as expected.
    See the output attached.

\

  ----------------------------------------------------- ---
  [](https://product-jira.ciena.com/browse/JD-80057)\   \
                                                        
  ----------------------------------------------------- ---

5.  Modify EVPLAN service {.western}
    ---------------------

There are three options available for EVPLAN service modification.

-   Add CVID

-   Remove CVID

-   Modify CIR/EIR

The procedure is the same as modifying an EVPL service. The only
difference is that you need to choose which device that you want to
modify the service from.

\

1.  #### Modify an EVPLAN service to add CVID {.western}

Note: Cvid 1 can only be added to the service via EVPLAN service
modification  Add CVID.

1.  Highlight a device, right click and select Snmp-Node -\> VFNZ
    Service Manager.

\

\

2.  From the pull down menu select action edit and select a service
    type for the service that you want to modify. Choose EVP-LAN.
    Click on Next to continue.

\

\

\

3.  Select the service from the drop down menu that you want to modify.
    Click on Next to continue.

\

\

\

4.  Select the device from the drop down to modify the service. Click on
    Next to continue.

\

5.  In the drop down menu, select Add CVID. Click on Next to
    continue.

\

\

6.  Add one CVID or a CVID range, click on Next to add the cvid.

\

\

\

7.  Telnet to the NID device that has been modified. Verify the new cvid
    has been added to the service. See the output attached.

\

2.  #### Modify an EVPLAN service to remove CVID {.western}

Note: Cvid 1 can not be removed from an EVPLAN service.

1.  Highlight a device, right click and select Snmp-Node -\> VFNZ
    Service Manager.

\

\

2.  From the pull down menu select action edit and select EVP-LAN as
    the service type. Click on Next to continue.

\

\

\

3.  Select the service from the drop down menu that you want to modify.
    Click on Next to continue.

\

\

\

4.  Select the device from the drop down to modify the service. Click on
    Next to continue.

\

\

5.  In the drop down menu, select Remove CVID. Click on Next to
    continue.

\

\

6.  Type in one CVID or a CVID range, click on Next to remove the
    cvid.

\

\

7.  Telnet to the device and verify the cvid has been removed from the
    service. See the output attached.

1.  #### Modify an EPLAN service to change bandwidth {.western}

13. Highlight a device, right click and select Snmp-Node -\> VFNZ
    Service Manager.

\

\

14. From the pull down menu select action edit and select EVP-LAN as
    the service type. Click on Next to continue.

\

\

\

15. Select the service from the drop down menu that you want to modify.
    Click on Next to continue.

\

\

\

16. Select the device from the drop down to modify the service. Click on
    Next to continue.

\

\

17. In the drop down menu, select Modify CIR/EIR. Click on Next to
    continue.

\

18. Change the CIR value as desired.

\

Note: If itb s a MCos service, please use the work-around documented on
page 75 to make the changes.

19. Telnet to the device and verify the bandwidth has been changed
    correctly on the devices.

1/ The traffic profiling configuration has been changed correctly on
each device.

2/ The traffic shaper configuration has been changed correctly on each
device.

\

See the output attached.

6.  Delete Service {.western}
    --------------

Before you delete a service, make sure no benchmark testing is running
on the service that to be deleted. Clear the IP interface for the
benchmark testing if it was using this service and the PDU type was set
to IP.

1.  #### Delete ELINE service {.western}

\

1.  Highlight a device, right click and select Snmp-Node -\> VFNZ
    Service Manager.

\

\

2.  Select the remove action from action list and select the service
    type for the service to be deleted. Click on Next to continue.

\

\

\

3.  Select the service name from the service list that you want to
    delete. Click on Next to delete the service.

\

\

\

4.  Telnet to the NID device that has been removed from the EPLAN
    service, verify:

1/ vs has been removed from the device

2/ traffic profiling configuration is removed

3/ traffic shaper configuration is updated on the NNI port

See the output attached.

\

\

5.  Verify the service directory has been removed from
    /tftpboot/provisioning/deployed directory on the ESM server.

2.  #### Remove a NID from an ELAN Service {.western}

\

Before you delete a service, make sure no benchmark testing is running
on the service that to be deleted. Clear the IP interface for the
benchmark testing if it was using this service and the PDU type was set
to IP.

\

1.  Highlight a device, right click and select Snmp-Node -\> VFNZ
    Service Manager.

\

\

2.  Select the remove action from action list and select the service
    type for the service to be deleted. Click on Next to continue.

\

\

3.  Select the service name from the service list that you want to
    delete. Click on Next to delete the service.

\

\

\

4.  Select the NID to remove the service and update the traffic shaper
    configuration if needed

If the traffic shaper rate is left in blank, then the Burst Size value
will not be deployed to the device. If the value is changed from
default, then it gets deployed.

\

\

\

5.  Telnet to the NID device that has been removed from the EPLAN
    service, verify:

1/ vs has been removed from the device

2/ traffic profiling configuration is removed

3/ traffic shaper configuration is updated on the NNI port

\

See the output attached.

****

3.  #### Delete ELAN Service {.western}

This operation will remove the the ELAN service from all NID devices.

Before you delete a service, make sure no benchmark testing is running
on the service that to be deleted. Clear the IP interface for the
benchmark testing if it was using this service and the PDU type was set
to IP.

\

1.  Highlight a device, right click and select Snmp-Node -\> VFNZ
    Service Manager.

\

\

2.  Select the remove action from action list and select the service
    type for the service to be deleted. Click on Next to continue.

\

\

\

3.  Select the service name from the service list that you want to
    delete. Click on Next to delete the service.

\

\

\

4.  Select ALL in the Device identifier to remove to delete the
    service from all NIDs. Update the traffic shaper configuration if
    needed.

\

\

5.  Telnet to each NID device and verify:

1/ vs has been removed from the device

2/ traffic profiling configuration is removed

3/ traffic shaper configuration is updated on the NNI port as expected.

\

See the output attached.

\

****

6.  Verify the service directory has been removed from
    /tftpboot/provisioning/deployed directory on the ESM server.

\

\

****Vodafone ServiceManager User Guide**31**

\

Copyright 2012 Ciena Proprietary and Confidential

