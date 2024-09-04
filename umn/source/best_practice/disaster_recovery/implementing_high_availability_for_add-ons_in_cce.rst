:original_name: cce_bestpractice_10027.html

.. _cce_bestpractice_10027:

Implementing High Availability for Add-ons in CCE
=================================================

Application Scenarios
---------------------

CCE offers various add-ons that enhance the cloud native capabilities of clusters. These add-ons include features like container scheduling and elasticity, cloud native observability, container networking, storage, and security. Helm charts are used to deploy these add-ons. Workload pods of the add-ons are deployed on worker nodes within the clusters.

As add-ons have become more popular, their stability and reliability have become essential requirements. By default, CCE implements a policy for add-on deployment where worker nodes have a hard anti-affinity configuration, and AZs have a soft anti-affinity configuration. This section explains how to enhance the CCE add-on scheduling policy, allowing you to customize the deployment policy according to your requirements.

Deployment Solution
-------------------

An add-on typically runs as Deployments or DaemonSets. By default, DaemonSet pods are deployed on all nodes. To ensure HA of the add-on, there are multiple pods, AZ affinity policies, and specified node scheduling configured for Deployments.

Pod-level HA solution:

-  :ref:`Increasing the Number of Pods <cce_bestpractice_10027__section17857154415012>`: Multi-pod deployment can effectively prevent service unavailability caused by a single point of failure (SPOF).

Node-level HA solutions:

-  :ref:`Deploying the Add-on Pods on Dedicated Nodes <cce_bestpractice_10027__section8921175018146>`: To prevent resource preemption between service applications and core add-ons, it is best to deploy the core add-on pods on dedicated nodes. This ensures that the add-on resources are isolated and restricted on the node level.
-  :ref:`Deploying the Add-on in Multiple AZs <cce_bestpractice_10027__section85249243493>`: Multi-AZ deployment can effectively prevent service unavailability caused by the failure of a single AZ.

Take the CoreDNS add-on as an example. This add-on is deployed as two pods by default in the preferred mode, and the scheduling policies are hard anti-affinity for nodes and soft anti-affinity for AZs. In this case, two nodes are needed to ensure that all add-on pods in the cluster can run properly, and Deployment pods of the add-on can be preferentially scheduled to nodes in different AZs.

The following sections describe how to further improve the add-on SLA.

.. _cce_bestpractice_10027__section17857154415012:

Increasing the Number of Pods
-----------------------------

You can adjust the number of CoreDNS pods ensure high performance and HA.

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**, locate **CoreDNS** on the right, and click **Edit**.
#. Increase the number of pods.
#. Click **OK**.

.. _cce_bestpractice_10027__section8921175018146:

Deploying the Add-on Pods on Dedicated Nodes
--------------------------------------------

You can adjust the node affinity policy of CoreDNS and make the CoreDNS pods run on dedicated nodes. This can prevent the CoreDNS resources from being preempted by service applications.

A custom policy is used as an example.

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Nodes**.

#. Click the **Nodes** tab, select the node dedicated for CoreDNS, and click **Labels and Taints** above the node list.

   Add the following labels:

   -  Key: **node-role.kubernetes.io/coredns**
   -  Value: **true**

   Add the following taints:

   -  Key: **node-role.kubernetes.io/coredns**
   -  Value: **true**
   -  Effect: **NoSchedule**

#. In the navigation pane, choose **Add-ons**, locate **CoreDNS**, and click **Edit**.

#. Select Custom Policies for **Node Affinity** and add the preceding node label.

   Add tolerations for the preceding taint.

#. Click **OK**.

.. _cce_bestpractice_10027__section85249243493:

Deploying the Add-on in Multiple AZs
------------------------------------

By default, the add-on scheduling policy can handle single-node faults. However, if your services require a higher SLA, you can create nodes with different AZ specifications on the node pool page and set the multi-AZ deployment mode of the add-on to the required mode.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Create nodes in different AZs.

   To create nodes in different AZs, you can simply repeat these steps. Alternatively, you can create multiple node pools, associate them with different AZ specifications, and increase the number of nodes in each pool to achieve the same result.

   a. In the navigation pane, choose **Nodes**, click the **Nodes** tab, and click **Create Node** in the upper right corner.
   b. On the page displayed, select an AZ for the node.
   c. Configure other mandatory parameters following instructions to complete the creation.

#. In the navigation tree, choose **Add-ons**. In the right pane, locate **CoreDNS** and click **Edit**.

#. In the window that slides out from the right, set **Multi AZ** to **Required** and click **Install**.

#. Choose **Workload**, click the **Deployments** tab, and view the CoreDNS pods. Select the **kube-system** namespace to view the pod distribution of the add-on.

#. View that the Deployment pods of the add-on has been allocated to nodes in two AZs.
