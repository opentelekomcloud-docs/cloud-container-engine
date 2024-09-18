:original_name: cce_faq_00260.html

.. _cce_faq_00260:

How Do I Evenly Distribute Multiple Pods to Each Node?
======================================================

The kube-scheduler component in Kubernetes is responsible pod scheduling. For each newly created pod or other unscheduled pods, kube-scheduler selects an optimal node from them to run on. kube-scheduler selects a node for a pod in a 2-step operation: filtering and scoring. In the filtering step, all nodes where it is feasible to schedule the pod are filtered out. In the scoring step, kube-scheduler ranks the remaining nodes to choose the most suitable pod placement. Finally, kube-scheduler schedules the pod to the node with the highest score. If there is more than one node with the equal scores, kube-scheduler selects one of them at random.

BalancedResourceAllocation is only one of the scoring priorities. Other scoring items may also cause uneven distribution. For details about scheduling, see `Kubernetes Scheduler <https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/>`__ and `Scheduling Policies <https://kubernetes.io/docs/reference/scheduling/policies/>`__.

You can configure pod anti-affinity policies to evenly distribute pods onto different nodes.

Example:

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
           podAntiAffinity:                   # Workload anti-affinity
             preferredDuringSchedulingIgnoredDuringExecution:    # Ensure that the following conditions are met:
               - weight: 100 # Priority that can be configured when the best-effort policy is used. The value ranges from 1 to 100. A larger value indicates a higher priority.
                 podAffinityTerm:
                   labelSelector:                       # Select the label of the pod, which is anti-affinity with the workload.
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
