:original_name: cce_10_0897.html

.. _cce_10_0897:

Binding a Security Group to a Pod Using an Annotation
=====================================================

In cloud native network 2.0, pods use VPC network interfaces or supplementary network interfaces for networking, which allow you to configure security groups. You can bind a security group to a pod by adding an annotation to the pod.

You can configure security groups in the following applications.

+-------------------------------------------------------------------------------------------------------------------------------+------------------------------------------+-------------------------------------------------------------------------------------------+
| Application                                                                                                                   | Annotation                               | Reference                                                                                 |
+===============================================================================================================================+==========================================+===========================================================================================+
| Configure a pod's network interface security group via an annotation.                                                         | yangtse.io/security-group-ids            | :ref:`Create a workload with a security group configured. <cce_10_0897__li1681161010532>` |
+-------------------------------------------------------------------------------------------------------------------------------+------------------------------------------+-------------------------------------------------------------------------------------------+
| Add an additional security group to a pod's network interface via an annotation, while retaining the existing security group. | yangtse.io/additional-security-group-ids | :ref:`Add an additional security group for the workload. <cce_10_0897__li65021682540>`    |
+-------------------------------------------------------------------------------------------------------------------------------+------------------------------------------+-------------------------------------------------------------------------------------------+

.. note::

   The priority of the security group bound to a pod using annotation **yangtse.io/security-group-ids** is higher than those of the security groups in the :ref:`security group policy <cce_10_0288>` (SecurityGroup) and :ref:`cluster container network configuration <cce_10_0196>` (NetworkAttachmentDefinition).

Prerequisites
-------------

A CCE Turbo cluster is available and the cluster version meets the following requirements:

-  v1.23: v1.23.16-r0 or later
-  v1.25: v1.25.11-r0 or later
-  v1.27: v1.27.8-r0 or later
-  v1.28: v1.28.6-r0 or later
-  v1.29: v1.29.2-r0 or later
-  Versions later than v1.29

Using kubectl
-------------

-  .. _cce_10_0897__li1681161010532:

   Create a workload with a security group configured. The security group bound to the pod is subject to the one configured using an annotation.

   .. note::

      If the pod has been bound to a security group, the configuration will be overwritten.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx
      spec:
        replicas: 3
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
            annotations:
              yangtse.io/security-group-ids: *****  # Security group ID. Use commas (,) to separate multiple security groups.
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

-  .. _cce_10_0897__li65021682540:

   Add an additional security group for the workload.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx
      spec:
        replicas: 3
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
            annotations:
              yangtse.io/additional-security-group-ids: *****  # Security group ID. Use commas (,) to separate multiple security groups.
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

.. table:: **Table 1** Annotations for configuring a security group for a pod

   +------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Annotation                               | Description                                                                                                                                                                                                           | Value Range                                                                                                                                                                  |
   +==========================================+=======================================================================================================================================================================================================================+==============================================================================================================================================================================+
   | yangtse.io/security-group-ids            | Configure a security group for a pod. The security group of the pod is subject to the one configured using this annotation. If the pod already has a security group, the original security group will be overwritten. | Security group IDs. A maximum of five IDs are allowed. Use commas (,) to separate multiple security groups.                                                                  |
   |                                          |                                                                                                                                                                                                                       |                                                                                                                                                                              |
   |                                          |                                                                                                                                                                                                                       | If this parameter is left blank, no annotation will be used to configure a security group for the pod.                                                                       |
   +------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | yangtse.io/additional-security-group-ids | Add more security groups to a pod.                                                                                                                                                                                    | Security group IDs. The total number of newly added security group IDs and existing security group IDs cannot exceed 5. Use commas (,) to separate multiple security groups. |
   +------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
