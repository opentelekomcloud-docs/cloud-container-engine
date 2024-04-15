:original_name: cce_10_0382.html

.. _cce_10_0382:

Configuring QoS for a Pod
=========================

Scenario
--------

Bandwidth preemption occurs between different containers deployed on the same node, which may cause service jitter. You can configure QoS rate limiting for inter-pod access to prevent this problem.

Constraints
-----------

The following shows constraints on setting the rate limiting for inter-pod access:

+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+----------------------------------------------------------------------------------+
| Constraint Type           | Tunnel network model                                                                                                                                                        | VPC network model              | Cloud Native 2.0 Network Model                                                   |
+===========================+=============================================================================================================================================================================+================================+==================================================================================+
| Supported versions        | All versions                                                                                                                                                                | Clusters of v1.19.10 and later | Clusters of v1.19.10 and later                                                   |
+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+----------------------------------------------------------------------------------+
| Supported runtime types   | Only common containers                                                                                                                                                      |                                |                                                                                  |
+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+----------------------------------------------------------------------------------+
| Supported pod types       | Only non-HostNetwork pods                                                                                                                                                   |                                |                                                                                  |
+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+----------------------------------------------------------------------------------+
| Supported scenarios       | Inter-pod access, pods accessing nodes, and pods accessing services                                                                                                         |                                |                                                                                  |
+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+----------------------------------------------------------------------------------+
| Constraints               | None                                                                                                                                                                        | None                           | -  Pods access external cloud service CIDR blocks 100.64.0.0/10 and 214.0.0.0/8. |
|                           |                                                                                                                                                                             |                                | -  Traffic rate limiting of health check                                         |
+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+----------------------------------------------------------------------------------+
| Value range of rate limit | Only the rate limit in the unit of Mbit/s or Gbit/s is supported, for example, 100 Mbit/s and 1 Gbit/s. The minimum value is 1 Mbit/s and the maximum value is 4.29 Gbit/s. |                                |                                                                                  |
+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+----------------------------------------------------------------------------------+

Using the CCE Console
---------------------

When creating a workload on the console, you can set pod ingress and egress bandwidth limits by clicking **Network Configuration** in the **Advanced Settings** area.

Using kubectl
-------------

You can add annotations to a workload to specify its egress and ingress bandwidth.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: test
     namespace: default
     labels:
       app: test
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: test
     template:
       metadata:
         labels:
           app: test
         annotations:
           kubernetes.io/ingress-bandwidth: 100M
           kubernetes.io/egress-bandwidth: 100M
       spec:
         containers:
           - name: container-1
             image: nginx:alpine
             imagePullPolicy: IfNotPresent
         imagePullSecrets:
           - name: default-secret

-  **kubernetes.io/ingress-bandwidth**: ingress bandwidth of the pod
-  **kubernetes.io/egress-bandwidth**: egress bandwidth of the pod

If these two parameters are not specified, the bandwidth is not limited.

.. note::

   After modifying the ingress or egress bandwidth limit of a pod, restart the container for the modification to take effect. After annotations are modified in a pod not managed by workloads, the container will not be restarted, so the bandwidth limits do not take effect. You can create a pod again or manually restart the container.
