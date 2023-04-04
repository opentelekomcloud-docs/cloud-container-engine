:original_name: cce_10_0352.html

.. _cce_10_0352:

Managing Node Taints
====================

Taints enable a node to repel specific pods to prevent these pods from being scheduled to the node.

Taints
------

A taint is a key-value pair associated with an effect. The following effects are available:

-  NoSchedule: No pod will be able to schedule onto the node unless it has a matching toleration. Existing pods will not be evicted from the node.
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

To remove a taint, run the following command with a hyphen (-) added after **NoSchedule**:

.. code-block::

   $ kubectl taint node 192.168.10.240 key1=value1:NoSchedule-
   node/192.168.10.240 untainted
   $ kubectl describe node 192.168.10.240
   Name:               192.168.10.240
   ...
   Taints:             <none>
   ...

On the CCE console, you can also manage taints of a node in batches.

#. Log in to the CCE console.

#. Click the cluster name, access the cluster details page, and choose **Nodes** in the navigation pane. On the page displayed, select a node and click **Manage Labels and Taints**.

#. In the displayed dialog box, click **Add batch operations** under **Batch Operation**, choose **Add/Update**, and select **Taint**.

   Enter the key and value of the taint to be added, select the taint effect, and click **OK**.

#. After the taint is added, check the added taint in node data.

Node Scheduling Settings
------------------------

To configure scheduling settings, log in to the CCE console, click the cluster, choose **Nodes** in the navigation pane, and click **More** > **Disable Scheduling** in the **Operation** column of a node in the node list.

In the dialog box that is displayed, click **OK** to set the node to be unschedulable.

This operation will add a taint to the node. You can use kubectl to view the content of the taint.

.. code-block::

   $ kubectl describe node 192.168.10.240
   ...
   Taints:             node.kubernetes.io/unschedulable:NoSchedule
   ...

On the CCE console, perform the same operations again to remove the taint and set the node to be schedulable.

.. _cce_10_0352__section2047442210417:

Tolerations
-----------

Tolerations are applied to pods, and allow (but do not require) the pods to schedule onto nodes with matching taints.

Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes. One or more taints are applied to a node. This marks that the node should not accept any pods that do not tolerate the taints.

Here's an example of a pod that uses tolerations:

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
