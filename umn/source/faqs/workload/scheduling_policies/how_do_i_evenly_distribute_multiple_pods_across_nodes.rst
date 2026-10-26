:original_name: cce_faq_00260.html

.. _cce_faq_00260:

How Do I Evenly Distribute Multiple Pods Across Nodes?
======================================================

In Kubernetes, when multiple pods need to be evenly distributed across nodes, you can configure scheduling policies to guide the kube-scheduler's decisions. These policies can be based on approaches such as pod affinity and pod topology spread constraints.

-  :ref:`Distribute Pods Using Pod Anti-affinity <cce_faq_00260__section14173530162816>`: This solution applies to rough distribution. In essence, pods are spread across different topology domains. However, the number of pods in each domain cannot be precisely controlled, and strict even distribution is not guaranteed. For example, if resources across nodes are unbalanced and the number of pods to be scheduled exceeds the number of nodes, the scheduler may preferentially place pods on nodes with more available resources, provided that each node already has one pod running (satisfying pod anti-affinity).
-  :ref:`Controlling Pod Distribution with Topology Spread Constraints <cce_faq_00260__section109954358351>`: The pod topology spread constraints provide a stronger solution for even scheduling than affinity. They can balance the number of pods across topology domains (such as AZs, nodes, or custom domains) and accurately control either the upper limit or the allowed difference in the number of pods within each topology domain.

.. _cce_faq_00260__section14173530162816:

Distribute Pods Using Pod Anti-affinity
---------------------------------------

If you want multiple pods evenly distributed across nodes, enable workload anti-affinity to make pods mutually exclusive per node.

**Example: configuring pod anti-affinity for a Deployment**

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: my-app
   spec:
     replicas: 8
     selector:
       matchLabels:
         app: my-app
     template:
       metadata:
         labels:
           app: my-app  # Pod label, which is used for anti-affinity matching
       spec:
         affinity:
           podAntiAffinity: # Workload anti-affinity
             preferredDuringSchedulingIgnoredDuringExecution:  # Preferred (soft constraint)
             - weight: 100  # Priority that can be configured when the best-effort policy is used. The value ranges from 1 to 100. A larger value indicates a higher priority.
               podAffinityTerm:
                 labelSelector: # Select the pod label, which is anti-affinity with the workload.
                   matchExpressions:
                   - key: app
                     operator: In
                     values:
                     - my-app  # Match the same pod label.
                 topologyKey: "kubernetes.io/hostname"
         containers:
         - name: my-app
           image: nginx:alpine
         imagePullSecrets:
           - name: default-secret

**Key parameters**

-  **preferredDuringSchedulingIgnoredDuringExecution**: soft constraint. During pod scheduling, the scheduler tries to satisfy the configured conditions, but scheduling will not fail if they are not met.

-  **topologyKey**: The value is the key of a node label. Nodes that share the same label value for this label key are considered part of the same topology domain.

   **kubernetes.io/hostname**: a built-in Kubernetes node label that uniquely identifies a node. If it is used, each node has a unique label value, that is, each node forms its own topology domain, enabling per-node distribution.

**Execution result**

On the **Workloads** page, click the name of the target Deployment to access its details page. On the **Pods** tab, you can see that anti-affinity scheduling has been implemented between pods of the same application, and these pods are running on four nodes.

.. _cce_faq_00260__section109954358351:

Controlling Pod Distribution with Topology Spread Constraints
-------------------------------------------------------------

Topology spread constraints provide a stronger solution for even scheduling than affinity. They control pod distribution across topology domains (for example, AZs, nodes, or custom domains) and can enforce that the difference in pod counts between domains, such as nodes, does not exceed *N*. When combined with node affinity, pods can be evenly distributed onto specific nodes. For details, see `Pod Topology Spread Constraints <https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/>`__.

**Example: configuring topology spread constraints for a Deployment**

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: my-app
   spec:
     replicas: 6
     selector:
       matchLabels:
         app: my-app
     template:
       metadata:
         labels:
           app: my-app
       spec:
         topologySpreadConstraints:
        - maxSkew: 1  # Pod quantity difference between nodes less than or equal to 1
           topologyKey: kubernetes.io/hostname  # Distribution by node
           whenUnsatisfiable: ScheduleAnyway  # Allow scheduling to proceed when conditions cannot be met (like insufficient nodes).
           labelSelector:
             matchLabels:
               app: my-app  # Match only pods of the same application.
         affinity:
           nodeAffinity:
             requiredDuringSchedulingIgnoredDuringExecution:
               nodeSelectorTerms:
               - matchExpressions:
                 - key: role
                   operator: In
                   values:
                   - app  # Only schedule pods to the nodes with the role=app label.
         containers:
         - name: my-app
           image: nginx:alpine
         imagePullSecrets:
           - name: default-secret

**Key parameters**

-  **maxSkew**: the maximum allowed difference in the number of matching pods. For example, if there are three nodes and six pods, two pods are scheduled onto each node. If the difference is less than or equal to 1, 3, 2, and 1 pods can be distributed to each node, respectively.
-  **topologyKey**: The value is the key of a node label. Nodes that share the same label value for this label key are considered part of the same topology domain.

   -  **kubernetes.io/hostname**: a built-in Kubernetes node label that uniquely identifies a node. If it is used, each node has a unique label value, that is, each node forms its own topology domain, enabling per-node distribution.

-  **whenUnsatisfiable:**

   -  **ScheduleAnyway**: The scheduler will try to satisfy topology spread constraints but will still place pods when the constraints cannot be met (for example, when there are fewer nodes than the value specified by **replicas**). This setting sacrifices perfect balance to ensure pods start instead of blocking scheduling and causing startup failures.
   -  **DoNotSchedule**: If the topology spread constraints cannot be satisfied (for example, there are fewer nodes than the value specified by **replicas**), the scheduler will refuse to place additional pods to any nodes.

-  **labelSelector**: Only pods with the label that matches the selector are counted. Ensure the selector targets only pods from the same application so other workloads do not affect the result.

**Execution result**

On the **Workloads** page, click the name of the target Deployment to access its details page. On the **Pods** tab, you can see that the pods of the same application have been evenly scheduled.
