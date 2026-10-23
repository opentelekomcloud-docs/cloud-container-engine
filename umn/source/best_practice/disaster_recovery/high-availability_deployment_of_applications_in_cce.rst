:original_name: cce_bestpractice_00220.html

.. _cce_bestpractice_00220:

High-Availability Deployment of Applications in CCE
===================================================

In cloud-native environments, containerized applications are inherently elastic and agile. However, their dynamic running environments introduce uncertainties, such as single-node failures or faulty rolling updates, that can lead to service interruptions and affect critical workloads.

Achieving high availability requires more than simply scaling replica counts. A robust deployment strategy must address multiple dimensions: topology distribution, disruption budgets, health probes, and graceful lifecycle management.

-  Pod topology distribution: distributes workloads evenly across AZs and nodes to eliminate single points of failure and ensure fault tolerance.
-  Pod disruption budgets (PDBs): Configure PodDisruptionBudget resources to limit the number of pods that can be simultaneously unavailable during voluntary disruptions.
-  Pod health management: Configure readiness, liveness, and startup probes to enable Kubernetes to automatically detect and remediate unhealthy pods, ensuring traffic is routed only to viable pods.
-  Graceful lifecycle: leverages preStop hooks and termination grace periods to allow in-flight requests to complete before a pod is terminated, preventing abrupt service disruption.

This section demonstrates a practical high-availability deployment using Nginx as an example.

Examples of Building an HA Application
--------------------------------------

Pod Topology Distribution
-------------------------

Consider a cluster with four nodes distributed across three AZs.

-  **Solution 1: Using Topology Spread Constraints (Recommended)**

   Topology spread constraints (specified via **topologySpreadConstraints**), provide precise control over how pods are distributed across different topology domains such as AZs and individual nodes. This mechanism allows multiple topology dimensions to be configured simultaneously, ensuring even pod distribution without interference from other workloads in the cluster.

   Configuration example:

   When four replicas are deployed across three AZs, the scheduler attempts to distribute them as evenly as possible, typically resulting in a distribution of two pods in one zone and one pod in each of the remaining two zones. If one AZ has no available nodes due to resource constraints or failure, the scheduler may adjust the distribution to two pods in each of two zones and zero in the third. The scheduler consistently prioritizes even distribution to prevent all pods from being concentrated in a single AZ.

   .. code-block::

      ...
      spec:
        replicas: 4
        template:
          spec:
            topologySpreadConstraints:
            # Constraint 1: Even distribution across AZs
            - maxSkew: 1                     # The maximum allowed difference in the number of pods between any two AZs cannot exceed one.
              topologyKey: topology.kubernetes.io/zone
              whenUnsatisfiable: DoNotSchedule  # Hard constraint. If the scheduler cannot find a node that satisfies this distribution requirement, the pod remains in a pending state and scheduling is prohibited. Alternatively, ScheduleAnyway can be used.
              labelSelector:
                matchLabels:
                  app: nginx-ha
            # Constraint 2: Even distribution across nodes (optional, further distribution across individual nodes)
            - maxSkew: 1
              topologyKey: kubernetes.io/hostname
              whenUnsatisfiable: DoNotSchedule
              labelSelector:
                matchLabels:
                  app: nginx-ha
      ...

   Parameters:

   -  **maxSkew: 1**: defines the maximum allowed difference in pod count between any two topology domains, and it is set to **1** to achieve the most even distribution possible.
   -  **whenUnsatisfiable: DoNotSchedule**: The constraint operates as a hard requirement. If the scheduler cannot identify a node that satisfies the distribution conditions, the pod remains in a pending state until suitable resources become available.
   -  **whenUnsatisfiable: ScheduleAnyway**: The constraint operates as a soft preference. The scheduler attempts to distribute pods as evenly as possible across topology domains, but if this even distribution cannot be achieved, the scheduler proceeds with placement rather than leaving the pod pending.

-  **Solution 2: Using Pod Anti-affinity**

   Pod anti-affinity offers greater flexibility than topology spread constraints and is better suited for complex scheduling logic. The **preferredDuringScheduling** rule enables soft preferences, allowing the scheduler to preferentially distribute pods across nodes in different AZs. When stricter guarantees are necessary, the **requiredDuringScheduling** rule can be used to enforce hard constraints that prevent pods from being scheduled to the same topology domain.

   Configuration example:

   .. code-block::

      ...
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 80                      # High weight: cross-AZ deployment preferred
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                - key: app
                  operator: In
                  values:
                  - nginx-ha
              topologyKey: topology.kubernetes.io/zone
          - weight: 20                      # Low weight: cross-node scheduling within the same AZ
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                - key: app
                  operator: In
                  values:
                  - nginx-ha
              topologyKey: kubernetes.io/hostname
      ...

Pod Disruption Budgets
----------------------

A PDB is a Kubernetes resource that limits voluntary disruptions to protected workloads. Voluntary disruptions are pod deletions initiated proactively by the system or an administrator, such as during rolling updates of Deployments or manual pod deletion.

The PDB safeguards service capacity by enforcing either a **minAvailable** threshold or a **maxUnavailable** limit.

Configuration example:

.. code-block::

   apiVersion: policy/v1
   kind: PodDisruptionBudget
   metadata:
     name: nginx-ha-pdb
   spec:
     minAvailable: 2        # At least two pods must be available.
     selector:
       matchLabels:
         app: nginx-ha

-  **minAvailable**: the minimum number of pods that must remain available at any given time. This value can be expressed as an absolute number, such as **2**, or as a percentage of the total replica count, such as **50%**.
-  **maxUnavailable**: the maximum number of pods that may be simultaneously unavailable, which can also be specified as either an absolute number or a percentage.
-  **selector**: uses a label selector to match the set of pods to which the PDB applies.

.. important::

   **minAvailable** and **maxUnavailable** are mutually exclusive; only one of these two fields may be configured in a single PDB.

Pod Health Management
---------------------

A probe is a diagnostic check that the kubelet periodically performs against a container. Based on the result, the kubelet determines the health status of the pod and takes appropriate actions. Kubernetes provides three types of probes:

-  **readinessProbe**: determines whether a pod is ready to receive traffic. If this probe fails, the pod is removed from the endpoints of its associated Service, but the container itself is not restarted. This mechanism is commonly used to control the pace of rolling updates.
-  **livenessProbe**: determines whether a pod is healthy and functioning correctly. If this probe fails, the kubelet automatically restarts the container. This probe is designed to remediate application-level faults such as deadlocks or unresponsive processes.
-  **startupProbe**: determines whether the application within a container has completed its initialization. Until the startup probe succeeds, the liveness and readiness probes remain disabled. This probe is particularly suitable for applications with lengthy startup times, such as Java Spring Boot services, as it prevents premature termination by the liveness probe during the initialization phase.

Configuration example:

.. code-block::

   ...
   containers:
   - name: nginx
     image: nginx:alpine
     ports:
     - containerPort: 80
     # Readiness probe: Traffic is received only when the application is ready.
     readinessProbe:
       httpGet:
         path: /
         port: 80
       initialDelaySeconds: 5 # Delay in seconds before the probe starts
       periodSeconds: 10 # Interval in seconds for performing the probe
       failureThreshold: 3 # Number of consecutive failures before the pod is marked unavailable
     # Liveness probe: Restart the container if it is abnormal.
     livenessProbe:
       httpGet:
         path: /
         port: 80
       initialDelaySeconds: 15
       periodSeconds: 20
       failureThreshold: 3  # Increase this value to between 3 and 5 to prevent frequent container restarts caused by temporary fluctuations such as heavy load or GC pauses.
     # Startup probe: protects applications such as Java that take a long time to start.
     startupProbe:
       httpGet:
         path: /
         port: 80
       failureThreshold: 30
       periodSeconds: 10
   ...

Graceful Lifecycle
------------------

When a pod is deleted, for example during a rolling update, node draining, or scale-in, Kubernetes sends a SIGTERM signal to the containers in the pod. If the container exits immediately, requests that are being processed may be interrupted, causing the client to receive an error response such as "Connection Refused." Configure preStop hooks and a graceful termination period so that the pod can deregister from load balancers first and the container can complete all cleanup operations before exiting.

Configuration example:

.. code-block::

   ...
   containers:
   - name: nginx
     lifecycle:
       preStop:
         exec:
           command: ["/bin/sh", "-c", "sleep 30"] # Wait for 30 seconds to allow the load balancer's health checks to detect the terminating state and remove the backend.
   terminationGracePeriodSeconds: 45   # The total grace period is 45 seconds, which must exceed the preStop sleep time.
   ...

Complete YAML Example
---------------------

This example deploys four replicas of an application, integrating topology distribution constraints (recommended), a PDB, health probes, and graceful termination to withstand both single-node failures and single-AZ outages.

.. code-block::

   # 1. PodDisruptionBudget ensures that at least two pods remain available during voluntary disruptions.
   apiVersion: policy/v1
   kind: PodDisruptionBudget
   metadata:
     name: nginx-ha-pdb
   spec:
     minAvailable: 2
     selector:
       matchLabels:
         app: nginx-ha
   ---
   # 2. A Deployment integrates topology distribution constraints, health probes, and graceful termination settings.
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx-ha
   spec:
     replicas: 4
     selector:
       matchLabels:
         app: nginx-ha
     template:
       metadata:
         labels:
           app: nginx-ha
       spec:
         terminationGracePeriodSeconds: 45
         # Use topology distribution constraints to evenly spread pods across AZs and nodes.
         topologySpreadConstraints:
         - maxSkew: 1
           topologyKey: topology.kubernetes.io/zone
           whenUnsatisfiable: DoNotSchedule
           labelSelector:
             matchLabels:
               app: nginx-ha
         - maxSkew: 1
           topologyKey: kubernetes.io/hostname
           whenUnsatisfiable: DoNotSchedule
           labelSelector:
             matchLabels:
               app: nginx-ha
         containers:
         - name: nginx
           image: nginx:alpine
           ports:
           - containerPort: 80
           # Graceful lifecycle
           lifecycle:
             preStop:
               exec:
                 command: ["/bin/sh", "-c", "sleep 30"]
           # Probe settings
           readinessProbe:
             httpGet:
               path: /
               port: 80
             initialDelaySeconds: 5
             periodSeconds: 10
           livenessProbe:
             httpGet:
               path: /
               port: 80
             initialDelaySeconds: 15
             periodSeconds: 20
           resources:
             requests:
               cpu: 250m
               memory: 256Mi
             limits:
               cpu: 500m
               memory: 512Mi
         imagePullSecrets:
         - name: default-secret

Verification and Testing
------------------------

#. Verify pod distribution.

   .. code-block::

      kubectl get pod -owide -l app=nginx-ha

   Inspect the **NODE** field in the pod list output. The pods should be distributed across multiple nodes and AZs.

#. Verify the PDB.

   .. code-block::

      kubectl get pdb nginx-ha-pdb

   Example output:

   .. code-block::

      STATUS: Healthy, Allowed disruptions: 2

#. Simulate a node failure by draining a node. Confirm that the pods are rescheduled elsewhere and that the total number of simultaneously unavailable pods does not exceed the limit defined by the PDB.

#. Trigger a rolling update and confirm that the old pod waits for the duration specified by the preStop hook before terminating.

#. Manually disable the application's internal port to simulate an application fault. Confirm that the readiness probe removes the pod from service endpoints and that the liveness probe subsequently restarts the container.

FAQs
----

Do the PDB and Deployment Rolling Update Parameters maxSurge and maxUnavailable Conflict with Each Other?
---------------------------------------------------------------------------------------------------------

No. The **maxSurge** and **maxUnavailable** parameters of a Deployment operate with the PDB. Kubernetes enforces the stricter of the two limits automatically.

How Long Should the preStop Hook Be Configured?
-----------------------------------------------

The duration depends on the average and maximum time required for the service to complete processing of in-flight requests. Typically, this ranges from 15 to 30 seconds. Additionally, the **terminationGracePeriodSeconds** should be set 5 to 10 seconds longer than the preStop duration to allow sufficient time for cleanup.

Should requiredDuringScheduling or preferredDuringScheduling Be Used for Pod Anti-affinity?
-------------------------------------------------------------------------------------------

Use **requiredDuringScheduling** when the cluster has sufficient schedulable nodes and the replica count is less than or equal to the number of nodes or AZs, as this enforces strict distribution. Otherwise, use **preferredDuringScheduling** to prevent pods from remaining pending when hard constraints cannot be satisfied.

What Should the MaxSkew Value Be in Topology Distribution Constraints?
----------------------------------------------------------------------

Setting **maxSkew** to **1** achieves the strictest even distribution. When the number of replicas is not an integer multiple of the number of topology domains, this value ensures that the maximum difference in pod count between any two domains does not exceed one.
