:original_name: cce_01_0212.html

.. _cce_01_0212:

Deleting a Cluster
==================

Scenario
--------

This section describes how to delete a cluster.

Precautions
-----------

-  Deleting a cluster will delete the nodes in the cluster (excluding accepted nodes), data disks attached to the nodes, workloads, and Services. Related services cannot be restored. Before performing this operation, ensure that data has been backed up or migrated. Deleted data cannot be restored.

   Resources that are not created in CCE will not be deleted:

   -  Accepted nodes (only the nodes created in CCE are deleted);
   -  ELB load balancers associated with Services and ingresses (only the automatically created load balancers are deleted);
   -  Manually created cloud storage resources associated with PVs or imported cloud storage resources (only the cloud storage resources automatically created by PVCs are deleted)

-  A hibernated cluster cannot be deleted. Wake up the cluster and try again.

-  If a cluster whose status is Unavailable is deleted, some storage resources of the cluster may need to be manually deleted.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Clusters**.

#. Choose **More** > **Delete**.

#. Delete the cluster.


   .. figure:: /_static/images/en-us_image_0000001190168507.png
      :alt: **Figure 1** Deleting a cluster

      **Figure 1** Deleting a cluster

#. Click **Yes** to start deleting the cluster.

   The delete operation takes 1 to 3 minutes to complete.
