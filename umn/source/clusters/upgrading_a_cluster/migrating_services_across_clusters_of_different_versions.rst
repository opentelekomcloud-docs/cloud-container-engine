:original_name: cce_10_0210.html

.. _cce_10_0210:

Migrating Services Across Clusters of Different Versions
========================================================

Application Scenarios
---------------------

This section describes how to migrate services from a cluster of an earlier version to a cluster of a later version in CCE.

This operation is applicable when a cross-version cluster upgrade is required (for example, upgrade from v1.19.\* to v1.28.*) and new clusters can be created for service migration.

Prerequisites
-------------

.. table:: **Table 1** Checklist before migration

   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Category                          | Description                                                                                                                                                                                                                              |
   +===================================+==========================================================================================================================================================================================================================================+
   | Cluster                           | NodeIP-related: Check whether node IP addresses (including EIPs) of the cluster before the migration have been used in other configurations or trustlists.                                                                               |
   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Workload                          | Record the number of workloads for post-migration check.                                                                                                                                                                                 |
   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Storage                           | #. Check whether the storage resources in use are provisioned by the cloud or by your organization.                                                                                                                                      |
   |                                   | #. Change the automatically created storage to the existing storage in the new cluster.                                                                                                                                                  |
   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Network                           | #. Pay special attention to the LoadBalancer Services and ingresses.                                                                                                                                                                     |
   |                                   | #. Clusters of an earlier version support only the classic load balancer. To migrate services to a new cluster, change load balancer type to shared load balancer. Then, the corresponding LoadBalancer Services will be re-established. |
   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | O&M                               | Private configuration: Check whether kernel parameters or system data have been configured on nodes in the cluster.                                                                                                                      |
   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Procedure
---------

#. **Create a CCE cluster.**

   Create a cluster with the same specifications and configurations as the cluster of the earlier version. For details, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.

#. **Add nodes.**

   Add nodes with the same specifications and manual configuration items. For details, see :ref:`Creating a Node <cce_10_0363>`.

#. **Create storage volumes in the new cluster.**

   Use the existing storage to create PVCs in the new cluster. The PVC names remain unchanged. For details, see :ref:`Using an Existing OBS Bucket Through a Static PV <cce_10_0379>` or :ref:`Using an Existing SFS Turbo File System Through a Static PV <cce_10_0625>`.

   .. note::

      Storage switching supports only shared storage such as OBS and SFS Turbo. If non-shared storage is used, suspend the workloads in the old cluster to switch the storage resources. In this case, services will be unavailable.

#. **Create workloads in the new cluster.**

   Create workloads in the new cluster. The names and specifications remain unchanged. For details, see :ref:`Creating a Deployment <cce_10_0047>` or :ref:`Creating a StatefulSet <cce_10_0048>`.

#. **Mount the storage again.**

   Remount the existing storage in the workload. For details, see :ref:`Using an Existing OBS Bucket Through a Static PV <cce_10_0379>` or :ref:`Using an Existing SFS Turbo File System Through a Static PV <cce_10_0625>`.

#. **Create Services in the new cluster.**

   The Service names and specifications remain unchanged. For details about how to create a Service, see :ref:`Service <cce_10_0247>`.

#. **Commission services.**

   After all resources are created, commission the containerized services. If the commissioning is successful, migrate the services to the new cluster.

#. **Delete the old cluster.**

   When all functions of the new cluster are ready, delete the old cluster. For details about how to delete a cluster, see :ref:`Deleting a Cluster <cce_10_0212>`.
