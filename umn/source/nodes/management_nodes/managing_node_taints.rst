:original_name: cce_10_0352.html

.. _cce_10_0352:

Managing Node Taints
====================

Taints enable a node to repel specific pods to prevent these pods from being scheduled to the node.

Procedure for Operations Performed on the Console
-------------------------------------------------

On the CCE console, you can also batch manage nodes' taints.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab, select the target node and click **Labels and Taints** in the upper left corner.

#. In the displayed dialog box, click **Add Operation** under **Batch Operation**, and then choose **Add/Update** or **Delete** as well as **Taint**.

   Enter the key and value of the taint to be operated, choose a taint effect, and click **OK**.

#. After the taint is added, check the added taint in node data.

Procedure for Operations Performed Through kubectl Commands
-----------------------------------------------------------

A taint is a key-value pair associated with an effect. The following effects are available:

-  NoSchedule: No pod will be scheduled onto the node unless it has a matching toleration. Existing pods will not be evicted from the node.
-  PreferNoSchedule: Kubernetes prevents pods that cannot tolerate this taint from being scheduled onto the node.
-  NoExecute: If the pod has been running on a node, the pod will be evicted from the node. If the pod has not been running on a node, the pod will not be scheduled onto the node.

To add a taint to a node, run the **kubectl taint** **node** *nodename* command as follows:

.. code-block::

   $ kubectl get node
   NAME             STATUS   ROLES    AGE    VERSION
   192.168.10.170   Ready    <none>   73d    v1.19.8-r1-CCE21.4.1.B003
   192.168.10.240   Ready    <none>   4h8m   v1.19.8-r1-CCE21.6.1.2.B001
   $ kubectl taint node 192.168.10.240 key1=value1:NoSchedule
   node/192.168.10.240 tainted

To view the taint configuration, run the **describe** and **get** commands as follows:

.. code-block::

   $ kubectl describe node 192.168.10.240
   Name:               192.168.10.240
   ...
   Taints:             key1=value1:NoSchedule
   ...
   $ kubectl get node 192.168.10.240 -oyaml
   apiVersion: v1
   ...
   spec:
     providerID: 06a5ea3a-0482-11ec-8e1a-0255ac101dc2
     taints:
     - effect: NoSchedule
       key: key1
       value: value1
   ...

To remove a taint, add a hyphen (-) at the end of the command for adding a taint, as shown in the following example:

.. code-block::

   $ kubectl taint node 192.168.10.240 key1=value1:NoSchedule-
   node/192.168.10.240 untainted
   $ kubectl describe node 192.168.10.240
   Name:               192.168.10.240
   ...
   Taints:             <none>
   ...

Configuring a Node Scheduling Policy in One-Click Mode
------------------------------------------------------

You can configure a node to be unschedulable on the console. Then, CCE will add a taint with key **node.kubernetes.io/unschedulable** and the **NoSchedule** setting to the node. After a node is set to be unschedulable, new pods cannot be scheduled to this node, but pods running on the node are not affected.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.

#. In the node list, locate the target node and choose **More** > **Disable Scheduling** in the **Operation** column.

#. In the dialog box that is displayed, click **Yes** to configure the node to be unschedulable.

   This operation will add a taint to the node. You can use kubectl to view the content of the taint.

   .. code-block::

      $ kubectl describe node 192.168.10.240
      ...
      Taints:             node.kubernetes.io/unschedulable:NoSchedule
      ...

#. Go back to the node list, locate the target node, and choose **More** > **Enable Scheduling**. Then, the node changes to be schedulable.

System Taints
-------------

When some issues occurred on a node, Kubernetes automatically adds a taint to the node. The built-in taints are as follows:

-  node.kubernetes.io/not-ready: The node is not ready. The node **Ready** value is **False**.
-  node.kubernetes.io/unreachable: The node controller cannot access the node. The node **Ready** value is **Unknown**.
-  node.kubernetes.io/memory-pressure: The node memory is approaching the upper limit.
-  node.kubernetes.io/disk-pressure: The node disk space is approaching the upper limit.
-  node.kubernetes.io/pid-pressure: The node PIDs are approaching the upper limit.
-  node.kubernetes.io/network-unavailable: The node network is unavailable.
-  node.kubernetes.io/unschedulable: The node cannot be scheduled.
-  node.cloudprovider.kubernetes.io/uninitialized: If an external cloud platform driver is specified when kubelet is started, kubelet adds a taint to the current node and marks it as unavailable. After a controller of **cloud-controller-manager** initializes the node, kubelet will delete the taint.

Related Operations (Tolerations)
--------------------------------

Tolerations are applied to pods, and allow (but do not require) the pods to schedule onto nodes with matching taints.

Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes. One or more taints are applied to a node. This marks that the node should not accept any pods that do not tolerate the taints.

Example:

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: nginx
     labels:
       env: test
   spec:
     containers:
     - name: nginx
       image: nginx
       imagePullPolicy: IfNotPresent
     tolerations:
     - key: "key1"
       operator: "Equal"
       value: "value1"
       effect: "NoSchedule"

In the preceding example, the toleration label of the pod is key1=value1 and the taint effect is NoSchedule. Therefore, the pod can be scheduled onto the corresponding node.

You can also configure tolerations similar to the following information, which indicates that the pod can be scheduled onto a node when the node has the taint key1:

.. code-block::

   tolerations:
   - key: "key1"
     operator: "Exists"
     effect: "NoSchedule"
