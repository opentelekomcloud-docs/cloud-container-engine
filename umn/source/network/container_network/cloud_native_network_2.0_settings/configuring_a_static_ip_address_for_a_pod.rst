:original_name: cce_10_0603.html

.. _cce_10_0603:

Configuring a Static IP Address for a Pod
=========================================

Application Scenarios
---------------------

In Cloud Native Network 2.0, each pod is associated with an ENI, providing a static IP address to the StatefulSet pods (container ENI). This is a common practice in access control, service registration, service discovery, and log audit of static IP addresses.

For example, if a StatefulSet service needs to control the access of a cloud database, you can fix the pod IP address of the service and configure the security group of the cloud database to allow only the service IP address to access the database.

Notes and Constraints
---------------------

-  You can configure a static IP address for a pod only in CCE Turbo clusters of the following versions:

   -  v1.25: v1.25.3-r0 or later
   -  v1.25 or later

-  Currently, only StatefulSet pods or pods without **ownerReferences** can be configured with static IP addresses. Deployments, DaemonSets, and other types of workloads cannot be configured with static IP addresses. In addition, pods with :ref:`HostNetwork <cce_10_0402>` configured cannot be configured with static IP addresses.
-  Do not configure static IP addresses for services that do not have specific requirements on pod IP addresses. Otherwise, the pod rebuilding takes a longer time and the IP address usage decreases.
-  The annotations of the static IP address of the pod object cannot be directly modified. Otherwise, the modification does not take effect in the background. To modify the annotations, modify the **annotations** configuration in the **spec.template** field of the corresponding StatefulSet workload.
-  If there are no ENIs left on the node where the pod with a static IP address is rebuilt and scheduled (the pre-bound ENIs also occupy the ENI quota), the static IP address ENIs preempt the pre-bound ENIs. In this case, the pod starts slightly slowly. If a node uses a static IP address, properly configure the dynamic pre-binding policy for the node to ensure that not all ENIs are pre-bound.

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

   +------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | Annotation                               | Default Value   | Description                                                                                                                                                                                                                                                                                                | Value Range                                                                                                                            |
   +==========================================+=================+============================================================================================================================================================================================================================================================================================================+========================================================================================================================================+
   | yangtse.io/static-ip                     | false           | Specifies whether to enable the static IP address of a pod. This function is supported only for StatefulSet pods or pods without **ownerReferences**. This function is disabled by default.                                                                                                                | **false** or **true**                                                                                                                  |
   +------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | yangtse.io/static-ip-expire-duration     | 5m              | Specifies the interval for reclaiming the expired ENI of the static IP address after the pod with a static IP address is deleted.                                                                                                                                                                          | The time format is Go time type, for example, 1h30m and 5m. For details, see `Go time type <https://pkg.go.dev/time#ParseDuration>`__. |
   +------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | yangtse.io/static-ip-expire-no-cascading | false           | Specifies whether to disable cascading reclamation of StatefulSet workloads.                                                                                                                                                                                                                               | **false** or **true**                                                                                                                  |
   |                                          |                 |                                                                                                                                                                                                                                                                                                            |                                                                                                                                        |
   |                                          |                 | The default value is **false**, indicating that the corresponding static IP address ENI will be deleted with the StatefulSet workload. If you want to retain the static IP address for a new StatefulSet with the same name during the interval for reclaiming the expired ENI, set the value to **true**. |                                                                                                                                        |
   +------------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
