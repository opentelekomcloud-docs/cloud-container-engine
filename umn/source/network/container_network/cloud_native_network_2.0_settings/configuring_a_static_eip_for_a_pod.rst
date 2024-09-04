:original_name: cce_10_0651.html

.. _cce_10_0651:

Configuring a Static EIP for a Pod
==================================

Application Scenarios
---------------------

In Cloud Native Network 2.0, static public IP addresses (EIPs) can be assigned to StatefulSets or pods created directly.

Constraints
-----------

-  You can configure a static EIP for a pod only in CCE Turbo clusters of the following versions:

   -  v1.19: v1.19.16-r20 or later
   -  v1.21: v1.21.10-r0 or later
   -  v1.23: v1.23.8-r0 or later
   -  v1.25: v1.25.3-r0 or later
   -  v1.25 or later

-  The static EIP function must be enabled together with the function of automatically allocating an EIP for a pod. For details, see :ref:`Configuring an EIP for a Pod <cce_10_0734>`.
-  Only StatefulSet pods or pods created directly can be bound with static EIPs. Deployments, DaemonSets, and other types of workloads cannot be bound with static EIPs.
-  After a static EIP is allocated, the EIP attributes cannot be modified through the pod within the EIP lifecycle (before the EIP expires or it is still being used by the pod).
-  Do not configure a static EIP for services that do not have specific requirements on pod EIPs. Otherwise, the pod rebuilding takes a longer time.


Configuring a Static EIP for a Pod
----------------------------------

When creating a pod to be bound with a static IP address, configure the EIP annotation. Then, an EIP will be automatically allocated and bound to the pod.

The following uses a StatefulSet named **nginx** as an example. For details about annotations, see :ref:`Table 1 <cce_10_0651__table9600144121919>`.

-  For an automatically allocated EIP with a **dedicated bandwidth** when you create a StatefulSet, you do not need to specify the bandwidth ID. The following shows an example:

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
              yangtse.io/static-eip: 'true'   # Static EIP bound to the pod
              yangtse.io/static-eip-expire-no-cascading: 'false'   # Deleting the EIP with the associated workload
              yangtse.io/static-eip-expire-duration: 5m   # Interval for reclaiming expired EIPs
              yangtse.io/pod-with-eip: 'true'   # An EIP will be automatically allocated when the pod is created.
              yangtse.io/eip-bandwidth-size: '5'    # EIP bandwidth
              yangtse.io/eip-network-type: 5_bgp    # EIP type
              yangtse.io/eip-charge-mode: bandwidth    # EIP billing mode
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

-  For an automatically allocated EIP with a **shared bandwidth** when you create a StatefulSet, you are required to specify the bandwidth ID. The following shows an example:

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
              yangtse.io/static-eip: 'true'   # Static EIP bound to the pod
              yangtse.io/pod-with-eip: 'true'   # An EIP will be automatically allocated when the pod is created.
              yangtse.io/eip-network-type: 5_bgp    # EIP type
              yangtse.io/eip-bandwidth-id: <eip_bandwidth_id>    # Shared bandwidth ID of the EIP
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

.. _cce_10_0651__table9600144121919:

.. table:: **Table 1** Annotations of the pod's static EIP

   +-------------------------------------------+-------------+---------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | Annotation                                | Mandatory   | Default Value | Description                                                                                                                                                                                                                                                                              | Value Range                                                                                                                            |
   +===========================================+=============+===============+==========================================================================================================================================================================================================================================================================================+========================================================================================================================================+
   | yangtse.io/static-eip                     | Yes         | false         | Specifies whether to enable the static EIP of a pod. This function is supported only for StatefulSet pods or pods without **ownerReferences**. This function is disabled by default.                                                                                                     | **false** or **true**                                                                                                                  |
   +-------------------------------------------+-------------+---------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | yangtse.io/static-eip-expire-duration     | No          | 5m            | Specifies the interval for reclaiming the expired static EIP after the pod with a static EIP is deleted.                                                                                                                                                                                 | The time format is Go time type, for example, 1h30m and 5m. For details, see `Go time type <https://pkg.go.dev/time#ParseDuration>`__. |
   +-------------------------------------------+-------------+---------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | yangtse.io/static-eip-expire-no-cascading | No          | false         | Specifies whether to disable cascading reclamation of StatefulSet workloads.                                                                                                                                                                                                             | **false** or **true**                                                                                                                  |
   |                                           |             |               |                                                                                                                                                                                                                                                                                          |                                                                                                                                        |
   |                                           |             |               | The default value is **false**, indicating that the corresponding static EIP will be deleted with the StatefulSet workload. If you want to retain the static EIP for a new StatefulSet with the same name during the interval for reclaiming the expired EIP, set the value to **true**. |                                                                                                                                        |
   +-------------------------------------------+-------------+---------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+

.. table:: **Table 2** Annotations of an EIP with a dedicated bandwidth

   +-------------------------------+-------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------+
   | Annotation                    | Mandatory   | Default Value | Description                                                                                                                                                                           | Value Range                                                                                                   |
   +===============================+=============+===============+=======================================================================================================================================================================================+===============================================================================================================+
   | yangtse.io/pod-with-eip       | Yes         | false         | Whether to allocate an EIP with a pod and bind the EIP to the pod                                                                                                                     | **false** or **true**                                                                                         |
   +-------------------------------+-------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------+
   | yangtse.io/eip-bandwidth-size | No          | 5             | Bandwidth, in Mbit/s                                                                                                                                                                  | The value range varies depending on the region and bandwidth billing mode. For details, see the EIP console.  |
   +-------------------------------+-------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------+
   | yangtse.io/eip-network-type   | No          | 5_bgp         | EIP type                                                                                                                                                                              | The type varies depending on the region. For details, see on the EIP console.                                 |
   +-------------------------------+-------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------+
   | yangtse.io/eip-charge-mode    | No          | None          | Billed by traffic or bandwidth                                                                                                                                                        | -  **bandwidth**: billed by bandwidth                                                                         |
   |                               |             |               |                                                                                                                                                                                       | -  **traffic**: billed by traffic                                                                             |
   |                               |             |               | **You are advised to configure this parameter.** If this parameter is left blank, no billing mode is specified. In this case, the default value of the EIP API in the region is used. |                                                                                                               |
   +-------------------------------+-------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------+
   | yangtse.io/eip-bandwidth-name | No          | Pod name      | Bandwidth name                                                                                                                                                                        | -  Enter 1 to 64 characters. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed. |
   |                               |             |               |                                                                                                                                                                                       | -  Minimum length: 1 character                                                                                |
   |                               |             |               |                                                                                                                                                                                       | -  Maximum length: 64 characters                                                                              |
   +-------------------------------+-------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------+

.. table:: **Table 3** Annotations of an EIP with a shared bandwidth

   +-----------------------------+-------------------------------------------+---------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------+
   | Annotation                  | Mandatory                                 | Default Value | Description                                                                                                                                                                                                                             | Value Range                                                              |
   +=============================+===========================================+===============+=========================================================================================================================================================================================================================================+==========================================================================+
   | yangtse.io/pod-with-eip     | Yes                                       | false         | Whether to allocate an EIP with a pod and bind the EIP to the pod                                                                                                                                                                       | **false** or **true**                                                    |
   +-----------------------------+-------------------------------------------+---------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------+
   | yangtse.io/eip-network-type | No                                        | 5_bgp         | EIP type                                                                                                                                                                                                                                | -  5_bgp                                                                 |
   |                             |                                           |               |                                                                                                                                                                                                                                         |                                                                          |
   |                             |                                           |               |                                                                                                                                                                                                                                         | The specific type varies with regions. For details, see the EIP console. |
   +-----------------------------+-------------------------------------------+---------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------+
   | yangtse.io/eip-bandwidth-id | Mandatory when a shared bandwidth is used | None          | ID of an existing bandwidth                                                                                                                                                                                                             | None                                                                     |
   |                             |                                           |               |                                                                                                                                                                                                                                         |                                                                          |
   |                             |                                           |               | -  If this parameter is not specified, the EIP with a dedicated bandwidth is used by default. For details about how to configure parameters for an EIP with a dedicated bandwidth, see :ref:`Table 1 <cce_10_0734__table042411131132>`. |                                                                          |
   |                             |                                           |               | -  Only the **yangtse.io/eip-network-type** field can be specified concurrently, and this field is optional.                                                                                                                            |                                                                          |
   +-----------------------------+-------------------------------------------+---------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------+

Deleting a Static EIP
---------------------

After a pod is deleted, if another pod with the same name is created before the static EIP expires, the EIP can still be used. The static EIP is deleted only if there is no new pod with the name the same as that of the deleted pod before the EIP expires, or the function of deleting the EIP with the associated StatefulSet is enabled and the StatefulSet is deleted.
