:original_name: cce_10_0004.html

.. _cce_10_0004:

Managing Node Labels
====================

Node Label Usage Scenario
-------------------------

Node labels are mainly used in the following scenarios:

-  Node management: Node labels are used to classify nodes.
-  Affinity and anti-affinity between a workload and node:

   -  Different workloads have different resource requirements such as CPU, memory, and I/O. If a workload consumes too many resources in a cluster, other workloads in the same cluster may fail to run properly. In this case, you are advised to add different labels to nodes. When deploying a workload, you can select nodes with specified labels for affinity deployment to ensure the normal operation of the system. Otherwise, node anti-affinity deployment can be used.
   -  A system can be divided into multiple modules. Each module consists of multiple microservices. To ensure efficient O&M, you can add a module label to each node so that each module can be deployed on the corresponding node. In this way, modules do not interfere with each other and microservices can be easily maintained on their nodes.

Inherent Label of a Node
------------------------

After a node is created, some fixed labels exist and cannot be deleted. For details about these labels, see :ref:`Table 1 <cce_10_0004__table83962234533>`.

.. _cce_10_0004__table83962234533:

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
   | node.kubernetes.io/container-engine                 | Container engine used by the node.                          |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | accelerator                                         | GPU node labels.                                            |
   +-----------------------------------------------------+-------------------------------------------------------------+
   | cce.cloud.com/cce-nodepool                          | The dedicated label of a node in a node pool.               |
   +-----------------------------------------------------+-------------------------------------------------------------+

Adding or Deleting a Node Label
-------------------------------

#. Log in to the CCE console.

#. Click the cluster name, access the cluster details page, and choose **Nodes** in the navigation pane. On the page displayed, select a node and click **Manage Labels and Taints**.

#. In the displayed dialog box, click **Add batch operations** under **Batch Operation**, and then choose **Add/Update** or **Delete**.

   Enter the key and value of the label to be added or deleted, and click **OK**.

   For example, the key is **deploy_qa** and the value is **true**, indicating that the node is used to deploy the QA (test) environment.

#. After the label is added, check the added label in node data.
