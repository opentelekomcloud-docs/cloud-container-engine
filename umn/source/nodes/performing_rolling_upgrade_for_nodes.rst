:original_name: cce_01_0276.html

.. _cce_01_0276:

Performing Rolling Upgrade for Nodes
====================================

Scenario
--------

In a rolling upgrade, a new node is created, existing workloads are migrated to the new node, and then the old node is deleted. :ref:`Figure 1 <cce_01_0276__fig1689610598118>` shows the migration process.

.. _cce_01_0276__fig1689610598118:

.. figure:: /_static/images/en-us_image_0295359661.png
   :alt: **Figure 1** Workload migration

   **Figure 1** Workload migration

Notes and Constraints
---------------------

-  The original node and the target node to which the workload is to be migrated must be in the same cluster.
-  The cluster must be of v1.13.10 or later.
-  The default node pool DefaultPool does not support this configuration.

Scenario 1: The Original Node Is in DefaultPool
-----------------------------------------------

#. .. _cce_01_0276__li375022715214:

   Create a node.

   a. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Node Pools**.

   b. Select the cluster to which the original node belongs.

   c. Click **Create Node Pool**, set the following parameters, and modify other parameters as required. For details about the parameters, see :ref:`Creating a Node Pool <cce_01_0012>`.

      #. **Name**: Enter the name of the new node pool, for example, **nodepool-demo**.
      #. **Nodes**: In this example, add one node.
      #. **Specifications**: Select node specifications that best suit your needs.
      #. **OS**: Select the operating system (OS) of the nodes to be created.
      #. **Login Mode**:

         -  If the login mode is **Key pair**, select a key pair for logging in to the node and select the check box to acknowledge that you have obtained the key file and that without this file you will not be able to log in to the node.

            A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create a key pair**.

   d. Click **Next: Confirm**. Confirm the node pool configuration and click **Submit**.

      Go back to the node pool list. In the node list, you can view that the new node pool has been created and is in the Normal state.

#. Click the name of the node pool. The IP address of the new node is displayed in the node list.

3. Install and configure kubectl.

   a. In the navigation pane of the CCE console, choose **Resource Management** > **Clusters**, and click **Command Line Tool** > **Kubectl** under the cluster where the original node is located.
   b. On the **Kubectl** tab page of the cluster details page, connect to the cluster as prompted.

4. Migrate the workload.

   a. Add a taint to the node where the workload needs to be migrated out.

      **kubectl taint node** *[node]* **key=value:**\ *[effect]*

      In the preceding command, *[node]* indicates the IP address of the node where the workload to be migrated is located. The value of *[effect]* can be **NoSchedule**, **PreferNoSchedule**, or **NoExecute**. In this example, set this parameter to **NoSchedule**.

      -  **NoSchedule**: Pods that do not tolerate this taint are not scheduled on the node; existing pods are not evicted from the node.
      -  **PreferNoSchedule**: Kubernetes tries to avoid scheduling pods that do not tolerate this taint onto the node.
      -  **NoExecute**: A pod is evicted from the node if it is already running on the node, and is not scheduled onto the node if it is not yet running on the node.

      .. note::

         To reset a taint, run the **kubectl taint node [node] key:[effect]- command** to remove the taint.

   b. Safely evicts the workload on the node.

      **kubectl drain** *[node]*

      In the preceding command, *[node]* indicates the IP address of the node where the workload to be migrated is located.

   c. In the navigation pane of the CCE console, choose **Workloads** > **Deployments**. In the workload list, the status of the workload to be migrated changes from **Running** to **Unready**. If the workload status changes to **Running** again, the migration is successful.

   .. note::

      During workload migration, if node affinity is configured for the workload, the workload keeps displaying a message indicating that the workload is not ready. In this case, click the workload name to go to the workload details page. On the **Scheduling Policies** tab page, delete the affinity configuration of the original node and click **Add Simple Scheduling Policy** to configure the affinity and anti-affinity policies of the new node. For details, see :ref:`Simple Scheduling Policies <cce_01_0230>`.

   After the workload is successfully migrated, you can view that the workload is migrated to the node created in :ref:`1 <cce_01_0276__li375022715214>` on the **Pods** tab page of the workload details page.

5. Delete the original node.

   After the workload is successfully migrated and is running properly, choose **Resource Management** > **Nodes** to delete the original node.

Scenario 2: The Original Node Is Not in DefaultPool
---------------------------------------------------

#. .. _cce_01_0276__li1992616214312:

   Copy the node pool and add nodes to it.

   a. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Node Pools**.

   b. Select the cluster to which the original node belongs.

      In the node pool list, locate the node pool to which the original node belongs.

   c. Click **More** > **Copy** next to the node pool name. On the **Create Node Pool** page, set the following parameters and modify other parameters as required. For details about the parameters, see :ref:`Creating a Node Pool <cce_01_0012>`.

      -  **Name**: Enter the name of the new node pool, for example, **nodepool-demo**.
      -  **Nodes**: In this example, add one node.
      -  **Specifications**: Select node specifications that best suit your needs.
      -  **OS**: Select the operating system (OS) of the nodes to be created.
      -  **Login Mode**:

         -  If the login mode is **Key pair**, select a key pair for logging in to the node and select the check box to acknowledge that you have obtained the key file and that without this file you will not be able to log in to the node.

            A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create a key pair**.

   d. Click **Next: Confirm**. Confirm the node pool configuration and click **Submit**.

      Go back to the node pool list. In the node list, you can view that the new node pool has been created and is in the Normal state.

#. Click the name of the node pool. The IP address of the new node is displayed in the node list.

3. Migrate the workload.

   a. Click **Edit** on the right of nodepool-demo and set **Taints**.
   b. Click **Add Taint**, set **Key** and **Value**, and set **Effect** to **NoExecute**. The value options of **Effect** include **NoSchedule**, **PreferNoSchedule**, or **NoExecute**.

      -  **NoSchedule**: Pods that do not tolerate this taint are not scheduled on the node; existing pods are not evicted from the node.
      -  **PreferNoSchedule**: Kubernetes tries to avoid scheduling pods that do not tolerate this taint onto the node.
      -  **NoExecute**: A pod is evicted from the node if it is already running on the node, and is not scheduled onto the node if it is not yet running on the node.

      .. note::

         If you need to reset the taint, enter the new values or click **Delete**.

   c. Click **Save**.
   d. In the navigation pane of the CCE console, choose **Workloads** > **Deployments**. In the workload list, the status of the workload to be migrated changes from **Running** to **Unready**. If the workload status changes to **Running** again, the migration is successful.

   .. note::

      During workload migration, if node affinity is configured for the workload, the workload keeps displaying a message indicating that the workload is not ready. In this case, click the workload name to go to the workload details page. On the **Scheduling Policies** tab page, delete the affinity configuration of the original node and click **Add Simple Scheduling Policy** to configure the affinity and anti-affinity policies of the new node. For details, see :ref:`Simple Scheduling Policies <cce_01_0230>`.

   After the workload is successfully migrated, you can view that the workload is migrated to the node created in :ref:`1 <cce_01_0276__li1992616214312>` on the **Pods** tab page of the workload details page.

4. Delete the original node.

   After the workload is successfully migrated and is running properly, choose **Resource Management** > **Node Pools** to delete the original node.
