:original_name: cce_faq_00326.html

.. _cce_faq_00326:

How Do I Evict All Pods on a Node?
==================================

You can run the **kubectl drain** command to safely evict all pods from a node.

.. note::

   By default, the **kubectl drain** command retains some system pods, for example, everest-csi-driver.

#. Use kubectl to connect to the cluster.

#. Check the nodes in the cluster.

   .. code-block::

      kubectl get node

#. Select a node and view all pods on the node.

   .. code-block::

      kubectl get pod --all-namespaces -owide --field-selector spec.nodeName=192.168.0.160

   The pods on the node before eviction are as follows:

   .. code-block::

      NAMESPACE     NAME                                      READY   STATUS    RESTARTS   AGE     IP              NODE            NOMINATED NODE   READINESS GATES
      default       nginx-5bcc57c74b-lgcvh                    1/1     Running   0          7m25s   10.0.0.140      192.168.0.160   <none>           <none>
      kube-system   coredns-6fcd88c4c-97p6s                   1/1     Running   0          3h16m   10.0.0.138      192.168.0.160   <none>           <none>
      kube-system   everest-csi-controller-56796f47cc-99dtm   1/1     Running   0          3h16m   10.0.0.139      192.168.0.160   <none>           <none>
      kube-system   everest-csi-driver-dpfzl                  2/2     Running   2          12d     192.168.0.160   192.168.0.160   <none>           <none>
      kube-system   icagent-tpfpv                             1/1     Running   1          12d     192.168.0.160   192.168.0.160   <none>           <none>

#. Evict all pods on the node.

   .. code-block::

      kubectl drain 192.168.0.160

   If a pod mounted with local storage or controlled by a DaemonSet set exists on the node, the message "error: unable to drain node "192.168.0.160", aborting command... " will be displayed. The eviction command does not take effect. You can add the following parameters to the end of the preceding command to forcibly evict the pod:

   -  **--delete-emptydir-data**: forcibly evicts pods mounted with local storage, for example, coredns.
   -  **--ignore-daemonsets**: forcibly evicts the DaemonSet pods, for example, everest-csi-driver.

   In the example, both types of pods exist on the node. Therefore, the eviction command is as follows:

   .. code-block::

      kubectl drain 192.168.0.160 --delete-emptydir-data --ignore-daemonsets

#. After the eviction, the node is automatically marked as unschedulable. That is, the node is tainted **node.kubernetes.io/unschedulable = : NoSchedule**.

   After the eviction, only system pods are retained on the node.

   .. code-block::

      NAMESPACE     NAME                       READY   STATUS    RESTARTS   AGE   IP              NODE            NOMINATED NODE   READINESS GATES
      kube-system   everest-csi-driver-dpfzl   2/2     Running   2          12d   192.168.0.160   192.168.0.160   <none>           <none>
      kube-system   icagent-tpfpv              1/1     Running   1          12d   192.168.0.160   192.168.0.160   <none>           <none>

Related Operations
------------------

Drain, cordon, and uncordon operations of kubectl:

-  **drain**: Safely evicts all pods from a node and marks the node as unschedulable.
-  **cordon**: Marks the node as unschedulable. That is, the node is tainted **node.kubernetes.io/unschedulable = : NoSchedule**.
-  **uncordon**: Marks the node as schedulable.

For more information, see the `kubectl documentation <https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#drain>`__.
