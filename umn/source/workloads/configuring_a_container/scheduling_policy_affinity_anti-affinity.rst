:original_name: cce_10_0232.html

.. _cce_10_0232:

Scheduling Policy (Affinity/Anti-affinity)
==========================================

A nodeSelector provides a very simple way to constrain pods to nodes with particular labels, as mentioned in :ref:`Creating a DaemonSet <cce_10_0216>`. The affinity and anti-affinity feature greatly expands the types of constraints you can express.

Kubernetes supports node-level and pod-level affinity and anti-affinity. You can configure custom rules to achieve affinity and anti-affinity scheduling. For example, you can deploy frontend pods and backend pods together, deploy the same type of applications on a specific node, or deploy different applications on different nodes.

Node Affinity (nodeAffinity)
----------------------------

Labels are the basis of affinity rules. Let's look at the labels on nodes in a cluster.

.. code-block::

   $ kubectl describe node 192.168.0.212
   Name:               192.168.0.212
   Roles:              <none>
   Labels:             beta.kubernetes.io/arch=amd64
                       beta.kubernetes.io/os=linux
                       failure-domain.beta.kubernetes.io/is-baremetal=false
                       failure-domain.beta.kubernetes.io/region=******
                       failure-domain.beta.kubernetes.io/zone=******
                       kubernetes.io/arch=amd64
                       kubernetes.io/availablezone=******
                       kubernetes.io/eniquota=12
                       kubernetes.io/hostname=192.168.0.212
                       kubernetes.io/os=linux
                       node.kubernetes.io/subnetid=fd43acad-33e7-48b2-a85a-24833f362e0e
                       os.architecture=amd64
                       os.name=EulerOS_2.0_SP5
                       os.version=3.10.0-862.14.1.5.h328.eulerosv2r7.x86_64

These labels are automatically added by CCE during node creation. The following describes a few that are frequently used during scheduling.

-  **failure-domain.beta.kubernetes.io/region**: region where the node is located.
-  **failure-domain.beta.kubernetes.io/zone**: availability zone to which the node belongs.
-  **kubernetes.io/hostname**: host name of the node.

When you deploy pods, you can use a nodeSelector, as described in :ref:`DaemonSet <cce_10_0216>`, to constrain pods to nodes with specific labels. The following example shows how to use a nodeSelector to deploy pods only on the nodes with the **gpu=true** label.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: nginx
   spec:
     nodeSelector:                 # Node selection. A pod is deployed on a node only when the node has the gpu=true label.
       gpu: true
   ...

Node affinity rules can achieve the same results, as shown in the following example.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name:  gpu
     labels:
       app:  gpu
   spec:
     selector:
       matchLabels:
         app: gpu
     replicas: 3
     template:
       metadata:
         labels:
           app:  gpu
       spec:
         containers:
         - image:  nginx:alpine
           name:  gpu
           resources:
             requests:
               cpu: 100m
               memory: 200Mi
             limits:
               cpu: 100m
               memory: 200Mi
         imagePullSecrets:
         - name: default-secret
         affinity:
           nodeAffinity:
             requiredDuringSchedulingIgnoredDuringExecution:
               nodeSelectorTerms:
               - matchExpressions:
                 - key: gpu
                   operator: In
                   values:
                   - "true"

Even though the node affinity rule requires more lines, it is more expressive, which will be further described later.

**requiredDuringSchedulingIgnoredDuringExecution** seems to be complex, but it can be easily understood as a combination of two parts.

-  requiredDuringScheduling indicates that pods can be scheduled to the node only when all the defined rules are met (required).
-  IgnoredDuringExecution indicates that pods already running on the node do not need to meet the defined rules. That is, a label on the node is ignored, and pods that require the node to contain that label will not be re-scheduled.

In addition, the value of **operator** is **In**, indicating that the label value must be in the values list. Other available operator values are as follows:

-  **NotIn**: The label value is not in a list.
-  **Exists**: A specific label exists.
-  **DoesNotExist**: A specific label does not exist.
-  **Gt**: The label value is greater than a specified value (string comparison).
-  **Lt**: The label value is less than a specified value (string comparison).

Note that there is no such thing as nodeAntiAffinity because operators **NotIn** and **DoesNotExist** provide the same function.

The following describes how to check whether the rule takes effect. Assume that a cluster has three nodes.

.. code-block::

   $ kubectl get node
   NAME            STATUS   ROLES    AGE   VERSION
   192.168.0.212   Ready    <none>   13m   v1.15.6-r1-20.3.0.2.B001-15.30.2
   192.168.0.94    Ready    <none>   13m   v1.15.6-r1-20.3.0.2.B001-15.30.2
   192.168.0.97    Ready    <none>   13m   v1.15.6-r1-20.3.0.2.B001-15.30.2

Add the **gpu=true** label to the **192.168.0.212** node.

.. code-block::

   $ kubectl label node 192.168.0.212 gpu=true
   node/192.168.0.212 labeled

   $ kubectl get node -L gpu
   NAME            STATUS   ROLES    AGE   VERSION                            GPU
   192.168.0.212   Ready    <none>   13m   v1.15.6-r1-20.3.0.2.B001-15.30.2   true
   192.168.0.94    Ready    <none>   13m   v1.15.6-r1-20.3.0.2.B001-15.30.2
   192.168.0.97    Ready    <none>   13m   v1.15.6-r1-20.3.0.2.B001-15.30.2

Create the Deployment. You can find that all pods are deployed on the **192.168.0.212** node.

.. code-block::

   $ kubectl create -f affinity.yaml
   deployment.apps/gpu created

   $ kubectl get pod -o wide
   NAME                     READY   STATUS    RESTARTS   AGE   IP            NODE
   gpu-6df65c44cf-42xw4     1/1     Running   0          15s   172.16.0.37   192.168.0.212
   gpu-6df65c44cf-jzjvs     1/1     Running   0          15s   172.16.0.36   192.168.0.212
   gpu-6df65c44cf-zv5cl     1/1     Running   0          15s   172.16.0.38   192.168.0.212

Node Preference Rule
--------------------

The preceding **requiredDuringSchedulingIgnoredDuringExecution** rule is a hard selection rule. There is another type of selection rule, that is, **preferredDuringSchedulingIgnoredDuringExecution**. It is used to specify which nodes are preferred during scheduling.

To achieve this effect, add a node attached with SAS disks to the cluster, add the **DISK=SAS** label to the node, and add the **DISK=SSD** label to the other three nodes.

.. code-block::

   $ kubectl get node -L DISK,gpu
   NAME            STATUS   ROLES    AGE     VERSION                            DISK     GPU
   192.168.0.100   Ready    <none>   7h23m   v1.15.6-r1-20.3.0.2.B001-15.30.2   SAS
   192.168.0.212   Ready    <none>   8h      v1.15.6-r1-20.3.0.2.B001-15.30.2   SSD      true
   192.168.0.94    Ready    <none>   8h      v1.15.6-r1-20.3.0.2.B001-15.30.2   SSD
   192.168.0.97    Ready    <none>   8h      v1.15.6-r1-20.3.0.2.B001-15.30.2   SSD

Define a Deployment. Use the **preferredDuringSchedulingIgnoredDuringExecution** rule to set the weight of nodes with the SSD disk installed as **80** and nodes with the **gpu=true** label as **20**. In this way, pods are preferentially deployed on the nodes with the SSD disk installed.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name:  gpu
     labels:
       app:  gpu
   spec:
     selector:
       matchLabels:
         app: gpu
     replicas: 10
     template:
       metadata:
         labels:
           app:  gpu
       spec:
         containers:
         - image:  nginx:alpine
           name:  gpu
           resources:
             requests:
               cpu:  100m
               memory:  200Mi
             limits:
               cpu:  100m
               memory:  200Mi
         imagePullSecrets:
         - name: default-secret
         affinity:
           nodeAffinity:
             preferredDuringSchedulingIgnoredDuringExecution:
             - weight: 80
               preference:
                 matchExpressions:
                 - key: DISK
                   operator: In
                   values:
                   - SSD
             - weight: 20
               preference:
                 matchExpressions:
                 - key: gpu
                   operator: In
                   values:
                   - "true"

After the deployment, there are five pods deployed on the node **192.168.0.212** (label: **DISK=SSD** and **GPU=true**), three pods deployed on the node **192.168.0.97** (label: **DISK=SSD**), and two pods deployed on the node **192.168.0.100** (label: **DISK=SAS**).

From the preceding output, you can find that no pods of the Deployment are scheduled to node **192.168.0.94** (label: **DISK=SSD**). This is because the node already has many pods on it and its resource usage is high. This also indicates that the **preferredDuringSchedulingIgnoredDuringExecution** rule defines a preference rather than a hard requirement.

.. code-block::

   $ kubectl create -f affinity2.yaml
   deployment.apps/gpu created

   $ kubectl get po -o wide
   NAME                   READY   STATUS    RESTARTS   AGE     IP            NODE
   gpu-585455d466-5bmcz   1/1     Running   0          2m29s   172.16.0.44   192.168.0.212
   gpu-585455d466-cg2l6   1/1     Running   0          2m29s   172.16.0.63   192.168.0.97
   gpu-585455d466-f2bt2   1/1     Running   0          2m29s   172.16.0.79   192.168.0.100
   gpu-585455d466-hdb5n   1/1     Running   0          2m29s   172.16.0.42   192.168.0.212
   gpu-585455d466-hkgvz   1/1     Running   0          2m29s   172.16.0.43   192.168.0.212
   gpu-585455d466-mngvn   1/1     Running   0          2m29s   172.16.0.48   192.168.0.97
   gpu-585455d466-s26qs   1/1     Running   0          2m29s   172.16.0.62   192.168.0.97
   gpu-585455d466-sxtzm   1/1     Running   0          2m29s   172.16.0.45   192.168.0.212
   gpu-585455d466-t56cm   1/1     Running   0          2m29s   172.16.0.64   192.168.0.100
   gpu-585455d466-t5w5x   1/1     Running   0          2m29s   172.16.0.41   192.168.0.212

In the preceding example, the node scheduling priority is as follows. Nodes with both **SSD** and **gpu=true** labels have the highest priority. Nodes with the **SSD** label but no **gpu=true** label have the second priority (weight: 80). Nodes with the **gpu=true** label but no **SSD** label have the third priority. Nodes without any of these two labels have the lowest priority.


.. figure:: /_static/images/en-us_image_0000001569022881.png
   :alt: **Figure 1** Scheduling priority

   **Figure 1** Scheduling priority

Workload Affinity (podAffinity)
-------------------------------

Node affinity rules affect only the affinity between pods and nodes. Kubernetes also supports configuring inter-pod affinity rules. For example, the frontend and backend of an application can be deployed together on one node to reduce access latency. There are also two types of inter-pod affinity rules: **requiredDuringSchedulingIgnoredDuringExecution** and **preferredDuringSchedulingIgnoredDuringExecution**.

Assume that the backend of an application has been created and has the **app=backend** label.

.. code-block::

   $ kubectl get po -o wide
   NAME                       READY   STATUS    RESTARTS   AGE     IP            NODE
   backend-658f6cb858-dlrz8   1/1     Running   0          2m36s   172.16.0.67   192.168.0.100

You can configure the following pod affinity rule to deploy the frontend pods of the application to the same node as the backend pods.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name:   frontend
     labels:
       app:  frontend
   spec:
     selector:
       matchLabels:
         app: frontend
     replicas: 3
     template:
       metadata:
         labels:
           app:  frontend
       spec:
         containers:
         - image:  nginx:alpine
           name:  frontend
           resources:
             requests:
               cpu:  100m
               memory:  200Mi
             limits:
               cpu:  100m
               memory:  200Mi
         imagePullSecrets:
         - name: default-secret
         affinity:
           podAffinity:
             requiredDuringSchedulingIgnoredDuringExecution:
             - topologyKey: kubernetes.io/hostname
               labelSelector:
                 matchExpressions:
                 - key: app
                   operator: In
                   values:
                   - backend

Deploy the frontend and you can find that the frontend is deployed on the same node as the backend.

.. code-block::

   $ kubectl create -f affinity3.yaml
   deployment.apps/frontend created

   $ kubectl get po -o wide
   NAME                        READY   STATUS    RESTARTS   AGE     IP            NODE
   backend-658f6cb858-dlrz8    1/1     Running   0          5m38s   172.16.0.67   192.168.0.100
   frontend-67ff9b7b97-dsqzn   1/1     Running   0          6s      172.16.0.70   192.168.0.100
   frontend-67ff9b7b97-hxm5t   1/1     Running   0          6s      172.16.0.71   192.168.0.100
   frontend-67ff9b7b97-z8pdb   1/1     Running   0          6s      172.16.0.72   192.168.0.100

The **topologyKey** field specifies the selection range. The scheduler selects nodes within the range based on the affinity rule defined. The effect of **topologyKey** is not fully demonstrated in the preceding example because all the nodes have the **kubernetes.io/hostname** label, that is, all the nodes are within the range.

To see how **topologyKey** works, assume that the backend of the application has two pods, which are running on different nodes.

.. code-block::

   $ kubectl get po -o wide
   NAME                       READY   STATUS    RESTARTS   AGE     IP            NODE
   backend-658f6cb858-5bpd6   1/1     Running   0          23m     172.16.0.40   192.168.0.97
   backend-658f6cb858-dlrz8   1/1     Running   0          2m36s   172.16.0.67   192.168.0.100

Add the **prefer=true** label to nodes **192.168.0.97** and **192.168.0.94**.

.. code-block::

   $ kubectl label node 192.168.0.97 prefer=true
   node/192.168.0.97 labeled
   $ kubectl label node 192.168.0.94 prefer=true
   node/192.168.0.94 labeled

   $ kubectl get node -L prefer
   NAME            STATUS   ROLES    AGE   VERSION                            PREFER
   192.168.0.100   Ready    <none>   44m   v1.15.6-r1-20.3.0.2.B001-15.30.2
   192.168.0.212   Ready    <none>   91m   v1.15.6-r1-20.3.0.2.B001-15.30.2
   192.168.0.94    Ready    <none>   91m   v1.15.6-r1-20.3.0.2.B001-15.30.2   true
   192.168.0.97    Ready    <none>   91m   v1.15.6-r1-20.3.0.2.B001-15.30.2   true

Define **topologyKey** in the **podAffinity** section as **prefer**. The node topology domains are divided as shown in :ref:`Figure 2 <cce_10_0232__fig511152614544>`.

.. code-block::

         affinity:
           podAffinity:
             requiredDuringSchedulingIgnoredDuringExecution:
             - topologyKey: prefer
               labelSelector:
                 matchExpressions:
                 - key: app
                   operator: In
                   values:
                   - backend

.. _cce_10_0232__fig511152614544:

.. figure:: /_static/images/en-us_image_0000001517903036.png
   :alt: **Figure 2** Topology domain example

   **Figure 2** Topology domain example

During scheduling, node topology domains are divided based on the **prefer** label. In this example, **192.168.0.97** and **192.168.0.94** are divided into the same topology domain. If pods with the **app=backend** label run in **192.168.0.97**, all frontend pods are deployed onto **192.168.0.97** or **192.168.0.94**.

.. code-block::

   $ kubectl create -f affinity3.yaml
   deployment.apps/frontend created

   $ kubectl get po -o wide
   NAME                        READY   STATUS    RESTARTS   AGE     IP            NODE
   backend-658f6cb858-5bpd6    1/1     Running   0          26m     172.16.0.40   192.168.0.97
   backend-658f6cb858-dlrz8    1/1     Running   0          5m38s   172.16.0.67   192.168.0.100
   frontend-67ff9b7b97-dsqzn   1/1     Running   0          6s      172.16.0.70   192.168.0.97
   frontend-67ff9b7b97-hxm5t   1/1     Running   0          6s      172.16.0.71   192.168.0.97
   frontend-67ff9b7b97-z8pdb   1/1     Running   0          6s      172.16.0.72   192.168.0.97

Workload Anti-Affinity (podAntiAffinity)
----------------------------------------

Unlike the scenarios in which pods are preferred to be scheduled onto the same node, sometimes, it could be the exact opposite. For example, if certain pods are deployed together, they will affect the performance.

In the following example, an anti-affinity rule is defined. This rule indicates that node topology domains are divided based on the **kubernetes.io/hostname** label. If a pod with the **app=frontend** label already exists on a node in the topology domain, pods with the same label cannot be scheduled to other nodes in the topology domain.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name:   frontend
     labels:
       app:  frontend
   spec:
     selector:
       matchLabels:
         app: frontend
     replicas: 5
     template:
       metadata:
         labels:
           app:  frontend
       spec:
         containers:
         - image:  nginx:alpine
           name:  frontend
           resources:
             requests:
               cpu:  100m
               memory:  200Mi
             limits:
               cpu:  100m
               memory:  200Mi
         imagePullSecrets:
         - name: default-secret
         affinity:
           podAntiAffinity:
             requiredDuringSchedulingIgnoredDuringExecution:
             - topologyKey: kubernetes.io/hostname    # Node topology domain
               labelSelector:    # Pod label matching rule
                 matchExpressions:
                 - key: app
                   operator: In
                   values:
                   - frontend

Create an anti-affinity rule and view the deployment result. In the example, node topology domains are divided by the **kubernetes.io/hostname** label. Among nodes with the **kubernetes.io/hostname** label, the label value of each node is different. Therefore, there is only one node in a topology domain. If a frontend pod already exists in a topology (a node in this example), the same pods will not be scheduled to this topology. In this example, there are only four nodes. Therefore, another pod is pending and cannot be scheduled.

.. code-block::

   $ kubectl create -f affinity4.yaml
   deployment.apps/frontend created

   $ kubectl get po -o wide
   NAME                        READY   STATUS    RESTARTS   AGE   IP            NODE
   frontend-6f686d8d87-8dlsc   1/1     Running   0          18s   172.16.0.76   192.168.0.100
   frontend-6f686d8d87-d6l8p   0/1     Pending   0          18s   <none>        <none>
   frontend-6f686d8d87-hgcq2   1/1     Running   0          18s   172.16.0.54   192.168.0.97
   frontend-6f686d8d87-q7cfq   1/1     Running   0          18s   172.16.0.47   192.168.0.212
   frontend-6f686d8d87-xl8hx   1/1     Running   0          18s   172.16.0.23   192.168.0.94

Configuring Scheduling Policies
-------------------------------

#. Log in to the CCE console.

#. When creating a workload, click **Scheduling** in the **Advanced Settings** area.

   .. table:: **Table 1** Node affinity settings

      +-----------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter | Description                                                                                                                                                                                                                                                               |
      +===========+===========================================================================================================================================================================================================================================================================+
      | Required  | This is a hard rule that must be met for scheduling. It corresponds to **requiredDuringSchedulingIgnoredDuringExecution** in Kubernetes. Multiple required rules can be set, and scheduling will be performed if only one of them is met.                                 |
      +-----------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Preferred | This is a soft rule specifying preferences that the scheduler will try to enforce but will not guarantee. It corresponds to **preferredDuringSchedulingIgnoredDuringExecution** in Kubernetes. Scheduling is performed when one rule is met or none of the rules are met. |
      +-----------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Under **Node Affinity**, **Workload Affinity**, and **Workload Anti-Affinity**, click |image1| to add scheduling policies. In the dialog box displayed, add a policy directly or by specifying a node or an AZ.

   Specifying a node or an AZ is essentially implemented through labels. The **kubernetes.io/hostname** label is used when you specify a node, and the **failure-domain.beta.kubernetes.io/zone** label is used when you specify an AZ.

   .. table:: **Table 2** Scheduling policy configuration

      +-----------------------------------+------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                |
      +===================================+============================================================================================================+
      | Label                             | Node label. You can use the default label or customize a label.                                            |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------+
      | Operator                          | The following relations are supported: **In**, **NotIn**, **Exists**, **DoesNotExist**, **Gt**, and **Lt** |
      |                                   |                                                                                                            |
      |                                   | -  **In**: A label exists in the label list.                                                               |
      |                                   | -  **NotIn**: A label does not exist in the label list.                                                    |
      |                                   | -  **Exists**: A specific label exists.                                                                    |
      |                                   | -  **DoesNotExist**: A specific label does not exist.                                                      |
      |                                   | -  **Gt**: The label value is greater than a specified value (string comparison).                          |
      |                                   | -  **Lt**: The label value is less than a specified value (string comparison).                             |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------+
      | Label Value                       | Label value.                                                                                               |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------+
      | Namespace                         | This parameter is available only in a workload affinity or anti-affinity scheduling policy.                |
      |                                   |                                                                                                            |
      |                                   | Namespace for which the scheduling policy takes effect.                                                    |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------+
      | Topology Key                      | This parameter can be used only in a workload affinity or anti-affinity scheduling policy.                 |
      |                                   |                                                                                                            |
      |                                   | Select the scope specified by **topologyKey** and then select the content defined by the policy.           |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------+
      | Weight                            | This parameter can be set only in a **Preferred** scheduling policy.                                       |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------+

.. |image1| image:: /_static/images/en-us_image_0000001518062612.png
