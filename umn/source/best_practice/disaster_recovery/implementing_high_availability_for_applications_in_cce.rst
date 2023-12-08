:original_name: cce_bestpractice_00220.html

.. _cce_bestpractice_00220:

Implementing High Availability for Applications in CCE
======================================================

Basic Principles
----------------

To achieve high availability for your CCE containers, you can do as follows:

#. Deploy three master nodes for the cluster.
#. Create nodes in different AZs. When nodes are deployed across AZs, you can customize scheduling policies based on your requirements to maximize resource utilization.
#. Create multiple node pools in different AZs and use them for node scaling.
#. Set the number of pods to be greater than 2 when creating a workload.
#. Set pod affinity rules to distribute pods to different AZs and nodes.

Procedure
---------

Assume that there are four nodes in a cluster distributed in different AZs.

.. code-block::

   $ kubectl get node -L topology.kubernetes.io/zone,kubernetes.io/hostname
   NAME            STATUS   ROLES    AGE   VERSION                      ZONE     HOSTNAME
   192.168.5.112   Ready    <none>   42m   v1.21.7-r0-CCE21.11.1.B007   zone01   192.168.5.112
   192.168.5.179   Ready    <none>   42m   v1.21.7-r0-CCE21.11.1.B007   zone01   192.168.5.179
   192.168.5.252   Ready    <none>   37m   v1.21.7-r0-CCE21.11.1.B007   zone02   192.168.5.252
   192.168.5.8     Ready    <none>   33h   v1.21.7-r0-CCE21.11.1.B007   zone03   192.168.5.8

Create workloads according to the following podAntiAffinity rules:

-  Pod anti-affinity in an AZ. Configure the parameters as follows:

   -  **weight**: A larger weight value indicates a higher priority of scheduling. In this example, set it to **50**.
   -  **topologyKey**: includes a default or custom key for the node label that the system uses to denote a topology domain. A topology key determines the scope where the pod should be scheduled to. In this example, set this parameter to **topology.kubernetes.io/zone**, which is the label for identifying the AZ where the node is located.
   -  **labelSelector**: Select the label of the workload to realize the anti-affinity between this container and the workload.

-  The second one is the pod anti-affinity in the node hostname. Configure the parameters as follows:

   -  **weight**: Set it to **50**.
   -  **topologyKey**: Set it to **kubernetes.io/hostname**.
   -  **labelSelector**: Select the label of the pod, which is anti-affinity with the pod.

.. code-block::

   kind: Deployment
   apiVersion: apps/v1
   metadata:
     name: nginx
     namespace: default
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: nginx
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
           - name: container-0
             image: nginx:alpine
             resources:
               limits:
                 cpu: 250m
                 memory: 512Mi
               requests:
                 cpu: 250m
                 memory: 512Mi
         affinity:
           podAntiAffinity:
             preferredDuringSchedulingIgnoredDuringExecution:
               - weight: 50
                 podAffinityTerm:
                   labelSelector:                       # Select the label of the workload to realize the anti-affinity between this container and the workload.
                     matchExpressions:
                       - key: app
                         operator: In
                         values:
                           - nginx
                   namespaces:
                     - default
                   topologyKey: topology.kubernetes.io/zone   # It takes effect in the same AZ.
               - weight: 50
                 podAffinityTerm:
                   labelSelector:                       # Select the label of the workload to realize the anti-affinity between this container and the workload.
                     matchExpressions:
                       - key: app
                         operator: In
                         values:
                           - nginx
                   namespaces:
                     - default
                   topologyKey: kubernetes.io/hostname     # It takes effect on the node.
         imagePullSecrets:
           - name: default-secret

Create a workload and view the node where the pod is located.

.. code-block::

   $ kubectl get pod -owide
   NAME                     READY   STATUS    RESTARTS   AGE   IP           NODE
   nginx-6fffd8d664-dpwbk   1/1     Running   0          17s   10.0.0.132   192.168.5.112
   nginx-6fffd8d664-qhclc   1/1     Running   0          17s   10.0.1.133   192.168.5.252

Increase the number of pods to 3. The pod is scheduled to another node, and the three nodes are in three different AZs.

.. code-block::

   $ kubectl scale --replicas=3 deploy/nginx
   deployment.apps/nginx scaled
   $ kubectl get pod -owide
   NAME                     READY   STATUS    RESTARTS   AGE     IP           NODE
   nginx-6fffd8d664-8t7rv   1/1     Running   0          3s      10.0.0.9     192.168.5.8
   nginx-6fffd8d664-dpwbk   1/1     Running   0          2m45s   10.0.0.132   192.168.5.112
   nginx-6fffd8d664-qhclc   1/1     Running   0          2m45s   10.0.1.133   192.168.5.252

Increase the number of pods to 4. The pod is scheduled to the last node. With podAntiAffinity rules, pods can be evenly distributed to AZs and nodes.

.. code-block::

   $ kubectl scale --replicas=4 deploy/nginx
   deployment.apps/nginx scaled
   $ kubectl get pod -owide
   NAME                     READY   STATUS    RESTARTS   AGE     IP           NODE
   nginx-6fffd8d664-8t7rv   1/1     Running   0          2m30s   10.0.0.9     192.168.5.8
   nginx-6fffd8d664-dpwbk   1/1     Running   0          5m12s   10.0.0.132   192.168.5.112
   nginx-6fffd8d664-h796b   1/1     Running   0          78s     10.0.1.5     192.168.5.179
   nginx-6fffd8d664-qhclc   1/1     Running   0          5m12s   10.0.1.133   192.168.5.252
