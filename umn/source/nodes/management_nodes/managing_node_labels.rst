:original_name: cce_10_0004.html

.. _cce_10_0004:

Managing Node Labels
====================

Node Label Usage Scenario
-------------------------

Node labels are mainly used in the following scenarios:

-  Node management: Node labels are used to classify nodes.
-  Node affinity or anti-affinity for workloads: By adding labels to nodes, you can schedule pods to specific nodes through node affinity or prevent pods from being scheduled to specific nodes through node anti-affinity. For details, see :ref:`Scheduling Policies (Affinity/Anti-affinity) <cce_10_0232>`.

Inherent Label of a Node
------------------------

After a node is created, some fixed labels exist and cannot be deleted. For details about these labels, see :ref:`Table 1 <cce_10_0004__table83962234533>`.

.. note::

   Do not manually change the inherent labels that are automatically added to a node. If the manually changed value conflicts with the system value, the system value is used.

.. _cce_10_0004__table83962234533:

.. table:: **Table 1** Inherent labels of a node

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
   | kubernetes.io/os                                    | Node OS type                                                |
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
   | node.kubernetes.io/container-engine                 | Container engine used by the node.                          |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | accelerator                                         | GPU node labels.                                            |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | cce.cloud.com/cce-nodepool                          | The dedicated label of a node in a node pool.               |
   +-----------------------------------------------------+-------------------------------------------------------------+

Adding or Deleting a Node Label
-------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab, select the target node and click **Labels and Taints** in the upper left corner.

#. In the displayed dialog box, click **Add batch operations** under **Batch Operation**, and then choose **Add/Update** or **Delete**.

   Enter the key and value of the label to be added or deleted, and click **OK**.

   For example, the key is **deploy_qa** and the value is **true**, indicating that the node is used to deploy the QA (test) environment.

#. After the label is added, check the added label in node data.
