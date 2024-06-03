:original_name: cce_10_0212.html

.. _cce_10_0212:

Deleting a Cluster
==================

Precautions
-----------

-  Deleting a cluster will delete the nodes in the cluster (excluding accepted nodes), data disks attached to the nodes, workloads, and Services. Related services cannot be restored. Before performing this operation, ensure that data has been backed up or migrated. Deleted data cannot be restored.

   Resources that are not created in CCE will not be deleted:

   -  Accepted nodes (only the nodes created in CCE are deleted)
   -  ELB load balancers associated with Services and ingresses (only the automatically created load balancers are deleted)
   -  Manually created cloud storage resources associated with PVs or imported cloud storage resources (only the cloud storage resources automatically created by PVCs are deleted)

-  If you delete a cluster that is not running (for example, unavailable), associated resources, such as storage and networking resources, will remain.


Deleting a Cluster
------------------

.. important::

   A hibernated cluster cannot be deleted. Wake up the cluster and try again.

#. Log in to the CCE console. In the navigation pane, choose **Clusters**.

#. Locate the cluster to be deleted, click **...** to view more operations on the cluster, and choose **Delete**.

#. In the displayed **Delete Cluster** dialog box, select the resources to be released.

   -  Delete cloud storage resources associated with workloads in the cluster.

      .. note::

         When deleting underlying cloud storage resources bound to storage volumes in a cluster, pay attention to following constraints:

         -  The underlying storage resources are deleted according to the reclamation policy you defined for the storage volumes. For example, if the reclamation policy of storage volumes is **Retain**, the underlying storage resources will be retained after the cluster is deleted.
         -  If there are more than 1000 files in the OBS bucket, manually clear the files and then delete the cluster.

   -  Delete network resources such as load balancers in a cluster. (Only automatically created load balancers will be deleted).

#. Enter **DELETE** and click **Yes** to start deleting the cluster.

   The delete operation takes 1 to 3 minutes to complete.
