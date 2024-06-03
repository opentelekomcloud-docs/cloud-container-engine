:original_name: cce_faq_00413.html

.. _cce_faq_00413:

How Do I Clear Residual Resources After Deleting a Non-Running Cluster?
=======================================================================

If a cluster is not in the running state (for example, frozen or unavailable), its resources such as PVCs, Services, and Ingresses cannot be obtained. After the cluster is deleted, residual network and storage resources may exist. In this case, manually delete these resources on their respective service console.

Deleting Residual ELB Resources
-------------------------------

#. Log in to the ELB console.
#. Search for load balancers in the VPC by VPC ID used in the cluster.
#. View the listener details of a load balancer. If the description contains the cluster ID and Service ID, the listener is created in the cluster.
#. Delete the residual load balancer-related resources from the cluster based on the preceding information.

Deleting Residual EVS Resources
-------------------------------

An EVS disk dynamically created using a PVC is named in the format of **pvc-**\ *{UID}*. The **metadata** field in the API contains the cluster ID. You can use this cluster ID to obtain these EVS disks automatically created in the cluster and delete them as required.

#. Go to the EVS console.
#. Search for EVS disks by **pvc-**\ *{UID}* to get all automatically created EVS disks in the cluster.
#. Press **F12** to open the developer tools. Check whether the **metadata** field in the **detail** API contains the cluster ID. If yes, the EVS disks are automatically created in this cluster.
#. Delete the residual EVS disk-related resources from the cluster based on the preceding information.

   .. note::

      Deleted data cannot be restored. Exercise caution when performing this operation.

Deleting Residual SFS Resources
-------------------------------

An SFS file system dynamically created using a PVC is named in the format of **pvc-**\ *{UID}*. The **metadata** field in the API contains the cluster ID. You can use this cluster ID to obtain these SFS file systems automatically created in the cluster and delete them as required.

#. Log in to the SFS console.
#. Search for SFS file systems **pvc-**\ *{UID}* to get all automatically created SFS file systems in the cluster.
#. Press **F12** to open the developer tools. Check whether the **metadata** field in the **detail** API contains the cluster ID. If yes, the SFS file systems are automatically created in the cluster.
#. Delete the residual SFS file system-related resources from the cluster based on the preceding information.

   .. note::

      Deleted data cannot be restored. Exercise caution when performing this operation.
