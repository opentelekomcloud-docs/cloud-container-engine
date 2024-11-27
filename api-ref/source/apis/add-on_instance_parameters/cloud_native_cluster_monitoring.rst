:original_name: cce_02_0421.html

.. _cce_02_0421:

Cloud Native Cluster Monitoring
===============================

Add-on Overview
---------------

The Cluster Native Cluster Monitoring add-on (kube-prometheus-stack) uses Prometheus-operator and Prometheus to provide easy-to-use, end-to-end Kubernetes cluster monitoring.

This add-on enables monitoring data to connect to the monitoring center, so that you can view monitoring data and configure alarms on the console.

Add-on Parameters
-----------------

.. table:: **Table 1** Parameters

   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | Parameter | Mandatory | Type                                                                                   | Description                                                       |
   +===========+===========+========================================================================================+===================================================================+
   | basic     | No        | object                                                                                 | Basic configuration parameters, which do not need to be specified |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | flavor    | Yes       | :ref:`Table 2 <cce_02_0421__en-us_topic_0000001834687097_table8149147344>` object      | Flavor parameters                                                 |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | custom    | Yes       | :ref:`Table 3 <cce_02_0421__en-us_topic_0000001834687097_table109631116113412>` object | Custom parameters                                                 |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+

.. _cce_02_0421__en-us_topic_0000001834687097_table8149147344:

.. table:: **Table 2** Configuration of flavor

   +-------------+-----------+---------------------------------------------------------------------------------------+--------------------------------------------+
   | Parameter   | Mandatory | Type                                                                                  | Description                                |
   +=============+===========+=======================================================================================+============================================+
   | description | No        | String                                                                                | Add-on description                         |
   +-------------+-----------+---------------------------------------------------------------------------------------+--------------------------------------------+
   | name        | Yes       | String                                                                                | Add-on specification name                  |
   +-------------+-----------+---------------------------------------------------------------------------------------+--------------------------------------------+
   | resources   | Yes       | :ref:`resources <cce_02_0421__en-us_topic_0000001834687097_table136520220346>` object | Container resource (CPU and memory) quotas |
   +-------------+-----------+---------------------------------------------------------------------------------------+--------------------------------------------+

.. _cce_02_0421__en-us_topic_0000001834687097_table109631116113412:

.. table:: **Table 3** Configuration of custom

   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                 | Mandatory | Type    | Description                                                                                                                                                  |
   +===========================+===========+=========+==============================================================================================================================================================+
   | aom_enable                | No        | Boolean | Whether to interconnect with AOM                                                                                                                             |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | aom_instance_id           | No        | String  | AOM instance ID. This parameter is mandatory when AOM is interconnected with the cluster.                                                                    |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | aom_project_id            | No        | String  | ID of the project to which the AOM instance belongs. This parameter is mandatory when AOM is interconnected with the cluster.                                |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | aom_auth_type             | No        | String  | Authentication type for interconnecting with AOM. This parameter is mandatory when AOM is interconnected with the cluster. The value is fixed at **Bearer**. |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | aom_app_key               | No        | String  | app_key for interconnecting with AOM. This parameter is mandatory when AOM is interconnected with the cluster.                                               |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | aom_app_secret            | No        | String  | app_secret for interconnecting with AOM. This parameter is mandatory when AOM is interconnected with the cluster.                                            |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | deploy_mode               | Yes       | String  | Prometheus add-on model. The value can be **agent** or **server**. **agent** is recommended.                                                                 |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | enablethird               | No        | Boolean | Whether to report metrics to the third-party monitoring system.                                                                                              |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | url_third                 | No        | String  | URL for the third-party monitoring system to report metrics.                                                                                                 |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | basic_auth_username_third | No        | String  | Account for interconnecting with a third-party monitoring system (in the BasicAuth authentication mode)                                                      |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | basic_auth_password_third | No        | String  | Password for interconnecting with a third-party monitoring system (in the BasicAuth authentication mode)                                                     |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | bearer_token              | No        | String  | Token for interconnecting with a third-party monitoring system (in the BearerToken authentication mode)                                                      |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | cluster                   | Yes       | String  | Cluster name                                                                                                                                                 |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | clusterId                 | Yes       | String  | Cluster ID                                                                                                                                                   |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | enable_custom_metrics     | No        | Boolean | Whether to enable custom metric collection. The default value is **false**.                                                                                  |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | highAvailability          | No        | Boolean | Whether HA is enabled. The default value is **false**.                                                                                                       |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | scrapeInterval            | Yes       | String  | Default metric collection period. The default value is **15s**.                                                                                              |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | shards                    | No        | Integer | Number of collected fragments. This parameter is valid only in the agent mode. The default value is **1**.                                                   |
   +---------------------------+-----------+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0421__en-us_topic_0000001834687097_table136520220346:

.. table:: **Table 4** Data structure of the resources field

   =========== ========= ====== ================================
   Parameter   Mandatory Type   Description
   =========== ========= ====== ================================
   limitsCpu   Yes       String CPU size limit (unit: m)
   limitsMem   Yes       String Memory size limit (unit: Mi)
   name        Yes       String Workload name
   requestsCpu Yes       String Requested CPU size (unit: m)
   requestsMem Yes       String Requested memory size (unit: Mi)
   =========== ========= ====== ================================

Example Request
---------------

.. code-block::

   {
       "kind":"Addon",
       "apiVersion":"v3",
       "metadata":{
           "annotations":{
               "addon.install/type":"install"
           }
       },
       "spec":{
           "clusterID":"b889764e-c100-11ee-80cf-0255ac100b0f",
           "version":"3.9.5",
           "addonTemplateName":"cie-collector",
           "values":{
               "basic":{
                   "aom_endpoint":"https://***",
                   "aom_url":"https://***",
                   "region_id":"***",
                   "swr_addr":"***",
                   "swr_user":"***",
                   "rbac_enabled":true,
                   "cluster_version":"v1.27"
               },
               "flavor":{
                   "deploy_mode":"agent",
                   "description":"Default flavor for agent mode.",
                   "name":"Agent-Default",
                   "resources":[
                       {
                           "limitsCpu":"500m",
                           "limitsMem":"500Mi",
                           "name":"prometheusOperator",
                           "requestsCpu":"100m",
                           "requestsMem":"100Mi"
                       },
                       {
                           "limitsCpu":"4",
                           "limitsMem":"8Gi",
                           "name":"prometheus",
                           "requestsCpu":"500m",
                           "requestsMem":"500Mi"
                       },
                       {
                           "limitsCpu":"500m",
                           "limitsMem":"500Mi",
                           "name":"kubeStateMetrics",
                           "requestsCpu":"200m",
                           "requestsMem":"200Mi"
                       },
                       {
                           "limitsCpu":"500m",
                           "limitsMem":"1Gi",
                           "name":"nodeExporter",
                           "requestsCpu":"200m",
                           "requestsMem":"100Mi"
                       }
                   ],
                   "category":[
                       "CCE",
                       "Turbo"
                   ]
               },
               "custom":{
                   "aom_app_key":"******",
                   "aom_app_secret":"******",
                   "aom_auth_type":"Bearer",
                   "aom_enable":true,
                   "aom_instance_id":"a2bf5a6f-2c64-4a7f-a369-78dccd9cb6fd",
                   "aom_project_id":"b6315dd3d0ff4be5b31a963256794989",
                   "basic_auth_password_third":"",
                   "basic_auth_username_third":"",
                   "bearer_token":"",
                   "cluster":"test-cluster",
                   "clusterId":"b889764e-c100-11ee-80cf-0255ac100b0f",
                   "deploy_mode":"agent",
                   "enable_custom_metrics":true,
                   "enablethird":false,
                   "highAvailability":false,
                   "projectId":"b6315dd3d0ff4be5b31a963256794989",
                   "region":"***",
                   "scrapeInterval":"15s",
                   "shards":1,
                   "url_third":"",
               }
           }
       }
   }
