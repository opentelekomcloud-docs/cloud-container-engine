:original_name: cce_faq_00413.html

.. _cce_faq_00413:

How Do I Clear Residual Resources After a Non-Running Cluster Is Deleted?
=========================================================================

If a cluster is not in the running state (for example, frozen or unavailable), resources such as PVCs, Services, and ingresses in the cluster cannot be obtained. After the cluster is deleted, residual network and storage resources may exist. In this case, manually delete these resources on their respective service console.

Deleting Residual ELB Resources
-------------------------------

#. Log in to the ELB console.
#. Search for load balancers in the VPC by VPC ID.
#. View the listener details of the load balancer. If the description contains the cluster ID and Service ID, the listener is created in the cluster.
#. Delete residual load balancer-related resources from the cluster based on the preceding information.

Deleting Residual EVS Resources
-------------------------------

An EVS disk dynamically created using a PVC is named in the format of "pvc-{uid}". The **metadata** field in the API contains the cluster ID. You can use this cluster ID to obtain these EVS disks in the cluster and delete them as required.

#. Go to the EVS console.
#. Obtain EVS disks by name ("pvc-{uid}") to obtain all automatically created EVS disks in the CCE clusters.
#. Press **F12** to open the developer tools. Check whether the **metadata** field in the **detail** interface contains the cluster ID. If yes, the EVS disk is automatically created in this cluster.
#. Delete the residual EVS resources from the cluster based on the preceding information.

   .. note::

      Deleted data cannot be restored. Exercise caution when performing this operation.

Deleting Residual SFS Resources
-------------------------------

The name format of SFS systems dynamically created using a PVC is "pvc-{uid}". The **metadata** field in the API contains the cluster ID. You can use this cluster ID to obtain these SFS systems automatically created in the cluster, and delete them as required.

#. Log in to the SFS console.
#. Search for SFS systems by name ("pvc-{uid}") to obtain all automatically created SFS systems in CCE clusters.
#. Press **F12** to open the developer tools. Check whether the **metadata** field in the **detail** interface contains the cluster ID. If yes, the SFS system is automatically created in the cluster.
#. Delete the residual SFS resources in the cluster based on the preceding information.

   .. note::

      Deleted data cannot be restored. Exercise caution when performing this operation.
