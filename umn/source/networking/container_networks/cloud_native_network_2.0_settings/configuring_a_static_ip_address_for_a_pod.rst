:original_name: cce_10_0603.html

.. _cce_10_0603:

Configuring a Static IP Address for a Pod
=========================================

Application Scenarios
---------------------

In Cloud Native Network 2.0, each pod is assigned a VPC network interface with a static IP address. This is particularly applicable to StatefulSet pods. This practice is commonly used for access control, service registration, service discovery, and log auditing.

For example, if a StatefulSet needs to access a cloud database with strict access controls, you can assign static IP addresses to its pods. Then, configure the database's security group to allow access only from those specific pod IP addresses. This ensures that only the StatefulSet can access the database.

Notes and Constraints
---------------------

-  You can configure a static IP address for a pod only in CCE Turbo clusters of the following versions:

   -  v1.25: v1.25.3-r0 or later
   -  Versions later than v1.25

-  Currently, only StatefulSet pods or pods without **ownerReferences** can be configured with static IP addresses. Deployments, DaemonSets, and other types of workloads cannot be configured with static IP addresses. In addition, pods with :ref:`hostNetwork <cce_10_0402>` configured cannot be configured with static IP addresses.
-  Do not configure static IP addresses for services that do not have specific requirements on pod IP addresses. Otherwise, the pod rebuilding takes a longer time and the IP address usage decreases.
-  The annotations of the static IP address of the pod object cannot be directly modified. Otherwise, the modification does not take effect in the background. To modify the annotations, modify the **annotations** configuration in the **spec.template** field of the corresponding StatefulSet workload.
-  If there are no available network interfaces left on the node where the rebuilt pod with a static IP address is scheduled (due to pre-bound network interfaces occupying the quota), the network interfaces with static IP addresses will preempt the pre-bound network interfaces. This can cause the pod to start slightly slower. If a node uses a static IP address, properly configure the dynamic pre-binding policy for the node to ensure that not all network interfaces are pre-bound.

Using the CCE Console
---------------------

When creating a workload on the console, you can set the static IP address for a pod in the **Advanced Settings > Network Configuration** area.

-  **Whether to enable fixed IP addresses**: After the function is enabled, the pod IP address does not change each time the pod is restarted.
-  **Recycling Interval**: Retention period of related IP addresses after the pod is deleted. During this interval, the original pod IP address cannot be used by other pods.

Using kubectl
-------------

You can add annotations to a StatefulSet to enable or disable the static IP address function of the pod.

.. code-block::

   apiVersion: apps/v1
   kind: StatefulSet
   metadata:
     name: nginx
   spec:
     serviceName: nginx
     replicas: 3
     selector:
       matchLabels:
         app: nginx
     template:
       metadata:
         labels:
           app: nginx
         annotations:
           pod.alpha.kubernetes.io/initialized: 'true'
           yangtse.io/static-ip: 'true'
           yangtse.io/static-ip-expire-no-cascading: 'false'
           yangtse.io/static-ip-expire-duration: 5m
       spec:
         containers:
           - name: container-0
             image: nginx:alpine
             resources:
               limits:
                 cpu: 100m
                 memory: 200Mi
               requests:
                 cpu: 100m
                 memory: 200Mi
         imagePullSecrets:
           - name: default-secret

.. table:: **Table 1** Annotations of the pod's static IP address

   +------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | Annotation                               | Default Value   | Description                                                                                                                                                                                                                                                                                                            | Value Range                                                                                                                            |
   +==========================================+=================+========================================================================================================================================================================================================================================================================================================================+========================================================================================================================================+
   | yangtse.io/static-ip                     | false           | Specifies whether to enable the static IP address of a pod. This function is supported only for StatefulSet pods or pods without **ownerReferences**. This function is disabled by default.                                                                                                                            | **false** or **true**                                                                                                                  |
   +------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | yangtse.io/static-ip-expire-duration     | 5m              | Specifies the interval for reclaiming the expired network interface with the static IP address after the pod with a static IP address is deleted.                                                                                                                                                                      | The time format is Go time type, for example, 1h30m and 5m. For details, see `Go time type <https://pkg.go.dev/time#ParseDuration>`__. |
   +------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | yangtse.io/static-ip-expire-no-cascading | false           | Specifies whether to disable cascading reclamation of StatefulSet workloads.                                                                                                                                                                                                                                           | **false** or **true**                                                                                                                  |
   |                                          |                 |                                                                                                                                                                                                                                                                                                                        |                                                                                                                                        |
   |                                          |                 | The default value is **false**, indicating that the network interface with a static IP address will be deleted with the StatefulSet. If you want to retain the static IP address for a new StatefulSet with the same name during the interval for reclaiming the expired network interface, set the value to **true**. |                                                                                                                                        |
   +------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
