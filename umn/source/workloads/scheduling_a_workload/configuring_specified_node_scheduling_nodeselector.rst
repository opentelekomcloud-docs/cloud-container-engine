:original_name: cce_10_0891.html

.. _cce_10_0891:

Configuring Specified Node Scheduling (nodeSelector)
====================================================

In Kubernetes, to schedule a workload to a specified node, simply configure the **nodeSelector** field in the workload. By setting the target node label in this field, Kubernetes will schedule the workload only to the node with the matching label.

When a node is created, some labels are automatically added to it. You can schedule workloads to the node based on these labels. For details, see :ref:`Inherent Labels of a Node <cce_10_0004__section74111324152813>`. The typically used node labels include:

-  **topology.kubernetes.io/zone**: the AZ where the node is located, which can be used for scheduling in a specified AZ
-  **kubernetes.io/hostname**: hostname of a node, which can be used for specified node scheduling
-  **cce.cloud.com/cce-nodepool**: node pool to which a node belongs, which can be used for scheduling in a specified node pool

Prerequisites
-------------

If the default labels of a node cannot meet your requirements, you can add custom labels to the node so that workloads can be scheduled based on the labels. For details, see :ref:`Adding or Deleting a Node Label <cce_10_0004__section33951611481>`.

Creating a Workload Scheduled to a Specified Node
-------------------------------------------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **nginx.yaml**. The file name can be customized.

   Configure nodeSelector for the workload. For example, if the key is **deploy_qa** and the value is **true**, the pod will be scheduled to the node with the **deploy_qa=true** label. Example:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx
      spec:
        replicas: 3
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
          spec:
            nodeSelector:
              deploy_qa: "true"
            containers:
            - image: nginx:latest
              imagePullPolicy: IfNotPresent
              name: nginx
            imagePullSecrets:
            - name: default-secret

#. Create a workload.

   .. code-block::

      kubectl apply -f nginx.yaml

#. Verify that all pods run on the target node.

   .. code-block::

      kubectl get pod -o wide

   In the following command output, node 192.168.0.103 is labeled with **deploy_qa=true**:

   .. code-block::

      NAME                     READY   STATUS    RESTARTS   AGE     IP              NODE                                   NOMINATED NODE   READINESS GATES
      nginx-66859f4f48-xgp2g   1/1     Running   0          6h57m   172.16.3.0   192.168.0.103   <none>           <none>
      nginx-66859f4f48-t9gqj   1/1     Running   0          6h57m   172.16.3.1   192.168.0.103   <none>           <none>
      nginx-66859f4f48-2grhq   1/1     Running   0          6h57m   172.16.3.2   192.168.0.103   <none>           <none>
