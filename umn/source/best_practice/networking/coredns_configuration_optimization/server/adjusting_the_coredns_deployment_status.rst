:original_name: cce_bestpractice_0356.html

.. _cce_bestpractice_0356:

Adjusting the CoreDNS Deployment Status
=======================================

In CCE clusters, the CoreDNS add-on is installed by default, and it can run on the same cluster nodes as your service containers. You need to pay attention to the following points when deploying CoreDNS:

-  :ref:`Properly Changing the Number of CoreDNS Replicas <cce_bestpractice_0356__section16223162574313>`
-  :ref:`Properly Deploying the CoreDNS Pods <cce_bestpractice_0356__section145912122442>`
-  :ref:`Deploying CoreDNS Separately Using Custom Parameters <cce_bestpractice_0356__section973132695212>`
-  :ref:`Automatically Expanding the CoreDNS Capacity Based on the HPA <cce_bestpractice_0356__section9558185318454>`

.. _cce_bestpractice_0356__section16223162574313:

Properly Changing the Number of CoreDNS Replicas
------------------------------------------------

You are advised to set the number of CoreDNS replicas to at least 2 in any case and keep the number of replicas within a proper range to support the resolution of the entire cluster. The default number of pods for installing the add-on in a CCE cluster is **2**.

-  Modifying the number of CoreDNS replicas, CPUs, and memory size will change CoreDNS' parsing capability. Therefore, evaluate the impact before the operation.
-  By default, podAntiAffinity (pod anti-affinity) is configured for the add-on. If a node already has a CoreDNS pod, no new pod can be added. That is, only one CoreDNS pod can run on a node. If there are more configured CoreDNS replicas than cluster nodes, the excess pods cannot be scheduled. Therefore, keep the number of replicas less than or equal to the number of nodes.

.. _cce_bestpractice_0356__section145912122442:

Properly Deploying the CoreDNS Pods
-----------------------------------

-  By default, podAntiAffinity (pod anti-affinity) is configured for CoreDNS, so CoreDNS pods are forcibly deployed on different nodes in a cluster. It is recommended that you deploy CoreDNS pods on nodes in different AZs to prevent the add-on from being interrupted by faults in a single AZ.
-  The CPU and memory of the cluster node where the coredns add-on runs must not be used up. Otherwise, the QPS and response of domain name resolution will be affected. It is recommended that you use the custom parameters to :ref:`deploy CoreDNS separately <cce_bestpractice_0356__section973132695212>`.

.. _cce_bestpractice_0356__section973132695212:

Deploying CoreDNS Separately Using Custom Parameters
----------------------------------------------------

It is recommended that CoreDNS be deployed separately from resource-intensive workloads to prevent CoreDNS performance deterioration or unavailability due to service fluctuation. You can customize parameters to deploy CoreDNS on a dedicated node.

.. note::

   There should be more nodes than CoreDNS pods. You need to avoid deploying multiple CoreDNS pods on a single node.

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

.. _cce_bestpractice_0356__section9558185318454:

Automatically Expanding the CoreDNS Capacity Based on the HPA
-------------------------------------------------------------

HPA frequently scales down the number of the coredns add-on replicas. Therefore, you are advised not to use HPA. If HPA is required, you can configure HPA auto scaling policies using the CCE Advanced HPA add-on. The process is as follows:

#. Log in to the CCE console and click the name of the target cluster to access the cluster console. In the navigation pane, choose **Add-ons**, locate the **CCE Advanced HPA** add-on on the right, and click **Install**.

#. Configure the add-on parameters and click **Install**.

#. In the navigation pane, choose **Workloads**, select the **kube-system** namespace, locate the row containing the CoreDNS pod, and click **Auto Scaling** in the **Operation** column.

   In the **HPA Policies** area, you can customize HPA policies based on metrics such as CPU usage and memory usage to automatically scale out the CoreDNS pods.

#. Click **Create**. If the latest status is **Started**, the policy has taken effect.
