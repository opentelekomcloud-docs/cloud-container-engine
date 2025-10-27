:original_name: cce_10_0212.html

.. _cce_10_0212:

Deleting a Cluster
==================

Precautions
-----------

-  Deleting a cluster will delete the workloads and Services in the cluster, and the deleted data cannot be recovered. Before performing this operation, ensure that related data has been backed up or migrated.
-  If you choose to delete a cluster with the nodes in it, the system disks and data disks attached to the nodes will also be deleted. Back up data before the deletion.
-  If you delete a cluster that is not running (for example, unavailable), associated resources, such as storage and networking resources, will remain.
-  A hibernated cluster can be deleted only after it is awakened.


Deleting a Cluster
------------------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**.

#. In the cluster list on the right, locate the cluster to be deleted and click |image1|. In the drop-down list, choose **Delete**.

#. In the displayed **Delete Cluster** dialog box, select the resources to be released.

   -  Delete cloud storage resources associated with workloads in the cluster.

      .. note::

         When deleting underlying cloud storage resources bound to storage volumes in a cluster, pay attention to following constraints:

         -  The underlying storage resources are deleted according to the reclamation policy you defined for the storage volumes. For example, if the reclamation policy of storage volumes is **Retain**, the underlying storage resources will be retained after the cluster is deleted.
         -  If there are more than 1000 files in the OBS bucket, manually clear the files and then delete the cluster.

      If the storage volume's reclamation policy is set to **Retain**, the following table lists the method of deleting PVs based on their type.

      +------------------------+--------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV Type                | Associated Underlying Storage        | Whether to Delete Underlying Storage                                                                                                                       |
      +========================+======================================+============================================================================================================================================================+
      | EVS                    | Disks                                | Yes                                                                                                                                                        |
      +------------------------+--------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | SFS                    | SFS Capacity-Oriented                | Yes                                                                                                                                                        |
      +------------------------+--------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | OBS                    | OBS buckets or parallel file systems | Yes                                                                                                                                                        |
      +------------------------+--------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | SFS Turbo              | SFS Turbo file systems               | Yes                                                                                                                                                        |
      +------------------------+--------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Local PV               | Logical volumes mounted to the node  | Determined based on the policy selected when the cluster is deleted.                                                                                       |
      |                        |                                      |                                                                                                                                                            |
      |                        |                                      | If you choose to retain the node, the logical volumes will not be deleted. If you choose to delete or reset the node, the logical volumes will be deleted. |
      +------------------------+--------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | SFS Turbo subdirectory | A directory in SFS Turbo             | No                                                                                                                                                         |
      +------------------------+--------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  Delete network resources such as load balancers in a cluster. (Only automatically created load balancers will be deleted).

   -  Delete all log groups, including their log streams, for the cluster in LTS. If you do not select this option, the LTS logs will not be deleted.

#. Enter **DELETE** and click **Yes** to start deleting the cluster.

   The delete operation takes 1 to 3 minutes to complete. If the cluster is removed from the cluster list, the cluster has been deleted.

.. |image1| image:: /_static/images/en-us_image_0000002434240380.png
