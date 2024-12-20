:original_name: cce_02_0412.html

.. _cce_02_0412:

NGINX Ingress Controller
========================

Add-on Overview
---------------

The NGINX Ingress Controller add-on can automatically modify configurations when there are changes to pods in Services. This add-on uses Nginx's excellent stability, performance, and concurrent processing ability to effectively manage containerized applications at the application layer.

Add-on Parameters
-----------------

.. table:: **Table 1** Parameters

   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | Parameter | Mandatory | Type                                                                                   | Description                                                       |
   +===========+===========+========================================================================================+===================================================================+
   | basic     | No        | object                                                                                 | Basic configuration parameters, which do not need to be specified |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | flavor    | Yes       | :ref:`Table 2 <cce_02_0412__en-us_topic_0000001787887814_table8149147344>` object      | Flavor parameters                                                 |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | custom    | Yes       | :ref:`Table 3 <cce_02_0412__en-us_topic_0000001787887814_table109631116113412>` object | Custom parameters                                                 |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+

.. _cce_02_0412__en-us_topic_0000001787887814_table8149147344:

.. table:: **Table 2** flavor

   +-----------+-----------+---------------------------------------------------------------------------------------+---------------------------------------------+
   | Parameter | Mandatory | Type                                                                                  | Description                                 |
   +===========+===========+=======================================================================================+=============================================+
   | replicas  | Yes       | String                                                                                | Number of pods. The default value is **1**. |
   +-----------+-----------+---------------------------------------------------------------------------------------+---------------------------------------------+
   | resources | Yes       | :ref:`resources <cce_02_0412__en-us_topic_0000001787887814_table136520220346>` object | Container resource (CPU and memory) quotas  |
   +-----------+-----------+---------------------------------------------------------------------------------------+---------------------------------------------+

.. _cce_02_0412__en-us_topic_0000001787887814_table109631116113412:

.. table:: **Table 3** custom

   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter              | Mandatory | Type                                                                                    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
   +========================+===========+=========================================================================================+===========================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+
   | ingressClass           | Yes       | String                                                                                  | Controller name. The default value is **nginx**.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | namespace              | Yes       | String                                                                                  | Namespace where the add-on is in. The default value is **kube-system**.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | service                | Yes       | :ref:`Table 8 <cce_02_0412__en-us_topic_0000001787887814_table10226105116386>`          | Configuration of a Service that provides external access                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | config                 | No        | Map<String>String                                                                       | Nginx configuration parameters. For details, see `ConfigMaps <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/>`__.                                                                                                                                                                                                                                                                                                                                                                                                   |
   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | admissionWebhooks      | No        | :ref:`Table 7 <cce_02_0412__en-us_topic_0000001787887814_table11490104416357>`          | Configuration of ingress admission verification.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | metrics                | No        | :ref:`Table 9 <cce_02_0412__en-us_topic_0000001787887814_table131692816497>`            | Monitoring metric configuration.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | defaultBackendService  | No        | String                                                                                  | Default 404 service, which is in the format of *<namespace>*\ **/**\ *<service_name>*.                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | extraArgs              | No        | :ref:`Table 10 <cce_02_0412__en-us_topic_0000001787887814_table193381337574>`           | Extended parameter configuration.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | multiAZEnable          | No        | bool                                                                                    | Whether to enable the forcible mode of multi-AZ deployment for the deployment component. The default value is **false**. Deployment pods of the add-on will be forcibly scheduled to nodes in different AZs. If there are fewer AZs than pods, the extra pods will fail to run. If both **multiAZEnable** and **multiAZBalance** are set to true, the settings of **multiAZBalance** take effect, which means, the equivalent mode of multi-AZ deployment is used.                                                                                        |
   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | multiAZBalance         | No        | bool                                                                                    | Whether to enable the equivalent mode of multi-AZ deployment for the deployment component. The default value is **false**. Deployment pods of the add-on are evenly scheduled to the nodes in the cluster in each AZ. If a new AZ is added, it is recommended that you increase add-on pods for cross-AZ HA deployment. With the equivalent multi-AZ deployment, the difference between the number of add-on pods in different AZs will be less than or equal to 1. If resources in one of the AZs are insufficient, pods cannot be scheduled to that AZ. |
   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | tolerations            | No        | Array of :ref:`Table 5 <cce_02_0412__en-us_topic_0000001787887814_table1347114825310>`  | Toleration configuration                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | node_match_expressions | No        | Array of :ref:`Table 6 <cce_02_0412__en-us_topic_0000001787887814_table31791231173618>` | Add-on pod affinity configuration                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
   +------------------------+-----------+-----------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0412__en-us_topic_0000001787887814_table136520220346:

.. table:: **Table 4** Data structure of the resources field

   +-------------+-----------+--------+-------------------------------------------------------+
   | Parameter   | Mandatory | Type   | Description                                           |
   +=============+===========+========+=======================================================+
   | limitsCpu   | Yes       | String | CPU size limit (unit: m)                              |
   +-------------+-----------+--------+-------------------------------------------------------+
   | limitsMem   | Yes       | String | Memory size limit (unit: Mi)                          |
   +-------------+-----------+--------+-------------------------------------------------------+
   | name        | Yes       | String | Add-on name. The value is fixed at **nginx-ingress**. |
   +-------------+-----------+--------+-------------------------------------------------------+
   | requestsCpu | Yes       | String | Requested CPU size (unit: m)                          |
   +-------------+-----------+--------+-------------------------------------------------------+
   | requestsMem | Yes       | String | Requested memory size (unit: Mi)                      |
   +-------------+-----------+--------+-------------------------------------------------------+

.. _cce_02_0412__en-us_topic_0000001787887814_table1347114825310:

.. table:: **Table 5** Taints and tolerations

   ================= ========= ====== ======================
   Parameter         Mandatory Type   Description
   ================= ========= ====== ======================
   key               No        String Taint key
   effect            No        String Taint policy
   operator          No        String Operator
   tolerationSeconds No        Int    Toleration time window
   ================= ========= ====== ======================

.. _cce_02_0412__en-us_topic_0000001787887814_table31791231173618:

.. table:: **Table 6** nodeMatchExpresssion node affinity

   ========= ========= ============ ==================
   Parameter Mandatory Type         Description
   ========= ========= ============ ==================
   key       No        String       Taint key
   values    No        List<String> Node affinity name
   operator  No        String       Operator
   ========= ========= ============ ==================

.. _cce_02_0412__en-us_topic_0000001787887814_table11490104416357:

.. table:: **Table 7** admissionWebhook

   +-----------+-----------+------+-------------------------------------------------------------------------------------------+
   | Parameter | Mandatory | Type | Description                                                                               |
   +===========+===========+======+===========================================================================================+
   | enable    | No        | bool | Whether to enable ingress resource admission verification. The default value is **true**. |
   +-----------+-----------+------+-------------------------------------------------------------------------------------------+

.. _cce_02_0412__en-us_topic_0000001787887814_table10226105116386:

.. table:: **Table 8** service

   +----------------+-----------+-------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter      | Mandatory | Type              | Description                                                                                                                                                                                                                      |
   +================+===========+===================+==================================================================================================================================================================================================================================+
   | annotations    | No        | Map<String>String | Annotations of a Service that provides external access. This parameter is only for configurations of ELB-related options, such as **kubernetes.io/elb.class**, **kubernetes.io/elb.id**, and **kubernetes.io/elb.pass-through**. |
   +----------------+-----------+-------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | loadBalancerIP | No        | String            | Load balancer IP address used by the Service that is interconnected with the load balancer.                                                                                                                                      |
   +----------------+-----------+-------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0412__en-us_topic_0000001787887814_table131692816497:

.. table:: **Table 9** metrics

   +----------------------+-----------+--------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter            | Mandatory | Type   | Description                                                                                                                                                                                          |
   +======================+===========+========+======================================================================================================================================================================================================+
   | enable               | No        | bool   | Whether to monitor metrics. The default value is **true**.                                                                                                                                           |
   +----------------------+-----------+--------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | excludeSocketMetrics | No        | String | Shielded monitoring metrics. The default value is **"nginx_ingress_controller_success,nginx_ingress_controller_header_duration_seconds,nginx_ingress_controller_ingress_upstream_latency_seconds"**. |
   +----------------------+-----------+--------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0412__en-us_topic_0000001787887814_table193381337574:

.. table:: **Table 10** extraArg extended parameter

   +------------------------+-----------+--------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter              | Mandatory | Type   | Description                                                                                                                                                           |
   +========================+===========+========+=======================================================================================================================================================================+
   | default-ssl-certificat | No        | String | Default certificate configuration. For details, see `Default SSL Certificate <https://kubernetes.github.io/ingress-nginx/user-guide/tls/#default-ssl-certificate>`__. |
   +------------------------+-----------+--------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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
           "clusterID": "2292498e-**************ac1001ba",
           "version": "2.2.52",
           "addonTemplateName": "nginx-ingress",
           "values": {
                           "basic": {
                   "basickey":"val"
               },
               "flavor": {
                   "replicas": 2,
                   "resources": [{
                       "limitsCpu": "8000m",
                       "limitsMem": "4000Mi",
                       "name": "nginx-ingress",
                       "requestsCpu": "8000m",
                       "requestsMem": "4000Mi"
                   }]
               },
               "custom": {
                   "config": {
                       "keep-alive-requests": "100"
                   },
                   "defaultBackend": {
                       "enabled": true
                   },
                   "defaultBackendService": "",
                   "extraArgs": {
                       "default-ssl-certificate": ""
                   },
                   "ingressClass": "nginx",
                   "multiAZBalance": false,
                   "multiAZEnabled": false,
                   "namespace": "kube-system",
                   "node_match_expressions": [],
                   "service": {
                       "annotations": {
                           "kubernetes.io/elb.class": "performance",
                           "kubernetes.io/elb.id": "8d6bd485-d8ac-4693-815d-9d54d79b0666"
                       },
                       "loadBalancerIP": ""
                   },
                   "tolerations": [{
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
                   }]
               }
           }
       }
   }
