:original_name: cce_02_0413.html

.. _cce_02_0413:

Kubernetes Metrics Server
=========================

Add-on Overview
---------------

From version 1.8 onwards, Kubernetes provides resource usage metrics, such as the container CPU and memory usage, through the Metrics API. These metrics can be directly accessed by users (for example, by using the **kubectl top** command) or used by controllers (for example, Horizontal Pod Autoscaler) in a cluster for decision-making. The specific component is metrics-server, which is used to substitute for Heapster for providing the similar functions. Heapster has been gradually abandoned since v1.11.

Add-on Parameters
-----------------

.. table:: **Table 1** Parameters

   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | Parameter | Mandatory | Type                                                                                   | Description                                                       |
   +===========+===========+========================================================================================+===================================================================+
   | basic     | No        | object                                                                                 | Basic configuration parameters, which do not need to be specified |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | flavor    | Yes       | :ref:`Table 2 <cce_02_0413__en-us_topic_0000001834687089_table8149147344>` object      | Flavor parameters                                                 |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | custom    | Yes       | :ref:`Table 3 <cce_02_0413__en-us_topic_0000001834687089_table109631116113412>` object | Custom parameters                                                 |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+

.. _cce_02_0413__en-us_topic_0000001834687089_table8149147344:

.. table:: **Table 2** flavor

   +-------------+-----------+---------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
   | Parameter   | Mandatory | Type                                                                                  | Description                                                           |
   +=============+===========+=======================================================================================+=======================================================================+
   | description | No        | String                                                                                | Add-on description                                                    |
   +-------------+-----------+---------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
   | name        | Yes       | String                                                                                | Add-on specification name. The value is fixed at **Single-instance**. |
   +-------------+-----------+---------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
   | replicas    | Yes       | String                                                                                | Number of pods. The default value is **1**.                           |
   +-------------+-----------+---------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
   | resources   | Yes       | :ref:`resources <cce_02_0413__en-us_topic_0000001834687089_table136520220346>` object | Container resource (CPU and memory) quotas                            |
   +-------------+-----------+---------------------------------------------------------------------------------------+-----------------------------------------------------------------------+

.. _cce_02_0413__en-us_topic_0000001834687089_table109631116113412:

.. table:: **Table 3** custom

   +------------------------+-----------+---------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter              | Mandatory | Type                                                                                        | Description                                                                                                                                                                                                                                  |
   +========================+===========+=============================================================================================+==============================================================================================================================================================================================================================================+
   | multiAZBalance         | No        | Bool                                                                                        | Whether the equivalent mode of multi-AZ deployment is enabled. If this function is enabled, the equivalent mode is used.                                                                                                                     |
   +------------------------+-----------+---------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | multiAZEnabled         | No        | Bool                                                                                        | Whether to deploy the add-on pods in multiple AZs. The default value is **false**. If this parameter is set to **true**, cross-AZ deployment is forcibly performed. If this parameter is set to **false**, cross-AZ deployment is preferred. |
   +------------------------+-----------+---------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | tolerations            | No        | List<Object> :ref:`Table 6 <cce_02_0413__en-us_topic_0000001834687089_table31791231173618>` | Tolerations of the add-on                                                                                                                                                                                                                    |
   +------------------------+-----------+---------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | node_match_expressions | No        | List<Object> :ref:`Table 6 <cce_02_0413__en-us_topic_0000001834687089_table31791231173618>` | Node affinity configuration of the add-on                                                                                                                                                                                                    |
   +------------------------+-----------+---------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0413__en-us_topic_0000001834687089_table136520220346:

.. table:: **Table 4** Data structure of the resources field

   +-------------+-----------+--------+--------------------------------------------------------+
   | Parameter   | Mandatory | Type   | Description                                            |
   +=============+===========+========+========================================================+
   | limitsCpu   | Yes       | String | CPU size limit (unit: m)                               |
   +-------------+-----------+--------+--------------------------------------------------------+
   | limitsMem   | Yes       | String | Memory size limit (unit: Mi)                           |
   +-------------+-----------+--------+--------------------------------------------------------+
   | name        | Yes       | String | Add-on name. The value is fixed at **metrics-server**. |
   +-------------+-----------+--------+--------------------------------------------------------+
   | requestsCpu | Yes       | String | Requested CPU size (unit: m)                           |
   +-------------+-----------+--------+--------------------------------------------------------+
   | requestsMem | Yes       | String | Requested memory size (unit: Mi)                       |
   +-------------+-----------+--------+--------------------------------------------------------+

.. table:: **Table 5** Taints and tolerations

   ================= ========= ====== ======================
   Parameter         Mandatory Type   Description
   ================= ========= ====== ======================
   key               No        String Taint key
   effect            No        String Taint policy
   operator          No        String Operator
   tolerationSeconds No        Int    Toleration time window
   ================= ========= ====== ======================

.. _cce_02_0413__en-us_topic_0000001834687089_table31791231173618:

.. table:: **Table 6** nodeMatchExpresssion node affinity

   ========= ========= ============ ==================
   Parameter Mandatory Type         Description
   ========= ========= ============ ==================
   key       No        String       Taint key
   values    No        List<String> Node affinity name
   operator  No        String       Operator
   ========= ========= ============ ==================

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
       "clusterID": "b78fb690-b82c-11ee-83cf-0255ac100b0f",
       "version": "1.3.39",
       "addonTemplateName": "metrics-server",
       "values": {
         "basic": {
           "image_version": "v0.6.2",
           "swr_addr": "***",
           "swr_user": "***",
           "rbac_enabled": true,
           "cluster_version": "v1.23"
         },
         "flavor": {
           "description": "Has only one instance",
           "name": "Single",
           "replicas": 1,
           "resources": [
             {
               "limitsCpu": "1000m",
               "limitsMem": "1000Mi",
               "name": "metrics-server",
               "requestsCpu": "100m",
               "requestsMem": "300Mi"
             }
           ],
           "category": [
             "CCE",
             "Turbo"
           ]
         },
         "custom": {
           "annotations": {},
           "multiAZBalance": false,
           "multiAZEnabled": false,
           "node_match_expressions": [],
           "tolerations": [
             {
               "key": "node.kubernetes.io/not-ready",
               "operator": "Exists",
               "effect": "NoExecute",
               "tolerationSeconds": 60
             },
             {
               "key": "node.kubernetes.io/unreachable",
               "operator": "Exists",
               "effect": "NoExecute",
               "tolerationSeconds": 60
             }
           ]
         }
       }
     }
   }
