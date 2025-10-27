:original_name: cce_10_1079.html

.. _cce_10_1079:

Using Node Pool Settings to Bind the Default Security Group to Pods in the Node Pool
====================================================================================

In Cloud Native Network 2.0, pods can be directly bound to security groups using VPC network interfaces or supplementary network interfaces. CCE Turbo allows you to configure the default security group for pods in a node pool. This operation is applicable to the following scenarios:

-  Access control by node pool: If your services are classified by node pool, you can use the method described in this section to implement security access control by node pool. Plan the security rules for the default security group of pods in different node pools based on your services.
-  Scaling pods in a CCE Turbo cluster: A security group can be associated with a maximum of 50,000 network interfaces. If the number of pods in a single CCE Turbo cluster exceeds 50,000, you can configure different security groups for different node pools. This allows each node pool to schedule up to 50,000 pods, thereby expanding the maximum number of pods supported by a CCE Turbo cluster.

.. caution::

   To bind the default security group to pods in a node pool, classify services by node pool and configure associated scheduling policies based on your services. Otherwise, the security group may change if the workload is rescheduled to another node pool. For details about how to configure workload scheduling policies, see :ref:`Configuring Node Affinity Scheduling (nodeAffinity) <cce_10_0892>`.

Notes and Constraints
---------------------

-  Newly added security groups apply only to new pods. To apply the configuration to existing pods, they must be rebuilt.
-  Only newly bound container network interfaces on the node can be associated with a target security group.

Procedure
---------

#. Create a security group in the VPC and copy the security group ID.
#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Nodes** in the navigation pane and click the **Node Pools** tab on the right.
#. Locate the target node pool and choose **More** > **Manage**. In the **Pod Security Group** area, set **Default Pod Security Group** to the security group ID planned for this node pool.
#. Click **OK**.
