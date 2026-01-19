:original_name: cce_10_0556.html

.. _cce_10_0556:

System Agencies
===============

CCE works closely with multiple cloud services to support compute, storage, networking, and monitoring functions. When you log in to the CCE console for the first time, CCE automatically requests permissions to access those cloud services in the region where you run your applications. Specifically:

-  Compute services

   When you create a node in a cluster, a cloud server is created accordingly. The prerequisite is that CCE has obtained the permissions for accessing Elastic Cloud Server (ECS) and Bare Metal Server (BMS).

-  Storage services

   CCE allows you to mount storage to nodes and containers in a cluster. The prerequisite is that CCE has obtained the permissions for accessing services such as Elastic Volume Service (EVS), Scalable File Service (SFS), and Object Storage Service (OBS).

-  Networking services

   CCE allows containers in a cluster to be published as services that can be accessed by external systems. The prerequisite is that CCE has obtained the permissions for accessing services such as Virtual Private Cloud (VPC) and Elastic Load Balance (ELB).

-  Container and monitoring services

   CCE supports functions such as container image pull, monitoring, and logging. The prerequisite is that CCE has obtained the permissions for accessing services such as SoftWare Repository for Container (SWR) and Application Operations Management (AOM).

After you agree to the entrustment, CCE automatically creates an agency in IAM to delegate other resource operation permissions in your account to CCE. For details, see `Account Delegation <https://docs.otc.t-systems.com/en-us/usermanual/iam/iam_01_0054.html>`__.

The agencies automatically created by CCE are as follows:

-  :ref:`cce_admin_trust <cce_10_0556__section123269912477>` has permission to call all cloud services that CCE depends on, with the exception of IAM.

.. note::

   Based on the principle of least privilege (POLP), an IAM user must have at least the following IAM operation permissions when creating or updating an agency:

   -  **iam:agencies:createAgency**: for creating an agency
   -  **iam:permissions:revokeRoleFromAgencyOnProject**: for removing permissions of an agency for a region-specific project
   -  **iam:permissions:grantRoleToAgencyOnProject**: for granting permissions to an agency for a region-specific project
   -  **iam:roles:createRole**: for creating a custom policy
   -  **iam:roles:updateRole**: for modifying a custom policy

   For details about how to add custom permissions to cloud services, see :ref:`Custom Policies <cce_10_0188__section1437818291149>`.

.. _cce_10_0556__section123269912477:

cce_admin_trust
---------------

The **cce_admin_trust** agency has permission to call all cloud services that CCE depends on, such as creating a cluster or node. This delegation of permissions only applies to the current region.

To use CCE in multiple regions, request for cloud resource permissions in each region. You can go to the IAM console, choose **Agencies** and click **cce_admin_trust** to view the permissions of each region.

To ensure CCE can run normally, do not delete or modify the **cce_admin_trust** agency on IAM, as CCE requires the permissions.
