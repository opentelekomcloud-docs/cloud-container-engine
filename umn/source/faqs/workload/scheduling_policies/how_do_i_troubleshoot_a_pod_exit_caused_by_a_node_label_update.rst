:original_name: cce_faq_00487.html

.. _cce_faq_00487:

How Do I Troubleshoot a Pod Exit Caused by a Node Label Update?
===============================================================

Symptom
-------

After a workload pod is scheduled on a node based on the node labels, any changes to the labels or kubelet restarts due to configuration changes lead to a fault. About 30 seconds after the kubelet restart, you may notice a **Predicate NodeAffinity failed** event in the workload status. Additionally, the pod status changed to **Completed**, and a new pod will be scheduled on an appropriate node. If there are no nodes that match the specified label required by the pod, the pod scheduling will fail.

Possible Cause
--------------

Once a workload pod is configured with label affinity, it will only be scheduled to a node with that label. If the node label is changed after the pod is scheduled on the node, the pod will still run on the node. However, if kubelet is restarted, it will verify if the pod affinity policy matches the node label. If it is not, kubelet will mark the pod as **Completed**. Then, it will create a pod and schedule it.

The main issue here stems from Kubernetes' native mechanism. To maintain pod stability, you are advised to match the label of a pod with the label of the node where the pod runs. This prevents the pod from being terminated during affinity policy checks when kubelet restarts, or a new pod from encountering issues with scheduling due to mismatched affinity policies and node labels after creation.

Solution
--------

-  When you only need to add a new Kubernetes label to a node or node pool, avoid removing any existing Kubernetes labels from it. This ensures that the affinity policies remain unchanged so that the existing pods can continue running on the node.
-  If you need to change or delete a Kubernetes label on a node or node pool, but the new label does not align with the affinity policies of the pods running on the node, take the following steps to verify if there are pods with affinity scheduling configured on the node: (If no check is done, there may be a problem with pod scheduling when kubelet restarts.)

   #. Check whether an affinity policy has been configured for the workloads on a node.

      -  If the node is in the default node pool (**DefaultPool**), run the following command and replace *<node-IP-address>* with the actual one:

         .. code-block::

            nodeIP='<node-IP-address>' && kubectl get pod --all-namespaces -o=custom-columns=nodeIP:'status.hostIP',nodeAffinity:'spec.affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution.nodeSelectorTerms' | sed '1d' | grep $nodeIP | awk '{print substr($0, index($0,$2))}' | grep matchExpressions | uniq

         -  If the command output is empty, it indicates that there is no affinity policy configured for any workload on the node, so the workload pods will not be rescheduled because of a new node label.

         -  If the command output is not empty, it means there is an affinity policy configured for some workloads on the node. The following shows an example, and it indicates that there is a **tag1=value1** label configured for some workloads.

            |image1|

      -  If the node is in a custom node pool, run the following command and replace *<node-pool-name>* with the actual one:

         .. code-block::

            nodeIPs=$(kubectl get nodes -l cce.cloud.com/cce-nodepool=<node-pool-name> -o=custom-columns=IP:'metadata.annotations.alpha\.kubernetes\.io\/provided-node-ip' | sed '1d' | tr '\n' '|' | sed 's/|$//') && kubectl get pod --all-namespaces -o=custom-columns=nodeIP:'status.hostIP',nodeAffinity:'spec.affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution.nodeSelectorTerms' | sed '1d' | grep -E $nodeIPs | awk '{print substr($0, index($0,$2))}' | grep matchExpressions | uniq

         -  If the command output is empty, it indicates that there is no affinity policy configured for any workload in the node pool, so the workload pods will not be rescheduled because of a new node label.

         -  If the command output is not empty, it means there is an affinity policy configured for some workloads in the node pool. The following shows an example, and it indicates that there is a **tag1=value1** label configured for some workloads.

            |image2|

   #. Get all workloads that are associated with the affinity policy. The value of *affinity-policy* is the one obtained in the previous step.

      .. code-block::

         match='affinity-policy' && kubectl get deployment,statefulset,daemonset,job,cronjob --all-namespaces -o=custom-columns=kind:'kind',name:'metadata.namespace',namespace:'metadata.name',nodeAffinity:'spec.template.spec.affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution.nodeSelectorTerms' | grep -F "$match" | awk '{print "kind:" $1 ",namespace:" $2 ",name:" $3}'

      As shown in the following figure, the workloads with the **tag1=value1** label are displayed.

      |image3|

   #. Evaluate the impact of the workload restart on services before changing any label on the node or node pool.

      You can also modify the affinity policy of a workload to schedule it to other nodes and change the node label after the workload pod runs properly.

.. |image1| image:: /_static/images/en-us_image_0000002516078175.png
.. |image2| image:: /_static/images/en-us_image_0000002483958226.png
.. |image3| image:: /_static/images/en-us_image_0000002483958234.png
