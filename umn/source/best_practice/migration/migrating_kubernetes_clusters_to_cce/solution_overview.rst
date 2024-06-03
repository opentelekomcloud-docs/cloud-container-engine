:original_name: cce_bestpractice_0307.html

.. _cce_bestpractice_0307:

Solution Overview
=================

Application Scenarios
---------------------

Containers are growing in popularity and Kubernetes simplifies containerized deployment. Many companies choose to build their own Kubernetes clusters. However, the O&M workload of on-premises clusters is heavy, and O&M personnel need to configure the management systems and monitoring solutions by themselves. This increases the labor costs while decreasing the efficiency.

In terms of performance, an on-premises cluster has poor scalability due to its fixed specifications. Auto scaling cannot be implemented in case of traffic surges, which may easily result in the insufficient or waste of cluster resources. In addition, disaster recovery risks are not considered for deploying an on-premises cluster, leading to poor reliability. Once a fault occurs, the entire cluster may fail, resulting in serious production incidents.

Now you can address the preceding challenges by using CCE, a service that allows easy cluster management and flexible scaling, integrated with application service mesh and Helm charts to simplify cluster O&M and reduce operations costs. CCE is easy to use and delivers high performance, security, reliability, openness, and compatibility. This section describes the solution and procedure for migrating on-premises clusters to CCE.

.. _cce_bestpractice_0307__section96147345128:

Migration Solution
------------------

This section describes a cluster migration solution, which applies to the following types of clusters:

-  Kubernetes clusters built in local IDCs
-  On-premises clusters built using multiple ECSs
-  Cluster services provided by other cloud service providers
-  CCE clusters that are no longer maintained and cannot be upgraded in place

Before the migration, analyze all resources in the source clusters and then determine the migration solution. Resources that can be migrated include resources inside and outside the clusters, as listed in the following table.

.. _cce_bestpractice_0307__table1126932541820:

.. table:: **Table 1** Resources that can be migrated

   +-----------------------------+----------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Category                    | Migration Object                                                                       | Remarks                                                                                                                                                                                                                                                                                                                                        |
   +=============================+========================================================================================+================================================================================================================================================================================================================================================================================================================================================+
   | Resources inside a cluster  | All objects in a cluster, including pods, jobs, Services, Deployments, and ConfigMaps. | You are not advised to migrate the resources in the **velero** and **kube-system** namespaces.                                                                                                                                                                                                                                                 |
   |                             |                                                                                        |                                                                                                                                                                                                                                                                                                                                                |
   |                             |                                                                                        | -  **velero**: Resources in this namespace are created by the migration tool and do not need to be migrated.                                                                                                                                                                                                                                   |
   |                             |                                                                                        | -  **kube-system**: Resources in this namespace are system resources. If this namespace of the source cluster contains resources created by users, migrate the resources on demand.                                                                                                                                                            |
   |                             |                                                                                        |                                                                                                                                                                                                                                                                                                                                                |
   |                             |                                                                                        | .. caution::                                                                                                                                                                                                                                                                                                                                   |
   |                             |                                                                                        |                                                                                                                                                                                                                                                                                                                                                |
   |                             |                                                                                        |    CAUTION:                                                                                                                                                                                                                                                                                                                                    |
   |                             |                                                                                        |    If you are migrating or backing up cluster resources in CCE, for example, from a namespace to another, do not back up Secret **paas.elb**. It is because secret **paas.elb** is periodically updated. After the backup is complete, the secret may become invalid when it is restored. As a result, network storage functions are affected. |
   +-----------------------------+----------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                             | PersistentVolumes (PVs) mounted to containers                                          | Due to restrictions of the Restic tool, migration is not supported for the hostPath storage volume. For details about how to solve the problem, see :ref:`Storage Volumes of the HostPath Type Cannot Be Backed Up <cce_bestpractice_0314__section11197194820367>`.                                                                            |
   +-----------------------------+----------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Resources outside a cluster | On-premises image repository                                                           | Resources can be migrated to SoftWare Repository for Container (SWR).                                                                                                                                                                                                                                                                          |
   +-----------------------------+----------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                             | Non-containerized database                                                             | Resources can be migrated to Relational Database Service (RDS).                                                                                                                                                                                                                                                                                |
   +-----------------------------+----------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                             | Non-local storage, such as object storage                                              | Resources can be migrated to Object Storage Service (OBS).                                                                                                                                                                                                                                                                                     |
   +-----------------------------+----------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

:ref:`Figure 1 <cce_bestpractice_0307__fig203631140201419>` shows the migration process. You can migrate resources outside a cluster as required.

.. _cce_bestpractice_0307__fig203631140201419:

.. figure:: /_static/images/en-us_image_0000001851744176.png
   :alt: **Figure 1** Migration solution diagram

   **Figure 1** Migration solution diagram

Migration Process
-----------------

|image1|

The cluster migration process is as follows:

#. **Plan resources for the target cluster.**

   For details about the differences between CCE clusters and on-premises clusters, see **Key Performance Parameter** in :ref:`Planning Resources for the Target Cluster <cce_bestpractice_0014>`. Plan resources as required and ensure that the performance configuration of the target cluster is the same as that of the source cluster.

#. **Migrate resources outside a cluster.**

   To migrate resources outside the cluster, see :ref:`Migrating Resources Outside a Cluster <cce_bestpractice_0059>`.

#. **Install the migration tool.**

   After resources outside a cluster are migrated, you can use a migration tool to back up and restore application configurations in the source and target clusters. For details about how to install the tool, see :ref:`Installing the Migration Tool <cce_bestpractice_0310>`.

#. **Migrate resources in the cluster.**

   Use Velero to back up resources in the source cluster to OBS and restore the resources in the target cluster. For details, see :ref:`Migrating Resources in a Cluster <cce_bestpractice_0024>`.

   -  :ref:`Backing Up Applications in the Source Cluster <cce_bestpractice_0024__section750718193288>`

      To back up resources, use the Velero tool to create a backup object in the original cluster, query and back up cluster data and resources, package the data, and upload the package to the object storage that is compatible with the S3 protocol. Cluster resources are stored in the JSON format.

   -  :ref:`Restoring Applications in the Target Cluster <cce_bestpractice_0024__section482103142819>`

      During restoration in the target cluster, Velero specifies the temporary object bucket that stores the backup data, downloads the backup data to the new cluster, and redeploys resources based on the JSON file.

#. **Update resources accordingly.**

   After the migration, cluster resources may fail to be deployed. Update the faulty resources. The possible adaptation problems are as follows:

   -  :ref:`Updating Images <cce_bestpractice_0312__section7125750134820>`
   -  :ref:`Updating Services <cce_bestpractice_0312__section41282507482>`
   -  :ref:`Updating the Storage Class <cce_bestpractice_0312__section746195321414>`
   -  :ref:`Updating Databases <cce_bestpractice_0312__section728213614323>`

#. **Perform additional tasks.**

   After cluster resources are properly deployed, verify application functions after the migration and switch service traffic to the target cluster. After confirming that all services are running properly, bring the source cluster offline.

.. |image1| image:: /_static/images/en-us_image_0000001851585464.png
