:original_name: cce_10_0276.html

.. _cce_10_0276:

Performing Rolling Upgrade for Nodes
====================================

Scenario
--------

In a rolling upgrade, a new node is created, existing workloads are migrated to the new node, and then the old node is deleted. :ref:`Figure 1 <cce_10_0276__fig1689610598118>` shows the migration process.

.. _cce_10_0276__fig1689610598118:

.. figure:: /_static/images/en-us_image_0000001199181340.png
   :alt: **Figure 1** Workload migration

   **Figure 1** Workload migration

Notes and Constraints
---------------------

-  The original node and the target node to which the workload is to be migrated must be in the same cluster.
-  The cluster must be of v1.13.10 or later.
-  The default node pool DefaultPool does not support this configuration.

Scenario 1: The Original Node Is in DefaultPool
-----------------------------------------------

#. .. _cce_10_0276__li375022715214:

   Create a node pool. For details, see :ref:`Creating a Node Pool <cce_10_0012>`.

#. Click the name of the node pool. The IP address of the new node is displayed in the node list.

3. Install and configure kubectl. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

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

      During workload migration, if node affinity is configured for the workload, the workload keeps displaying a message indicating that the workload is not ready. In this case, click the workload name to go to the workload details page. On the **Scheduling Policies** tab page, delete the affinity configuration of the original node and configure the affinity and anti-affinity policies of the new node. For details, see :ref:`Scheduling Policy (Affinity/Anti-affinity) <cce_10_0232>`.

   After the workload is successfully migrated, you can view that the workload is migrated to the node created in :ref:`1 <cce_10_0276__li375022715214>` on the **Pods** tab page of the workload details page.

5. Delete the original node.

   After the workload is successfully migrated and runs properly, delete the original node.

Scenario 2: The Original Node Is Not in DefaultPool
---------------------------------------------------

#. .. _cce_10_0276__li1992616214312:

   Copy the node pool and add nodes to it. For details, see :ref:`Copying a Node Pool <cce_10_0222__section550619571556>`.

#. Click **View Node** in the **Operation** column of the node pool. The IP address of the new node is displayed in the node list.

3. Migrate the workload.

   a. Click **Edit** on the right of original node pool and set **Taints**.
   b. Enter the key and value of the taint. The options of **Effect** are **NoSchedule**, **PreferNoSchedule**, and **NoExecute**. Select **NoExecute** and click **confirm to add**.

      -  **NoSchedule**: Pods that do not tolerate this taint are not scheduled on the node; existing pods are not evicted from the node.
      -  **PreferNoSchedule**: Kubernetes tries to avoid scheduling pods that do not tolerate this taint onto the node.
      -  **NoExecute**: A pod is evicted from the node if it is already running on the node, and is not scheduled onto the node if it is not yet running on the node.

      .. note::

         If you need to reset the taint, delete the configured taint.

   c. Click **OK**.
   d. In the navigation pane of the CCE console, choose **Workloads** > **Deployments**. In the workload list, the status of the workload to be migrated changes from **Running** to **Unready**. If the workload status changes to **Running** again, the migration is successful.

   .. note::

      During workload migration, if node affinity is configured for the workload, the workload keeps displaying a message indicating that the workload is not ready. In this case, click the workload name to go to the workload details page. On the **Scheduling Policies** tab page, delete the affinity configuration of the original node and configure the affinity and anti-affinity policies of the new node. For details, see :ref:`Scheduling Policy (Affinity/Anti-affinity) <cce_10_0232>`.

   After the workload is successfully migrated, you can view that the workload is migrated to the node created in :ref:`1 <cce_10_0276__li1992616214312>` on the **Pods** tab page of the workload details page.

4. Delete the original node.

   After the workload is successfully migrated and runs properly, delete the original node.
