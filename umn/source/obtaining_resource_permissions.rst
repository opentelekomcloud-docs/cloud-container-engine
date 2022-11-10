:original_name: cce_01_9994.html

.. _cce_01_9994:

Obtaining Resource Permissions
==============================

CCE works closely with multiple cloud services to support computing, storage, networking, and monitoring functions. When you log in to the CCE console for the first time, CCE automatically requests permissions to access those cloud services in the region where you run your applications. Specifically:

-  Compute services

   When you create a node in a cluster, an ECS is created accordingly. The prerequisite is that CCE have obtained the permissions to access Elastic Cloud Service (ECS).

-  Storage services

   CCE allows you to mount storage to nodes and containers in a cluster. The prerequisite is that CCE have obtained the permissions to access services such as Elastic Volume Service (EVS), Scalable File Service (SFS), and Object Storage Service (OBS).

-  Networking services

   CCE allows containers in a cluster to be published as services that can be accessed by external systems. The prerequisite is that CCE have obtained the permissions to access services such as Virtual Private Cloud (VPC) and Elastic Load Balance (ELB).

-  Container and monitoring services

   CCE supports functions such as container image pulling, monitoring, and logging. The prerequisite is that CCE have obtained the permissions to access services such as SoftWare Repository for Container (SWR) and Application Operations Management (AOM).

After you agree to delegate the permissions, an agency named **cce_admin_trust** will be created for CCE in Identity and Access Management (IAM). The system account **op_svc_cce** will be delegated the **Tenant Administrator** role to perform operations on other cloud service resources. Tenant Administrator has the permissions on all cloud services except IAM, which calls the cloud services on which CCE depends. The delegation takes effect only in the current region. For details, see `Delegating Resource Access to Another Account <https://docs.otc.t-systems.com/en-us/usermanual/iam/iam_01_0054.html>`__.

To use CCE in multiple regions, you need to request cloud resource permissions in each region. You can go to the IAM console, choose **Agencies**, and click **cce_admin_trust** to view the delegation records of each region.

.. note::

   CCE may fail to run as expected if the Tenant Administrator role is not assigned. Therefore, do not delete or modify the **cce_admin_trust** agency when using CCE.
