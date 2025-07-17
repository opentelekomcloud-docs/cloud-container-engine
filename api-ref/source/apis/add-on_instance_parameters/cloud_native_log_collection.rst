:original_name: cce_02_0422.html

.. _cce_02_0422:

Cloud Native Log Collection
===========================

Add-on Overview
---------------

The Cloud Native Logging add-on (log-agent) is developed based on Fluent Bit and OpenTelemetry for collecting logs and Kubernetes events. This add-on supports CRD-based log collection policies. It collects and forwards standard output logs, container file logs, node logs, and Kubernetes event logs in a cluster based on configured policies. It also reports Kubernetes events to AOM for configuring event alarms. By default, all abnormal events and some normal events are reported.

Add-on Parameters
-----------------

.. table:: **Table 1** Parameters

   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | Parameter | Mandatory | Type                                                                                   | Description                                                       |
   +===========+===========+========================================================================================+===================================================================+
   | basic     | No        | object                                                                                 | Basic configuration parameters, which do not need to be specified |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | flavor    | Yes       | :ref:`Table 2 <cce_02_0422__en-us_topic_0000001834767029_table8149147344>` object      | Flavor parameters                                                 |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | custom    | Yes       | :ref:`Table 3 <cce_02_0422__en-us_topic_0000001834767029_table109631116113412>` object | Custom parameters                                                 |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+

.. _cce_02_0422__en-us_topic_0000001834767029_table8149147344:

.. table:: **Table 2** Configuration of flavor

   +-------------+-----------+---------------------------------------------------------------------------------------+---------------------------------------------------------------------------+
   | Parameter   | Mandatory | Type                                                                                  | Description                                                               |
   +=============+===========+=======================================================================================+===========================================================================+
   | description | No        | String                                                                                | Add-on description                                                        |
   +-------------+-----------+---------------------------------------------------------------------------------------+---------------------------------------------------------------------------+
   | name        | Yes       | String                                                                                | Add-on specification name. The value is fixed at **Single-instance**.     |
   +-------------+-----------+---------------------------------------------------------------------------------------+---------------------------------------------------------------------------+
   | replicas    | Yes       | String                                                                                | Number of otel-collector component instances. The default value is **1**. |
   +-------------+-----------+---------------------------------------------------------------------------------------+---------------------------------------------------------------------------+
   | resources   | Yes       | :ref:`resources <cce_02_0422__en-us_topic_0000001834767029_table136520220346>` object | Container resource (CPU and memory) quotas                                |
   +-------------+-----------+---------------------------------------------------------------------------------------+---------------------------------------------------------------------------+

.. _cce_02_0422__en-us_topic_0000001834767029_table109631116113412:

.. table:: **Table 3** Configuration of custom

   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter           | Mandatory       | Type            | Description                                                                                                                                                                                                                                                                          |
   +=====================+=================+=================+======================================================================================================================================================================================================================================================================================+
   | caCert              | Yes             | String          | Specifies a client certificate, which is encrypted using Base64. This parameter is optional during installation. It is generated by the CCE add-on center and used for two-way authentication between components and webhook requests.                                               |
   |                     |                 |                 |                                                                                                                                                                                                                                                                                      |
   |                     |                 |                 | Domain name to be issued: **\*.monitoring.svc**.                                                                                                                                                                                                                                     |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | serverCert          | Yes             | String          | Specifies a server certificate, which is encrypted using Base64. This parameter is optional during installation. It is generated by CCE add-ons and used for two-way authentication between components and webhook requests. Domain name to be issued: **\*.monitoring.svc**.        |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | serverKey           | Yes             | String          | Specifies the private key of a server, which is encrypted using Base64. This parameter is optional during installation. It is generated by CCE add-ons and used for two-way authentication between components and webhook requests. Domain name to be issued: **\*.monitoring.svc**. |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | accessKey           | No              | String          | Specifies the access key ID of a user, which is used to request AOM and LTS APIs. If this parameter is left blank, a temporary AK/SK is used.                                                                                                                                        |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | secretKey           | No              | String          | Specifies the access key of a user, which is used to request AOM and LTS APIs. If this parameter is left blank, a temporary AK/SK is used.                                                                                                                                           |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | createDefaultStdout | No              | Bool            | Specifies whether to create the default policy for collecting container standard output logs and reporting them to LTS. This parameter is valid only after it is specified during the add-on installation. The default value is **false**.                                           |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | createDefaultEvent  | No              | Bool            | Specifies whether to create the default policy for collecting Kubernetes events and reporting them to LTS. This parameter is valid only after it is specified during the add-on installation. The default value is **false**.                                                        |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | multiAZEnabled      | No              | Bool            | Specifies whether to deploy the add-on pods in multiple AZs. The default value is **false**. If this parameter is set to **true**, cross-AZ deployment is forcibly performed. If this parameter is set to **false**, cross-AZ deployment is preferred.                               |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | cluster_category    | No              | String          | The value is fixed at **CCE**.                                                                                                                                                                                                                                                       |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | ltsAccessEndpoint   | No              | String          | Specifies the address for reporting logs to LTS. If this parameter is left blank, the address specified in **basic** is used.                                                                                                                                                        |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | ltsEndpoint         | No              | String          | Specifies the address of the LTS APIs. If this parameter is left blank, the address specified in **basic** is used.                                                                                                                                                                  |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | aomEndpoint         | No              | String          | Specifies the address for reporting events to AOM. If this parameter is left blank, the address specified in **basic** is used.                                                                                                                                                      |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | projectID           | Yes             | String          | Specifies the ID of the project to which the current CCE cluster belongs. This parameter is optional during installation.                                                                                                                                                            |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | clusterID           | Yes             | String          | Specifies the ID of the current CCE cluster. This parameter is optional during installation.                                                                                                                                                                                         |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | clusterName         | Yes             | String          | Specifies the name of the current CCE cluster.                                                                                                                                                                                                                                       |
   +---------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0422__en-us_topic_0000001834767029_table136520220346:

.. table:: **Table 4** Data structure of the resources field

   +-----------------+-----------------+-----------------+---------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                               |
   +=================+=================+=================+===========================================================================+
   | limitsCpu       | Yes             | String          | CPU size limit (unit: m)                                                  |
   +-----------------+-----------------+-----------------+---------------------------------------------------------------------------+
   | limitsMem       | Yes             | String          | Memory size limit (unit: Mi)                                              |
   +-----------------+-----------------+-----------------+---------------------------------------------------------------------------+
   | name            | Yes             | String          | Component name. Options:                                                  |
   |                 |                 |                 |                                                                           |
   |                 |                 |                 | **fluent-bit**: component for collecting node logs                        |
   |                 |                 |                 |                                                                           |
   |                 |                 |                 | **cop-logs**: generates soft links for collected files on each node.      |
   |                 |                 |                 |                                                                           |
   |                 |                 |                 | **log-operator**: generates fluent-bit and otel-collector configurations. |
   |                 |                 |                 |                                                                           |
   |                 |                 |                 | **otel-collector**: reports collected logs and events to AOM and LTS.     |
   +-----------------+-----------------+-----------------+---------------------------------------------------------------------------+
   | requestsCpu     | Yes             | String          | Requested CPU size (unit: m)                                              |
   +-----------------+-----------------+-----------------+---------------------------------------------------------------------------+
   | requestsMem     | Yes             | String          | Requested memory size (unit: Mi)                                          |
   +-----------------+-----------------+-----------------+---------------------------------------------------------------------------+

Example Request
---------------

.. code-block::

   {
     "kind": "Addon",
     "apiVersion": "v3",
     "metadata": {
       "annotations": {
         "addon.install/type": "install"
       }
     },
     "spec": {
       "clusterID": "ccbe7bdf-4**9-3**b-b**4-0********78",
       "version": "1.4.4",
       "addonTemplateName": "log-agent",
       "values": {
         "basic": {
           "aomEndpoint": "https://***",
           "iam_url": "***",
           "ltsAccessEndpoint": "https://***:8102",
           "ltsEndpoint": "https://***",
           "region": "***",
           "swr_addr": "***",
           "swr_user": "***",
           "rbac_enabled": true,
           "cluster_version": "v1.25"
         },
         "flavor": {
           "description": "Recommanded when the number of logs per second does not exceed 5000.",
           "name": "Low",
           "replicas": 1,
           "resources": [
             {
               "limitsCpu": "500m",
               "limitsMem": "500Mi",
               "name": "fluent-bit",
               "requestsCpu": "100m",
               "requestsMem": "200Mi"
             },
             {
               "limitsCpu": 1,
               "limitsMem": "500Mi",
               "name": "cop-logs",
               "requestsCpu": "100m",
               "requestsMem": "100Mi"
             },
             {
               "limitsCpu": "500m",
               "limitsMem": "500Mi",
               "name": "log-operator",
               "requestsCpu": "100m",
               "requestsMem": "100Mi"
             },
             {
               "limitsCpu": 1,
               "limitsMem": "2Gi",
               "name": "otel-collector",
               "requestsCpu": "200m",
               "requestsMem": "1Gi"
             }
           ],
           "category": [
             "CCE",
             "Turbo"
           ]
         },
         "custom": {
           "accessKey": "",
           "aomEndpoint": "https://***",
           "aomPrivateEndpointIP": "",
           "caCert": "",
           "clusterID": "",
           "clusterName": "clusterName",
           "cluster_category": "CCE",
           "createAudit": false,
           "createDefaultEvent": false,
           "createDefaultEventToAOM": true,
           "createDefaultStdout": false,
           "createKubeApiserver": false,
           "createKubeControllerManager": false,
           "createKubeScheduler": false,
           "ltsAccessEndpoint": "https://***:8102",
           "ltsAuditStreamID": "",
           "ltsEndpoint": "https://***",
           "ltsEventStreamID": "",
           "ltsGroupID": "",
           "ltsKubeApiserverStreamID": "",
           "ltsKubeControllerManagerStreamID": "",
           "ltsKubeSchedulerStreamID": "",
           "ltsLogReportDomain": "",
           "ltsPrivateEndpointIP": "",
           "ltsStdoutStreamID": "",
           "multiAZEnabled": false,
           "paasakskEnable": true,
           "projectID": "",
           "secretKey": "",
           "securityToken": "",
           "serverCert": "",
           "serverKey": ""
         }
       }
     }
   }
