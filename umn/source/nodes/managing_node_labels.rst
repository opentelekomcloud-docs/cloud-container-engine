:original_name: cce_01_0004.html

.. _cce_01_0004:

Managing Node Labels
====================

Node Label Usage Scenario
-------------------------

Node labels are mainly used in the following scenarios:

-  Node management: Node labels are used to classify nodes.
-  Affinity and anti-affinity between a workload and node:

   -  Some workloads require a large CPU, some require a large memory, some require a large I/O, and other workloads may be affected. In this case, you are advised to add different labels to nodes. When deploying a workload, you can select nodes with specified labels for affinity deployment to ensure the normal operation of the system. Otherwise, node anti-affinity deployment can be used.
   -  A system can be divided into multiple modules. Each module consists of multiple microservices. To ensure the efficiency of subsequent O&M, you can add a module label to each node so that each module can be deployed on the corresponding node, does not interfere with other modules, and can be easily developed and maintained on its node.

Inherent Label of a Node
------------------------

After a node is created, some fixed labels exist and cannot be deleted. For details about these labels, see :ref:`Table 1 <cce_01_0004__en-us_topic_0000001199181148_table83962234533>`.

.. _cce_01_0004__en-us_topic_0000001199181148_table83962234533:

.. table:: **Table 1** Inherent label of a node

   +-----------------------------------------------------+-------------------------------------------------------------+
   | Key                                                 | Description                                                 |
   +=====================================================+=============================================================+
   | New: topology.kubernetes.io/region                  | Region where the node is located                            |
   |                                                     |                                                             |
   | Old: failure-domain.beta.kubernetes.io/region       |                                                             |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | New: topology.kubernetes.io/zone                    | AZ where the node is located                                |
   |                                                     |                                                             |
   | Old: failure-domain.beta.kubernetes.io/zone         |                                                             |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | New: node.kubernetes.io/baremetal                   | Whether the node is a bare metal node                       |
   |                                                     |                                                             |
   | Old: failure-domain.beta.kubernetes.io/is-baremetal | **false** indicates that the node is not a bare metal node. |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | node.kubernetes.io/instance-type                    | Node specifications                                         |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | kubernetes.io/arch                                  | Node processor architecture                                 |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | kubernetes.io/hostname                              | Node name                                                   |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | kubernetes.io/os                                    | OS type                                                     |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | node.kubernetes.io/subnetid                         | ID of the subnet where the node is located.                 |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | os.architecture                                     | Node processor architecture                                 |
   |                                                     |                                                             |
   |                                                     | For example, **amd64** indicates a AMD64-bit processor.     |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | os.name                                             | Node OS name                                                |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | os.version                                          | Node OS kernel version                                      |
   +-----------------------------------------------------+-------------------------------------------------------------+

Adding a Node Label
-------------------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Nodes**.

#. In the same row as the node for which you will add labels, choose **Operation** > **More** > **Manage Labels**.

#. In the dialog box displayed, click **Add Label** below the label list, enter the key and value of the label to be added, and click **OK**.

   As shown in the figure, the key is **deploy_qa** and the value is **true**, indicating that the node is used to deploy the QA (test) environment.

#. After the label is added, click **Manage Labels**. Then, you will see the label that you have added.

Deleting a Node Label
---------------------

Only labels added by users can be deleted. Labels that are fixed on the node cannot be deleted.

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Nodes**.

#. In the same row as the node for which you will delete labels, choose **Operation** > **More** > **Manage Labels**.

#. Click **Delete**, and then click **OK** to delete the label.

   **Label updated successfully** is displayed.

Searching for a Node by Label
-----------------------------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Nodes**.
#. In the upper right corner of the node list, click **Search by Label**.
#. Enter a Kubernetes label to find the target node.
