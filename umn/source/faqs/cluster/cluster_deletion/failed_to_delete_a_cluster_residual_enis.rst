:original_name: cce_faq_00394.html

.. _cce_faq_00394:

Failed to Delete a Cluster: Residual ENIs
=========================================

When deleting a cluster, CCE obtains the cluster's resources through kube-apiserver of the cluster. If the cluster is unavailable, frozen, or hibernated, the resources may fail to be obtained, and the cluster may not be deleted.

Symptom
-------

Failed to delete a cluster.

|image1|

Possible Causes
---------------

In this example, the ENI cannot be deleted because kube-apiserver of the cluster fails to obtain the ENI or sub ENI of the cluster. The security group created by CCE for the ENI or sub ENI reports the error code **409**. As a result, the cluster fails to be deleted.

Procedure
---------

#. Copy the resource ID in the error information, go to the **Security Groups** page of the VPC console, and obtain security groups by ID.

#. Click the security group to view its details, and click the **Associated Instances** tab.

   A security group remains after the deletion, because it is attached to an ENI or a sub ENI. Click the **Others** tab to view the residual ENIs. Delete the residual ENIs, and the sub ENI will be automatically deleted.

#. Choose **Network Interfaces** in the navigation pane to delete the ENIs obtained in the previous step.

   You can search for the ENIs to be deleted by ID or name.

#. Go to the **Security Groups** page, and confirm that *Cluster name*\ **-cce-eni-**\ *xxx* is not attached to any ENIs. Then, you can delete the cluster on the CCE console.

.. |image1| image:: /_static/images/en-us_image_0000001704574285.png
