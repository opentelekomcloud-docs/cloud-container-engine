:original_name: cce_02_0417.html

.. _cce_02_0417:

Volcano Scheduler
=================

Add-on Overview
---------------

Volcano is a batch scheduling platform based on Kubernetes. It provides a series of features required by machine learning, deep learning, bioinformatics, genomics, and other big data applications, as a powerful supplement to Kubernetes capabilities.

Add-on Parameters
-----------------

.. table:: **Table 1** Parameters

   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | Parameter | Mandatory | Type                                                                                   | Description                                                       |
   +===========+===========+========================================================================================+===================================================================+
   | basic     | No        | :ref:`Table 2 <cce_02_0417__en-us_topic_0000001834687093_table119253411314>` object    | Basic configuration parameters, which do not need to be specified |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | flavor    | Yes       | :ref:`Table 3 <cce_02_0417__en-us_topic_0000001834687093_table8149147344>` object      | Flavor parameters                                                 |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+
   | custom    | Yes       | :ref:`Table 4 <cce_02_0417__en-us_topic_0000001834687093_table109631116113412>` object | Custom parameters                                                 |
   +-----------+-----------+----------------------------------------------------------------------------------------+-------------------------------------------------------------------+

.. _cce_02_0417__en-us_topic_0000001834687093_table119253411314:

.. table:: **Table 2** Configuration of basic

   +-------------+-----------+--------+---------------------------------------------------------------------------------+
   | Parameter   | Mandatory | Type   | Description                                                                     |
   +=============+===========+========+=================================================================================+
   | swr_addr    | Yes       | String | Add-on download address, which does not need to be specified                    |
   +-------------+-----------+--------+---------------------------------------------------------------------------------+
   | swr_user    | Yes       | String | User who can download the add-on. This parameter does not need to be specified. |
   +-------------+-----------+--------+---------------------------------------------------------------------------------+
   | platform    | Yes       | String | Add-on platform, which does not need to be specified                            |
   +-------------+-----------+--------+---------------------------------------------------------------------------------+
   | escEndpoint | Yes       | String | ECS address, which does not need to be specified                                |
   +-------------+-----------+--------+---------------------------------------------------------------------------------+

.. _cce_02_0417__en-us_topic_0000001834687093_table8149147344:

.. table:: **Table 3** Configuration of flavor

   +-------------+-----------+---------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
   | Parameter   | Mandatory | Type                                                                                  | Description                                                           |
   +=============+===========+=======================================================================================+=======================================================================+
   | description | No        | String                                                                                | Add-on description                                                    |
   +-------------+-----------+---------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
   | name        | Yes       | String                                                                                | Add-on specification name. The value is fixed at **Single-instance**. |
   +-------------+-----------+---------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
   | replicas    | Yes       | String                                                                                | Number of pods. The default value is **1**.                           |
   +-------------+-----------+---------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
   | resources   | Yes       | :ref:`resources <cce_02_0417__en-us_topic_0000001834687093_table136520220346>` object | Container resource (CPU and memory) quotas                            |
   +-------------+-----------+---------------------------------------------------------------------------------------+-----------------------------------------------------------------------+

.. _cce_02_0417__en-us_topic_0000001834687093_table109631116113412:

.. table:: **Table 4** Configuration of custom

   +--------------------------+-----------------+-----------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | Parameter                | Mandatory       | Type                                                                                    | Description                                                                                                       |
   +==========================+=================+=========================================================================================+===================================================================================================================+
   | multiAZEnabled           | No              | Bool                                                                                    | Whether multi-AZ deployment is enabled                                                                            |
   |                          |                 |                                                                                         |                                                                                                                   |
   |                          |                 |                                                                                         | **true**: The Volcano Scheduler pods are deployed based on anti-affinity.                                         |
   +--------------------------+-----------------+-----------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | node_match_expressions   | No              | Array of :ref:`Table 7 <cce_02_0417__en-us_topic_0000001787887814_table31791231173618>` | Expression for matching the Volcano Scheduler pods to nodes                                                       |
   +--------------------------+-----------------+-----------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | tolerations              | No              | Array of :ref:`Table 6 <cce_02_0417__en-us_topic_0000001787887814_table1347114825310>`  | The format is the same as that of Kubernetes tolerations. It is used to add taints to Volcano Scheduler pod pods. |
   +--------------------------+-----------------+-----------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | oversubscription_ratio   | No              | int                                                                                     | Node resource overcommitment ratio in the Volcano scheduling environment                                          |
   +--------------------------+-----------------+-----------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | descheduler_enable       | No              | Bool                                                                                    | Whether to support rescheduling                                                                                   |
   +--------------------------+-----------------+-----------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | enable_workload_balancer | No              | Bool                                                                                    | Whether load balancers are supported                                                                              |
   +--------------------------+-----------------+-----------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | default_scheduler_conf   | Yes             | yaml                                                                                    | The format is the same as that of Volcano configuration YAML.                                                     |
   +--------------------------+-----------------+-----------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
   | deschedulerPolicy        | No              | yaml                                                                                    | The format is the same as that of the volcano descheduling configuration YAML.                                    |
   +--------------------------+-----------------+-----------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0417__en-us_topic_0000001834687093_table136520220346:

.. table:: **Table 5** Data structure of the resources field

   +-----------------+-----------------+-----------------+---------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                             |
   +=================+=================+=================+=========================================================+
   | limitsCpu       | Yes             | String          | CPU size limit (unit: m)                                |
   |                 |                 |                 |                                                         |
   |                 |                 |                 | Default value: **250m**                                 |
   +-----------------+-----------------+-----------------+---------------------------------------------------------+
   | limitsMem       | Yes             | String          | Memory size limit (unit: Mi)                            |
   |                 |                 |                 |                                                         |
   |                 |                 |                 | Default value: **512Mi**                                |
   +-----------------+-----------------+-----------------+---------------------------------------------------------+
   | name            | Yes             | String          | Add-on name. The value is fixed at **virtual-kubelet**. |
   +-----------------+-----------------+-----------------+---------------------------------------------------------+
   | requestsCpu     | Yes             | String          | Requested CPU size (unit: m)                            |
   |                 |                 |                 |                                                         |
   |                 |                 |                 | Default value: **250m**                                 |
   +-----------------+-----------------+-----------------+---------------------------------------------------------+
   | requestsMem     | Yes             | String          | Requested memory size (unit: Mi)                        |
   |                 |                 |                 |                                                         |
   |                 |                 |                 | Default value: **512Mi**                                |
   +-----------------+-----------------+-----------------+---------------------------------------------------------+

.. _cce_02_0417__en-us_topic_0000001787887814_table1347114825310:

.. table:: **Table 6** Taints and tolerations

   ================= ========= ====== ======================
   Parameter         Mandatory Type   Description
   ================= ========= ====== ======================
   key               No        String Taint key
   effect            No        String Taint policy
   operator          No        String Operator
   tolerationSeconds No        Int    Toleration time window
   ================= ========= ====== ======================

.. _cce_02_0417__en-us_topic_0000001787887814_table31791231173618:

.. table:: **Table 7** nodeMatchExpresssion node affinity

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
           "clusterID": "2292498e-d169-******-0255ac1001ba",
           "version": "1.12.14",
           "addonTemplateName": "volcano",
           "values": {
                           "basic": {
                   "swr_addr":"x.x.x.x:20202",
                                   "swr_user":"hwofficial",
                                   "platform":"linux-amd64",
                                   "ecsEndpoint":"x.x.x.x"
               },
               "flavor": {
                   "resources": [{
                       "limitsCpu": "500m",
                       "limitsMem": "500Mi",
                       "name": "volcano-scheduler",
                       "requestsCpu": "2000m",
                       "requestsMem": "2000Mi"
                   },
                   {
                       "limitsCpu": "500m",
                       "limitsMem": "500Mi",
                       "name": "volcano-controller",
                       "requestsCpu": "2000m",
                       "requestsMem": "2000Mi"
                   },
                   {
                       "limitsCpu": "200m",
                       "limitsMem": "500Mi",
                       "name": "volcano-admission",
                       "requestsCpu": "500m",
                       "requestsMem": "500Mi"
                   },
                   {
                       "limitsCpu": "100m",
                       "limitsMem": "150Mi",
                       "name": "volcano-agent",
                       "requestsCpu": "200m",
                       "requestsMem": "200Mi"
                   },
                   {
                       "limitsCpu": "50m",
                       "limitsMem": "50Mi",
                       "name": "volcano-exporter",
                       "requestsCpu": "100m",
                       "requestsMem": "100Mi"
                   },
                   {
                       "limitsCpu": "500m",
                       "limitsMem": "256Mi",
                       "name": "volcano-descheduler",
                       "requestsCpu": "1000m",
                       "requestsMem": "512Mi"
                   },
                   {
                       "limitsCpu": "300m",
                       "limitsMem": "300Mi",
                       "name": "volcano-recommender",
                       "requestsCpu": "500m",
                       "requestsMem": "500Mi"
                   },
                   {
                       "limitsCpu": "200m",
                       "limitsMem": "200Mi",
                       "name": "volcano-recommender-prometheus-adapter",
                       "requestsCpu": "300m",
                       "requestsMem": "300Mi"
                   }]
               },
               "custom": {
                   "default_scheduler_conf": {
                                           "actions":"allocate,backfill,preempt",
                                           "tiers": [{
                                                   "plugins": [{
                                                           "name":"priority"
                                                   },
                                                   {
                                                           "name":"conformance"
                                                   },
                                                   {
                                                           "name":"gang",
                                                           "enablePreemptable":"false",
                                                           "enableJobStarving":"false",
                                                   }]
                                           },
                                           {
                                                   "plugins": [{
                                                           "name":"predicates"
                                                   },
                                                   {
                                                           "name":"nodeorder"
                                                   },
                                                   {
                                                           "name":"drf",
                                                           "enablePreemptable":"false",
                                                   }]
                                           },
                                           {
                                                   "plugins": [{
                                                           "name":"cce-gpu-topology-predicate"
                                                   },
                                                   {
                                                           "name":"cce-gpu-topology-priority"
                                                   },
                                                   {
                                                           "name":"xgpu"
                                                   }]
                                           },
                                           {
                                                   "plugins": [{
                                                           "name":"nodelocalvolume"
                                                   },
                                                   {
                                                           "name":"nodeemptydirvolume"
                                                   },
                                                   {
                                                           "name":"nodeCSIscheduling"
                                                   },
                                                   {
                                                           "name":"networkresource"
                                                   }]
                                           }],
                                           "metrics":{
                                                   "type":"",
                                                   "interval":30s,
                                           }

                                   }
               }
           }
       }
   }
