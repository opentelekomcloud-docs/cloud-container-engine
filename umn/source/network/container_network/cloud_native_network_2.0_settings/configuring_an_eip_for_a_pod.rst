:original_name: cce_10_0734.html

.. _cce_10_0734:

Configuring an EIP for a Pod
============================

Application Scenarios
---------------------

In Cloud Native Network 2.0, pods use VPC ENIs or sub-ENIs for networking. You can directly bind EIPs to pods.

To associate an EIP with a pod, simply set the value of the **yangtse.io/pod-with-eip** annotation to **true** when creating the pod. Then, the EIP will be automatically allocated and bound to the pod.

Prerequisites
-------------

A CCE Turbo cluster is available and the cluster version meets the requirements listed in the following table.

+-----------------------------------+-----------------------------------+
| Scenario                          | Cluster Version                   |
+===================================+===================================+
| Allocating an EIP with a pod      | -  v1.19: v1.19.16-r20 or later   |
|                                   | -  v1.21: v1.21.10-r0 or later    |
|                                   | -  v1.23: v1.23.8-r0 or later     |
|                                   | -  v1.25: v1.25.3-r0 or later     |
|                                   | -  Versions later than v1.25      |
+-----------------------------------+-----------------------------------+
| Using an existing EIP for a pod   | v1.23: v1.23.16-r0 or later       |
|                                   |                                   |
|                                   | v1.25: v1.25.11-r0 or later       |
|                                   |                                   |
|                                   | v1.27: v1.27.8-r0 or later        |
|                                   |                                   |
|                                   | v1.28: v1.28.6-r0 or later        |
|                                   |                                   |
|                                   | v1.29: v1.29.2-r0 or later        |
|                                   |                                   |
|                                   | Versions later than v1.29         |
+-----------------------------------+-----------------------------------+

Notes and Constraints
---------------------

-  To access a pod bound with an EIP from the Internet, add security group rules to allow the target request traffic.
-  Only one EIP can be bound to a pod.
-  Configure the EIP-related annotation when creating a pod. After the pod is created, the annotations related to the EIP cannot be modified.
-  Do not perform operations on the EIP associated with a pod through the EIP console or API. Otherwise, the EIP may malfunction. The operations include changing the EIP name, and deleting, unbinding, or binding the EIP.
-  After an automatically allocated EIP is manually deleted, the network malfunctions. In this case, rebuild the pod.

Allocating an EIP with a Pod
----------------------------

If you set the **pod-with-eip** annotation to **true** when creating a pod, an EIP will be automatically allocated and bound to the pod.

.. note::

   -  CCE will automatically add cluster ID, namespace, and pod name labels to an EIP that has been allocated automatically.
   -  If a pod is created with an automatically allocated EIP, deleting the pod will also delete the EIP.

The following uses a Deployment named **nginx** as an example. For details about annotations, see :ref:`Table 2 <cce_10_0734__table324113386408>`.

-  For an automatically allocated EIP with a **dedicated bandwidth** when you create a Deployment, you do not need to specify the bandwidth ID. The following shows an example:

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
              yangtse.io/pod-with-eip: "true"  # An EIP will be automatically allocated when the pod is created.
              yangtse.io/eip-bandwidth-size: "5"  # EIP bandwidth
              yangtse.io/eip-network-type: 5_bgp  # EIP type
              yangtse.io/eip-charge-mode: bandwidth  # EIP billing mode
              yangtse.io/eip-bandwidth-name: <eip_bandwidth_name>  # EIP bandwidth name
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

   .. _cce_10_0734__table042411131132:

   .. table:: **Table 1** Annotations of an EIP with a dedicated bandwidth

      +-------------------------------+-------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+
      | Annotation                    | Mandatory   | Default Value | Description                                                                                                                                                                           | Value Range                                                                                                              |
      +===============================+=============+===============+=======================================================================================================================================================================================+==========================================================================================================================+
      | yangtse.io/pod-with-eip       | Yes         | false         | Whether to allocate an EIP with a pod and bind the EIP to the pod                                                                                                                     | **false** or **true**                                                                                                    |
      +-------------------------------+-------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+
      | yangtse.io/eip-bandwidth-size | No          | 5             | Bandwidth, in Mbit/s                                                                                                                                                                  | The value range varies depending on the region and bandwidth billing mode. For details, see the page on the EIP console. |
      +-------------------------------+-------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+
      | yangtse.io/eip-network-type   | No          | 5_bgp         | EIP type                                                                                                                                                                              | The type varies depending on the region. For details, see the page on the EIP console.                                   |
      +-------------------------------+-------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+
      | yangtse.io/eip-charge-mode    | No          | None          | Billed by traffic or bandwidth                                                                                                                                                        | -  **bandwidth**: billed by bandwidth                                                                                    |
      |                               |             |               |                                                                                                                                                                                       | -  **traffic**: billed by traffic                                                                                        |
      |                               |             |               | **You are advised to configure this parameter.** If this parameter is left blank, no billing mode is specified. In this case, the default value of the EIP API in the region is used. |                                                                                                                          |
      +-------------------------------+-------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+
      | yangtse.io/eip-bandwidth-name | No          | Pod name      | Bandwidth name                                                                                                                                                                        | -  Enter 1 to 64 characters. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed.            |
      |                               |             |               |                                                                                                                                                                                       | -  Minimum length: 1 character                                                                                           |
      |                               |             |               |                                                                                                                                                                                       | -  Maximum length: 64 characters                                                                                         |
      +-------------------------------+-------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+

-  For an automatically allocated EIP with a **shared bandwidth** when you create a Deployment, you are required to specify the bandwidth ID. The following shows an example:

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
              yangtse.io/pod-with-eip: "true"  # An EIP will be automatically allocated when the pod is created.
              yangtse.io/eip-network-type: 5_bgp  # EIP type
              yangtse.io/eip-bandwidth-id: <eip_bandwidth_id>  # Shared bandwidth ID of the EIP
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

   .. _cce_10_0734__table324113386408:

   .. table:: **Table 2** Annotations of an EIP with a shared bandwidth

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

Using an Existing EIP for a Pod
-------------------------------

If you configure the **yangtse.io/eip-id** annotation when creating a pod, the EIP will be automatically bound to the pod.

.. note::

   -  After an existing EIP is bound to a pod, CCE will automatically add the cluster ID, namespace, and pod name labels to the EIP.
   -  When a pod using an existing EIP is deleted, the EIP will be retained. CCE will automatically delete the labels for the cluster ID, namespace, and pod name that were added when the EIP was bound.

The following example shows how to create a Deployment named **nginx** with a single pod with an EIP automatically bound.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: nginx
     template:
       metadata:
         labels:
           app: nginx
         annotations:
           yangtse.io/eip-id: <eip_id> # ID of an existing EIP
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

.. table:: **Table 3** Annotations for using an existing EIP

   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------+
   | Annotation            | Mandatory             | Description                                                                                         |
   +=======================+=======================+=====================================================================================================+
   | yangtse.io/eip-id     | Yes                   | EIP ID                                                                                              |
   |                       |                       |                                                                                                     |
   |                       |                       | **How to obtain**:                                                                                  |
   |                       |                       |                                                                                                     |
   |                       |                       | Log in to the EIP console, click the name of the target EIP in the EIP list, and copy the ID field. |
   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------+

Checking Whether the EIP Bound to the Pod Is Available
------------------------------------------------------

After an EIP is allocated to a pod, the container networking controller binds the EIP to the pod and writes the allocation result back to the pod's **yangtse.io/allocated-ipv4-eip** annotation. The startup time of the pod's service containers may be earlier than the time when the EIP allocation result is written back.

You can configure an init container for the pod, associate the **yangtse.io/allocated-ipv4-eip** annotation with the init container through a downwardAPI volume, and check whether the EIP has been allocated in the init container. You can configure the init container as follows:

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: example
     annotations:
       yangtse.io/pod-with-eip: "true"
       yangtse.io/eip-bandwidth-size: "5"
       yangtse.io/eip-network-type: 5_bgp
       yangtse.io/eip-charge-mode: bandwidth
       yangtse.io/eip-bandwidth-name: "xxx"
   spec:
     initContainers:
     - name: init
       image: busybox:latest
       command: ['timeout', '60', 'sh', '-c', "until grep -E '[0-9]+' /etc/eipinfo/allocated-ipv4-eip; do echo waiting for allocated-ipv4-eip; sleep 2; done"]
       volumeMounts:
           - name: eipinfo
             mountPath: /etc/eipinfo
     volumes:
       - name: eipinfo
         downwardAPI:
           items:
             - path: "allocated-ipv4-eip"
               fieldRef:
                 fieldPath: metadata.annotations['yangtse.io/allocated-ipv4-eip']
   ...
