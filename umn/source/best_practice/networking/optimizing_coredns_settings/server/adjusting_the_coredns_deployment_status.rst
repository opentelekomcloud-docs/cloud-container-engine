:original_name: cce_bestpractice_0356.html

.. _cce_bestpractice_0356:

Adjusting the CoreDNS Deployment Status
=======================================

In CCE clusters, the CoreDNS add-on is installed by default, and it can run on the same cluster nodes as your service containers. You need to pay attention to the following points when deploying CoreDNS:

-  :ref:`Properly Changing the Number of CoreDNS Pods <cce_bestpractice_0356__section16223162574313>`
-  :ref:`Properly Deploying the CoreDNS Pods <cce_bestpractice_0356__section145912122442>`
-  :ref:`Isolating CoreDNS Deployment Using Custom Parameters <cce_bestpractice_0356__section973132695212>`
-  :ref:`Automatically Expanding the CoreDNS Capacity Based on an HPA <cce_bestpractice_0356__section9558185318454>`

.. _cce_bestpractice_0356__section16223162574313:

Properly Changing the Number of CoreDNS Pods
--------------------------------------------

You are advised to set the number of CoreDNS pods to at least 2 in any case and keep the number of pods within a proper range to support the resolution within the entire cluster. The default number of pods for installing the add-on in a CCE cluster is **2**.

-  The specifications of resources used by CoreDNS are related to the resolution capability. Modifying the number of CoreDNS pods, CPUs, and memory size will change CoreDNS' resolution capability. Therefore, evaluate the impact before the operation.
-  By default, **podAntiAffinity** (pod anti-affinity) is configured for the add-on. If a node already has a CoreDNS pod, no new pod can be added to it. This means that only one CoreDNS pod can run on a node. If there are more CoreDNS pods than the number of nodes, the excess pods cannot be scheduled to any node. Therefore, keep the number of CoreDNS pods less than or equal to the number of nodes.

.. _cce_bestpractice_0356__section145912122442:

Properly Deploying the CoreDNS Pods
-----------------------------------

-  By default, CoreDNS is configured with **podAntiAffinity**, ensuring that its pods are scheduled onto different nodes. It is advised to deploy CoreDNS pods on nodes in different AZs to avoid service impact caused by a single-AZ failure.
-  Nodes running CoreDNS should avoid CPU or memory saturation, as resource exhaustion can degrade domain name resolution QPS and increase query latency. It is advised to use the custom add-on parameters to :ref:`deploy the CoreDNS pods separately <cce_bestpractice_0356__section973132695212>`.

.. _cce_bestpractice_0356__section973132695212:

Isolating CoreDNS Deployment Using Custom Parameters
----------------------------------------------------

It is advised to isolate CoreDNS from resource-intensive workloads to prevent performance deterioration or unavailability due to service fluctuations. You can use custom parameters to deploy CoreDNS on dedicated nodes.

.. note::

   Ensure that the number of nodes exceeds the number of the CoreDNS pods to avoid scheduling multiple CoreDNS pods onto the same node.

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

.. _cce_bestpractice_0356__section9558185318454:

Automatically Expanding the CoreDNS Capacity Based on an HPA
------------------------------------------------------------

HPAs frequently scale in the number of the CoreDNS add-on pods. Therefore, you are advised not to use HPAs. If an HPA is required, you can configure HPA auto scaling policies using the CCE Advanced HPA add-on. The process is as follows:

#. Log in to the CCE console and click the name of the cluster to access the cluster console. In the navigation pane, choose **Add-ons**, locate the **CCE Advanced HPA** add-on on the right, and click **Install**.

#. Configure the add-on parameters and click **Install**.

#. In the navigation pane, choose **Workloads**, select the **kube-system** namespace, locate the row containing the CoreDNS workload, and choose **More** > **Auto Scaling** in the **Operation** column.

   In the **HPA Policies** area, you can customize HPA policies based on metrics such as CPU usage and memory usage to automatically scale out the CoreDNS pods.

#. Click **Create**. If the latest status is **Started**, the policy has taken effect.
