:original_name: cce_10_0382.html

.. _cce_10_0382:

Configuring QoS for a Pod
=========================

Scenario
--------

Bandwidth preemption occurs between different containers deployed on the same node, which may cause service jitter. You can configure bandwidth limitation for the pod to solve this problem.

Specifications
--------------

The following table lists the bandwidth limitation specifications of pods.

+-------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+-------------------------------------------------------------------+---------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------+
| Specifications                                        | Tunnel Network                                                                                                                                                             | VPC Network                    | Cloud Native Network 2.0                                          | VPC + DataPlane V2                                                                          | Cloud Native Network 2.0 + DataPlane V2                                       |
+=======================================================+============================================================================================================================================================================+================================+===================================================================+=============================================================================================+===============================================================================+
| Supported cluster versions                            | All versions                                                                                                                                                               | Clusters of v1.19.10 and later | Clusters of v1.19.10 and later                                    | Cluster version: v1.27.16-r30, v1.28.15-r20, v1.29.13-r0, v1.30.10-r0, v1.31.6-r0, or later | Cluster version: v1.27.16-r10, v1.28.15-r0, v1.29.10-r0, v1.30.6-r0, or later |
+-------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+-------------------------------------------------------------------+---------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------+
| Egress bandwidth limitation                           | Supported                                                                                                                                                                  | Supported                      | Supported                                                         | Supported                                                                                   | Supported                                                                     |
+-------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+-------------------------------------------------------------------+---------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------+
| Ingress bandwidth limitation                          | Supported                                                                                                                                                                  | Supported                      | Supported                                                         | Not supported                                                                               | Not supported                                                                 |
+-------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+-------------------------------------------------------------------+---------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------+
| Scenarios where bandwidth limitation is not supported | None                                                                                                                                                                       | None                           | -  Pod access to cloud service CIDR blocks such as 100.125.0.0/16 | Mutual access between pods on the host node                                                 | -  Pod access to cloud service CIDR blocks such as 100.125.0.0/16             |
|                                                       |                                                                                                                                                                            |                                | -  Pod health check                                               |                                                                                             | -  Pod health check                                                           |
+-------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+-------------------------------------------------------------------+---------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------+
| Bandwidth limitation range                            | Only the rate limit in the unit of Mbit/s or Gbit/s is supported, for example, 100 Mbit/s or 1 Gbit/s. The minimum value is 1 Mbit/s and the maximum value is 4.29 Gbit/s. |                                |                                                                   | The minimum value is 1 kbit/s, and the maximum value is 1 Pbit/s.                           | The minimum value is 1 kbit/s, and the maximum value is 1 Pbit/s.             |
+-------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------+-------------------------------------------------------------------+---------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------+

-  CCE DataPlane V2 is released with restrictions. To use this feature, submit a service ticket to CCE.
-  After DataPlane V2 network acceleration is enabled, pods on the nodes running HCE OS 2.0 use EDT to limit the egress bandwidth. The ingress bandwidth limitation is not supported. In other network modes, a Token Bucket Filter (TBF) qdisc is used to limit the bandwidth.
-  Pod bandwidth limitation applies to regular containers (runC as the container runtime), not secure containers (Kata Containers as the container runtime).
-  Pod bandwidth limitation does not apply to hostNetwork pods.

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
