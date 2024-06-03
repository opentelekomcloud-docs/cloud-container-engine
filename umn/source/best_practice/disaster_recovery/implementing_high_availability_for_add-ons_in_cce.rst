:original_name: cce_bestpractice_10027.html

.. _cce_bestpractice_10027:

Implementing High Availability for Add-ons in CCE
=================================================

Application Scenarios
---------------------

CCE offers various add-ons that enhance the cloud native capabilities of clusters. These add-ons include features like container scheduling and elasticity, cloud native observability, container networking, storage, and security. Helm charts are used to deploy these add-ons. Workload pods of the add-ons are deployed on worker nodes within the clusters.

As add-ons have become more popular, their stability and reliability have become essential requirements. By default, CCE implements a policy for add-on deployment where worker nodes have a hard anti-affinity configuration, and AZs have a soft anti-affinity configuration. This section explains how to enhance the CCE add-on scheduling policy, allowing you to customize the deployment policy according to your requirements.

Viewing the Scheduling Policy of an Add-on
------------------------------------------

An add-on typically includes Deployments and daemon processes. By default, daemon processes are deployed on all nodes, while Deployments are deployed in multi-instance mode for HA scenarios.

Take the CoreDNS add-on as an example. Two pods are deployed for this add-on by default, and multi-AZ deployment is in the preferred mode. The scheduling policy is hard anti-affinity for nodes and soft anti-affinity for AZs. As a result, to ensure that all pods run smoothly, the cluster requires two nodes. The Deployment pods of the add-on are scheduled to nodes in different AZs. However, if the nodes in the cluster are not in multiple AZs, the add-on pods will be scheduled to different nodes in a single AZ.

Creating Nodes in Different AZs for a Cluster
---------------------------------------------

#. Log in to the CCE console.
#. In the navigation pane, choose **Clusters**. Click the target cluster name to access its details page.
#. In the navigation pane, choose **Nodes**, click the **Nodes** tab, and click **Create Node** in the upper right corner.
#. On the page displayed, select an AZ for the node.
#. Configure other mandatory parameters following instructions to complete the creation.

To create nodes in different AZs, you can simply repeat the previous steps. Alternatively, you can create multiple node pools, associate them with different AZ flavors, and increase the number of nodes in each pool to achieve the same result.

Configuring the AZ Anti-affinity Scheduling Policy for the Add-on
-----------------------------------------------------------------

By default, the add-on scheduling policy can handle single-node faults. However, if your services require a higher SLA, you can create nodes with different AZ specifications on the node pool page. Then, set the multi-AZ deployment mode of the add-on scheduling policy to the required mode.

This section uses the CoreDNS add-on as an example to describe how to configure the multi-AZ deployment policy for an add-on.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation tree, choose **Add-ons**. In the right pane, locate **CoreDNS** and click **Edit**.
#. In the window that slides out from the right, set **Multi AZ** to **Required** and click **Install**.
#. Choose **Workload**, click the **Deployments** tab, and view the CoreDNS pods. Select the **kube-system** namespace to view the pod distribution of the add-on.
#. View that the Deployment pods of the add-on has been allocated to nodes in two AZs.
