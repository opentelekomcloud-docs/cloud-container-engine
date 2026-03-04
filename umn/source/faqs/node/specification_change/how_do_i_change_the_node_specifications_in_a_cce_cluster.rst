:original_name: cce_faq_00030.html

.. _cce_faq_00030:

How Do I Change the Node Specifications in a CCE Cluster?
=========================================================

Notes and Constraints
---------------------

.. caution::

   Changing node specifications is not a best practice recommended by CCE. Certain restrictions may affect subsequent usage, so review this section carefully.

   To upgrade node specifications that do not meet requirements, it is advised to add new nodes and then scale in the original nodes.

-  After a node's specifications are changed, the maximum number of pods (**maxPods** configured by kubelet) will **not be updated automatically**. Ensure that the maximum number of pods configured for the node aligns with your requirements. Otherwise, even if the node specifications are increased, additional pods may still fail to run.
-  After the specifications of a node in a node pool are modified on the ECS console, some node pool scaling issues occur. For details, see :ref:`What Are the Impacts of Changing the Flavor of a Node in a CCE Node Pool? <cce_faq_00445>`
-  CCE Turbo cluster nodes of certain specifications can be created only on the CCE console and cannot be modified on the ECS console. You can call the ECS API to modify the specifications.

Solution
--------

.. caution::

   If the node whose specifications need to be changed is accepted into the cluster for management, remove the node from the cluster and then change the node specifications to avoid affecting services.

#. Log in to the CCE console and click the cluster. In the navigation pane, choose **Nodes**. Click the name of the node to display the ECS details page.
#. In the upper right corner of the ECS details page, click **Stop**. After the ECS is stopped, choose **More** > **Modify Specifications**.
#. On the **Modify ECS Specifications** page, select a flavor name and click **Submit** to finish the specification modification. Return to ECS list page and choose **More > Start** to start the ECS.
#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Nodes**. Locate the target node in the node list, and click **Sync Server Data** in the **Operation** column. After the synchronization is complete, you can view that the node specifications are the same as the modified specifications of the ECS.

Common Issues
-------------

After the specifications of a node configured with CPU management policies are changed, the node may fail to be rebooted or workloads may fail to be created. In this case, see :ref:`What Should I Do If I Fail to Restart or Create Workloads on a Node After Modifying the Node Specifications? <cce_faq_00189>` to rectify the fault.
