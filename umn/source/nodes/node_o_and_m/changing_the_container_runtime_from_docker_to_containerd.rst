:original_name: cce_10_0601.html

.. _cce_10_0601:

Changing the Container Runtime from Docker to containerd
========================================================

`Dockershim was removed <https://kubernetes.io/docs/tasks/administer-cluster/migrating-from-dockershim/>`__ in Kubernetes 1.24. To maintain compatibility with upstream Kubernetes, ensure continuous support, and improve cluster stability and resource efficiency, migrate the container runtime from Docker to containerd, which is the officially recommended runtime.

`containerd <https://containerd.io/>`__ is an industry-standard container runtime supported by Kubernetes. Compared with Docker, containerd occupies fewer resources, starts containers faster, and is more stable and secure.

Prerequisites
-------------

-  At least one cluster that supports containerd nodes is available. For details, see :ref:`Mapping Between Node OSs and Container Runtimes <cce_10_0462__section159298451879>`.
-  There is a Docker node or Docker node pool in your cluster.

Precautions
-----------

-  Migrating a node's container runtime rebuilds all service pods on that node, which **may cause brief service interruptions**. To minimize risk, ensure pods are deployed in HA mode, validate the migration impact in a test environment, and perform production migrations during off-peak hours.
-  **containerd does not support image building.** After migration, you cannot use Docker Build on containerd nodes. Image pull is unaffected. For other differences between Docker and containerd, see :ref:`Container Runtimes <cce_10_0462>`.
-  If you have configured node affinity policies for the workloads deployed on the original Docker nodes or node pool, configure affinity policies for the workloads to run on the new containerd nodes or node pool. In this case, you can perform a rolling upgrade to add affinity configurations for the workloads to run on the new containerd nodes or node pool without deleting the existing node affinity configurations. After the rolling upgrade is complete and there are no problems through sufficient tests, delete the affinity configurations on the old Docker nodes.

Migrating from Docker to containerd in the Default Node Pool
------------------------------------------------------------

You can reset the Docker nodes to containerd nodes.

.. caution::

   If the node resource usages are high, resetting an existing Docker node may cause pods on the node to fail to be rebuilt after migration. You can add nodes as needed to avoid this problem.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.

#. Select a Docker node in the cluster, and choose **More** > **Disable Scheduling**.

#. Choose **More** > **Drain Node**.

#. On the displayed page, enable **Forcible Drain** and click **OK**.

#. After the drainage is complete, select the node in the node list and choose **More** > **Reset Node**.

#. Set the container runtime to **containerd**. You can adjust other parameters as needed or retain them as set during creation.

#. Click **Next: Confirm**.

#. Read the instructions, select the check box for confirmation, and click **Submit**. If the node status is **Installing**, the node is being reset.

   When the node status is **Running**, check whether the node runtime has switched to containerd by logging into the node and running commands such as **crictl** **ps** or **crictl inspect** to view container information.

#. Repeat the preceding steps until Docker on all nodes is replaced with containerd.

Method 1: Performing a Rolling Update
-------------------------------------

You can :ref:`update a node pool <cce_10_0653>` to change the runtime of the node pool from Docker to containerd, retain other configurations, and then update the nodes in the node pool in rolling mode.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Nodes** in the navigation pane. In the right pane, click the **Node Pools** tab. Locate the target Docker node pool and click **Update**.

#. On the slide-out panel, set the container runtime to **containerd** and modify other parameters as needed.

#. Wait until the node pool is updated. After the update is complete, new nodes in the node pool use containerd, and "Node pool updated" is displayed for existing nodes in the node pool. You can reset these nodes to change the runtime to containerd.

   You can also choose **More** > **Synchronize** next to the node pool name and reset all nodes in the node pool in batches.

   .. caution::

      If the resource usages in a node pool are high, resetting an existing Docker node in this pool may cause pods on the node to fail to be rebuilt after migration. You can add nodes to this pool as needed to avoid this problem.

Method 2: Creating a containerd Node Pool
-----------------------------------------

You can :ref:`copy a node pool <cce_10_0655>`, set the container runtime of the new node pool to containerd, and keep other configurations the same as those of the original Docker node pool.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Nodes** in the navigation pane. On the **Node Pools** tab, locate the Docker node pool to be copied and choose **More** > **Copy**.

#. On the slide-out panel, set the container runtime to **containerd** and modify other parameters as needed to create the node pool.

#. Scale the created containerd node pool to the size of the original Docker node pool and delete nodes from the Docker node pool one by one.

   Rolling migration is preferred. Add some containerd nodes and then delete some Docker nodes until the number of nodes in the new containerd node pool is the same as that in the original Docker node pool.

   .. caution::

      If you have configured node affinity policies for the workloads deployed on the original Docker nodes or node pool, configure affinity policies for the workloads to run on the new containerd nodes or node pool. In this case, you can add affinity configurations for the workloads to run on the new containerd nodes or node pool without deleting the existing node affinity configurations. After the rolling upgrade is complete and there are no problems through sufficient tests, delete the affinity configurations on the old Docker nodes.

#. Delete the original Docker node pool.

FAQs
----

How Is Docker Compatibility Maintained After Migration to containerd?
---------------------------------------------------------------------

containerd supports the Open Container Initiative (OCI) standard. All images built with **docker build** can run in a containerd environment. Theoretically, your workloads will not be affected by the migration.

However, the following development scenarios may be affected:

-  **docker build** is required for image building, but containerd does not support image building.
-  Docker-in-Docker (DinD) or Docker-outside-of-Docker (DooD) patterns are required. For example, CI/CD pipelines that build and push images may rely on DinD or DooD. DooD mounts the host Docker socket into the container.
-  Docker APIs are required to view container status or interact with containers.

What Do I Do If I Cannot Build Images After Upgrading the Runtime from Docker to containerd?
--------------------------------------------------------------------------------------------

Install the official containerd tool `nerdctl <https://github.com/containerd/nerdctl>`__ on the node to build images. Alternatively, create an additional ECS, install Docker manually, and use it to build images.
