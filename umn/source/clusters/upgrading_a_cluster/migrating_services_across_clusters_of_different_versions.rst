:original_name: cce_10_0210.html

.. _cce_10_0210:

Migrating Services Across Clusters of Different Versions
========================================================

Application Scenarios
---------------------

This section describes how to migrate services from a cluster of an earlier version to a cluster of a later version in CCE.

This operation is applicable when a cross-version cluster upgrade is required (for example, upgrade from v1.7.\* or v1.9.\* to 1.17.*) and new clusters can be created for service migration.

Prerequisites
-------------

.. table:: **Table 1** Checklist before migration

   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Category                          | Description                                                                                                                                                                                                                    |
   +===================================+================================================================================================================================================================================================================================+
   | Cluster                           | NodeIP-related: Check whether node IP addresses (including EIPs) of the cluster before the migration have been used in other configurations or whitelists.                                                                     |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Workloads                         | Record the number of workloads for post-migration check.                                                                                                                                                                       |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Storage                           | #. Check whether the storage resources in use are provisioned by the cloud or by your organization.                                                                                                                            |
   |                                   | #. Change the automatically created storage to the existing storage in the new cluster.                                                                                                                                        |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Network                           | #. Pay special attention to the ELB and ingress.                                                                                                                                                                               |
   |                                   | #. Clusters of an earlier version support only the classic load balancer. To migrate services to a new cluster, change load balancer type to shared load balancer. Then, the corresponding ELB service will be re-established. |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | O&M                               | Private configuration: Check whether kernel parameters or system data have been configured on nodes in the cluster.                                                                                                            |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Procedure
---------

#. **Create a CCE cluster.**

   Create a cluster with the same specifications and configurations as the cluster of the earlier version. For details, see :ref:`Creating a CCE Cluster <cce_10_0028>`.

#. **Add a node.**

   Add a node with the same specifications and manual configuration items. For details, see :ref:`Creating a Node <cce_10_0363>`.

#. **Create a storage volume in the new cluster.**

   Use the existing storage to create a PVC in the new cluster. The PVC name remains unchanged. For details, see :ref:`Using an Existing OBS Bucket Through a Static PV <cce_10_0379>` or :ref:`Using an Existing SFS Turbo File System Through a Static PV <cce_10_0625>`.

   .. note::

      Storage switching supports only OBS buckets and SFS Turbo file systems. If non-shared storage is used, suspend the workloads in the old cluster to switch the storage resources. As a result, services will be unavailable.

#. **Create a workload in the new cluster.**

   Create a workload in the new cluster. The name and specifications remain unchanged. For details, see :ref:`Creating a Deployment <cce_10_0047>` or :ref:`Creating a StatefulSet <cce_10_0048>`.

#. **Mount the storage again.**

   Remount the existing storage in the workload. For details, see :ref:`Using an Existing OBS Bucket Through a Static PV <cce_10_0379>` or :ref:`Using an Existing SFS Turbo File System Through a Static PV <cce_10_0625>`.

#. **Create a Service in the new cluster.**

   The Service name and specifications remain unchanged. For details about how to create a Service, see :ref:`Service <cce_10_0247>`.

#. **Commission services.**

   After all resources are created, commission the containerized services. If the commissioning is successful, migrate the services to the new cluster.

#. **Delete the old cluster.**

   When all functions of the new cluster are stable, delete the old cluster. For details about how to delete a cluster, see :ref:`Deleting a Cluster <cce_10_0212>`.
