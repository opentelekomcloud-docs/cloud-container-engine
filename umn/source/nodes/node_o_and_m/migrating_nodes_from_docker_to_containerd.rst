:original_name: cce_10_0601.html

.. _cce_10_0601:

Migrating Nodes from Docker to containerd
=========================================

Context
-------

Kubernetes has removed dockershim from v1.24 and does not support Docker by default. CCE is going to stop the support for Docker. Change the node container engine from Docker to containerd.

Prerequisites
-------------

-  At least one cluster that supports containerd nodes has been created. For details, see :ref:`Mapping between Node OSs and Container Engines <cce_10_0462__section159298451879>`.
-  There is a Docker node or Docker node pool in your cluster.

Precautions
-----------

-  Theoretically, migration during container running will interrupt services for a short period of time. Therefore, it is strongly recommended that the services to be migrated have been deployed as multi-instance. In addition, you are advised to test the migration impact in the test environment to minimize potential risks.
-  containerd cannot build images. Do not use the **docker build** command to build images on containerd nodes. For other differences between Docker and containerd, see :ref:`Container Engine <cce_10_0462>`.

Migrating a Node
----------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.

#. In the node list, select one or more nodes to be reset and choose **More** > **Reset Node** in the **Operation** column.

#. Set **Container Engine** to **containerd**. You can adjust other parameters as required or retain them as set during creation.

#. If the node status is **Installing**, the node is being reset.

   When the node status is **Running**, you can see that the node version is switched to containerd. You can log in to the node and run containerd commands such as **crictl** to view information about the containers running on the node.

Migrating a Node Pool
---------------------

You can :ref:`copy a node pool <cce_10_0655>`, set the container engine of the new node pool to containerd, and keep other configurations the same as those of the original Docker node pool.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Nodes**. On the **Node Pools** tab page, locate the Docker node pool to be copied and choose **More** > **Copy** in the **Operation** column.

#. On the **Compute Settings** area, set **Container Engine** to **containerd** and modify other parameters as required.

#. Scale the number of created containerd node pools to the number of original Docker node pools and delete nodes from the Docker node pools one by one.

   Rolling migration is preferred. That is, add some containerd nodes and then delete some Docker nodes until the number of nodes in the new containerd node pool is the same as that in the original Docker node pool.

   .. note::

      If you have set node affinity for the workloads deployed on the original Docker nodes or node pool, set affinity policies for the workloads to run on the new containerd nodes or node pool.

#. After the migration, delete the original Docker node pool.
