:original_name: cce_faq_00394.html

.. _cce_faq_00394:

What Can I Do If a Cluster Deletion Fails Due to Residual Resources in the Security Group?
==========================================================================================

When deleting a cluster, CCE obtains the cluster's resources through kube-apiserver of the cluster. If the cluster is unavailable, frozen, or hibernated, the resources may fail to be obtained, and the cluster may not be deleted.

Symptom
-------

The cluster cannot be deleted, and the following error information is displayed:

.. code-block::

   Expected HTTP response code [200 202 204 404] when accessing [DELETE https://vpc.***.com/v2.0/security-groups/46311976-7743-4c7c-8249-ccd293bcae91], but got 409 instead {"code":"VPC.0602","message":"{\"NeutronError\":{\"message\": \"Security Group 46311976-7743-4c7c-8249-ccd293bcae91 in use.\",\"type\":\"SecurityGroupInUse\",\"detail\":\"\"}}"}

Possible Causes
---------------

The cluster's security group has undeleted resources, preventing its deletion and causing the creation of the cluster to fail.

Procedure
---------

#. Copy the resource ID in the error information, go to the **Security Groups** page of the VPC console, and obtain security groups by ID.

#. Click the security group to view its details, and click the **Associated Instances** tab.

   Obtain other resources associated with the security group, such as servers, ENIs, and sub-ENIs. You can delete residual resources. The sub ENIs will be automatically deleted.

#. For a residual ENI, go to the **Network Interfaces** page and delete the ENI obtained in the previous step.

#. Go to the **Security Groups** page to confirm that the security group is not associated with any instance. Then, go to the CCE console to delete the cluster.
