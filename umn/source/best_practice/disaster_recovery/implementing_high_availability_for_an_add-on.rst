:original_name: cce_bestpractice_10027.html

.. _cce_bestpractice_10027:

Implementing High Availability for an Add-on
============================================

Applications
------------

CCE offers various add-ons that enhance the cloud native capabilities of clusters. These add-ons enable features like container scheduling and elasticity, cloud native observability, container networking, storage, and security. You can use Helm charts to deploy these add-ons and run their workload pods on worker nodes.

As add-ons have become more popular, their stability and reliability have become essential requirements. By default, CCE implements a hard anti-affinity rule for add-on deployment on worker nodes and a soft anti-affinity rule on AZs. This section explains how to enhance the CCE add-on scheduling policy, allowing you to customize the deployment policy according to your requirements.

Solution
--------

An add-on typically runs as a Deployment or DaemonSet. By default, DaemonSet pods are deployed on all nodes. To ensure Deployment HA, configure multiple pods, AZ affinity rules, and specified node scheduling.

Pod-level:

-  :ref:`Increasing the Number of Add-on Pods <cce_bestpractice_10027__section17857154415012>`: Deploy the add-on in multiple pods to prevent service unavailability caused by a fault.

Node-level:

-  :ref:`Deploying the Add-on Pods on a Dedicated Node <cce_bestpractice_10027__section8921175018146>`: If it is a core add-on, deploy it on a dedicated node to prevent resource preemption.
-  :ref:`Deploying the Add-on Pods in Different AZs <cce_bestpractice_10027__section85249243493>`: Deploy the add-on pods in different AZs to prevent service unavailability caused by the failure of a single AZ.

Take the CoreDNS add-on as an example. CoreDNS runs in two Deployment pods by default in the preferred mode. It is configured with hard anti-affinity rules for nodes and soft anti-affinity rules for AZs. In this case, two nodes are needed to ensure that all the CoreDNS pods in the cluster can run properly, and the add-on pods can be preferentially scheduled to nodes in different AZs.

The following describes how to further improve the add-on SLA.

.. _cce_bestpractice_10027__section17857154415012:

Increasing the Number of Add-on Pods
------------------------------------

You can adjust the number of CoreDNS pods to ensure high performance and HA.

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**, locate **CoreDNS** on the right, and click **Edit**.
#. Increase the number of pods.
#. Click **OK**.

.. _cce_bestpractice_10027__section8921175018146:

Deploying the Add-on Pods on a Dedicated Node
---------------------------------------------

You can adjust the node affinity rules of CoreDNS and make the CoreDNS pods run on a dedicated node. This can prevent the CoreDNS resources from being preempted by service applications.

A custom affinity rule is used as an example.

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Nodes**.

#. Click the **Nodes** tab, select a node dedicated for CoreDNS, and click **Manage Labels and Taints** above the node list.

   Add the following label:

   -  Key: **node-role.kubernetes.io/coredns**
   -  Value: **true**

   Add the following taint:

   -  Key: **node-role.kubernetes.io/coredns**
   -  Value: **true**
   -  Effect: **NoSchedule**

#. In the navigation pane, choose **Add-ons**, locate **CoreDNS**, and click **Edit**.

#. Select **Custom Policies** for **Node Affinity** and add the preceding node label.

   Add a toleration for the taint.

#. Click **OK**.

.. _cce_bestpractice_10027__section85249243493:

Deploying the Add-on Pods in Different AZs
------------------------------------------

The default add-on scheduling policy can tolerate a single-node failure. If your services require a higher SLA, you can create nodes in different AZs and set **Multi AZ** of the add-on to **Forcible**.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Create nodes in different AZs.

   Simply repeat these steps or create multiple node pools, associate them with different available AZs, and increase the number of nodes in each pool.

   a. In the navigation pane, choose **Nodes**, click the **Nodes** tab, and click **Create Node** in the upper right corner.
   b. On the page displayed, select an AZ for the node.
   c. Configure other mandatory parameters following instructions to complete the creation.

#. In the navigation pane, choose **Add-ons**. In the right pane, locate **CoreDNS** and click **Edit**.

#. In the window that slides out from the right, set **Multi AZ** to **Forcible** and click **Install**.

#. In the navigation pane, choose **Workloads**. In the right pane, click the **Deployments** tab, select the **kube-system** namespace, and view the distribution of the add-on pods.

#. Verify that the Deployment pods of the add-on have been allocated to two nodes in different AZs.
