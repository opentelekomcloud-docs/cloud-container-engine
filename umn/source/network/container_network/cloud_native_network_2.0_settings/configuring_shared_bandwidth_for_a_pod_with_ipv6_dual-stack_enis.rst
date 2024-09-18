:original_name: cce_10_0604.html

.. _cce_10_0604:

Configuring Shared Bandwidth for a Pod with IPv6 Dual-Stack ENIs
================================================================

Application Scenarios
---------------------

By default, pods with IPv6 dual-stack ENIs can access only the IPv6 private network. To access the public network, configure shared bandwidth for such pods.

Notes and Constraints
---------------------

-  Only CCE Turbo clusters are supported and the following constraints must be met:

   -  IPv6 dual stack has been enabled for the cluster.
   -  The cluster version is v1.23.8-r0, v1.25.3-r0, or later.

-  The number of IPv6 ENIs that can be added to a shared bandwidth is limited by the tenant quota. The default value is 20.
-  HostNetwork Pods are not supported.
-  All types of workloads are supported. When configuring IPv6 shared bandwidth for workloads with the replicas attribute, such as Deployment and StatefulSet, ensure that the number of replicas and the maximum number of pods during the upgrade are less than the remaining quota of IPv6 ENIs that can be added to the shared bandwidth.
-  IPv6 dual-stack pod configured with shared bandwidth: When a pod is created, the CNI returns a success message only after the IPv6 dual-stack ENI is inserted into the shared bandwidth. When a pod is deleted, the IPv6 dual-stack ENI is removed from the shared bandwidth after the pod is completely deleted or the pod is in the deleting status for 30 seconds.
-  If the IPv6 dual-stack ENI corresponding to the pod fails to be added to the shared bandwidth, an alarm event **FailedIPv6InsertBandwidth** is generated on the pod, for example, when the quota is exceeded or flow control is triggered. Rectify the fault based on the alarm event.
-  On the **Shared Bandwidths** page of the EIP console, go to the target shared bandwidth details page, and click the **IPv6 Addresses** tab. The IPv6 dual-stack ENI whose **Associated Instance** is CCE is displayed. Do not remove the ENI directly on the page or using VPC APIs, otherwise, your services may be affected.

Using the CCE Console
---------------------

When creating a workload, you can set the IPv6 shared bandwidth on the **Advanced Settings > Network Configuration** area.

Using kubectl
-------------

You can add annotations to a Deployment to specify the shared bandwidth to be added to the IPv6 dual-stack ENI of the pod. The following is an example:

.. code-block::

   ...
   spec:
     selector:
       matchLabels:
         app: demo
         version: v1
     template:
       metadata:
         annotations:
           yangtse.io/ipv6-bandwidth-id: "xxx"

-  **yangtse.io/ipv6-bandwidth-id**: specifies the ID of the shared bandwidth. The IPv6 dual-stack ENIs corresponding to the pod will be added to the shared bandwidth. You can query the ID on the **Shared Bandwidths** page on the EIP console.
