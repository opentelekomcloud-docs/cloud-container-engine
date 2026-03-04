:original_name: cce_10_1069.html

.. _cce_10_1069:

Custom Agencies
===============

CCE clusters rely on various cloud services in areas like compute, storage, networking, and monitoring to function properly. To access these cloud resources, CCE clusters need authorization, which is handled through agencies. By default, CCE uses :ref:`system agencies <cce_10_0556>` to generate temporary access credentials. These credentials are used internally by the clusters to access cloud services. You can also configure a custom agency for your cluster. Once set, the cluster will use this custom agency to generate its temporary access credentials.

.. note::

   -  Custom agencies are supported only in clusters of v1.27 or later.
   -  Custom agencies are supported only in CCE standard clusters.
   -  Custom agencies do not support IAM 5.0 trust agencies.

Prerequisites
-------------

-  You need to create a custom agency of the cloud service type on the **Agencies** page of the IAM console and authorize it to CCE.
-  You need to authorize the created custom agency. CCE has preset the permissions required for cluster running as system policies. Some of them are mandatory, and the rest can be granted on demand based on what cluster functions you will use. For details, see :ref:`System Policies <cce_10_1069__section1348855433317>`.

   .. caution::

      Ensure that you have granted the required permissions to the created agency, or some functions of the cluster may be unavailable.

Configuring a Custom Agency During Cluster Creation
---------------------------------------------------

A custom agency can be configured during cluster creation only using APIs.

Call the API for and configure a custom agency.

The request body is as follows:

.. code-block::

   {
       "kind": "Cluster",
       "apiVersion": "v3",
       "metadata": {
           "name": "cce-cluster",
       },
       "spec": {
           "agencyName": "custom_agency_name",
           "category": "CCE",
           . . .
       }
   }

**custom_agency_name** is the name of the custom agency created on IAM.

Configuring a Custom Agency for a Running Cluster
-------------------------------------------------

Custom agencies can be configured for running clusters only using APIs.

Call the API for and configure a custom agency.

The request body is as follows:

.. code-block::

   {
       "spec": {
           "agencyName": "custom_agency_name"
       }
   }

**custom_agency_name** is the name of the custom agency created on IAM.

.. caution::

   -  If you use a custom agency in a cluster, do not delete the agency or allow it to expire, as this may cause certain cluster functions to become unavailable.
   -  After modifying the custom agency used in a cluster, allow some time for the modifications to apply.

.. _cce_10_1069__section1348855433317:

System Policies
---------------

+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| System Policy                   | Description                                                                              | Authorization Required                                                                          |
+=================================+==========================================================================================+=================================================================================================+
| CCEClusterManagedPolicy         | Permissions for using the basic functions of a CCE cluster                               | Yes                                                                                             |
+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| CCEClusterNodePolicy            | Permissions for using the basic functions of worker nodes in a CCE cluster               | Yes                                                                                             |
+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| CCEClusterTurboNetworkingPolicy | Permissions for using Cloud Native 2.0 networks in a CCE cluster                         | Required if the Cloud Native 2.0 network is used for a cluster                                  |
+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| CCEClusterVPCNetworkingPolicy   | Permissions for using VPC networks in a CCE cluster                                      | Required if the VPC network is used for a cluster                                               |
+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| CCEClusterLoadBalancingPolicy   | Permissions for using ELB capabilities in a CCE cluster                                  | No                                                                                              |
+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| CCEClusterCSIEVSPolicy          | Permissions for using EVS volumes in a CCE cluster                                       | No                                                                                              |
+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| CCEClusterCSIOBSPolicy          | Permissions for using OBS volumes in a CCE cluster                                       | No (OBS is a global service. To use OBS, grant all project service permissions.)                |
+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| CCEClusterCSISFSTurboPolicy     | Permissions for using SFS Turbo volumes in a CCE cluster                                 | No                                                                                              |
+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| CCEClusterGEIPPolicy            | Permissions for binding global EIPs to load balancers in a CCE cluster                   | No (Global EIP is a global service. To use global EIPs, grant all project service permissions.) |
+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| CCEClusterKMSPolicy             | Permissions for mounting encrypted credentials located outside the cluster to containers | No                                                                                              |
+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| CCEClusterLogPolicy             | Permissions for using log collection in a CCE cluster                                    | No                                                                                              |
+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| CCEClusterNodeAutoscalingPolicy | Permissions for using node auto scaling in a CCE cluster                                 | No                                                                                              |
+---------------------------------+------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
