:original_name: cce_10_0014.html

.. _cce_10_0014:

LoadBalancer
============

.. _cce_10_0014__section19854101411508:

Scenario
--------

A workload can be accessed from public networks through a load balancer, which is more secure and reliable than EIP.

The LoadBalancer access address is in the format of <IP address of public network load balancer>:<access port>, for example, **10.117.117.117:80**.

In this access mode, requests are transmitted through an ELB load balancer to a node and then forwarded to the destination pod through the Service.


.. figure:: /_static/images/en-us_image_0000001244141181.png
   :alt: **Figure 1** LoadBalancer

   **Figure 1** LoadBalancer

When **CCE Turbo clusters and dedicated load balancers** are used, passthrough networking is supported to reduce service latency and ensure zero performance loss.

External access requests are directly forwarded from a load balancer to pods. Internal access requests can be forwarded to a pod through a Service.


.. figure:: /_static/images/en-us_image_0000001249073211.png
   :alt: **Figure 2** Passthrough networking

   **Figure 2** Passthrough networking

Notes and Constraints
---------------------

-  LoadBalancer Services allow workloads to be accessed from public networks through **ELB**. This access mode has the following restrictions:

   -  It is recommended that automatically created load balancers not be used by other resources. Otherwise, these load balancers cannot be completely deleted, causing residual resources.
   -  Do not change the listener name for the load balancer in clusters of v1.15 and earlier. Otherwise, the load balancer cannot be accessed.

-  After a Service is created, if the affinity setting is switched from the cluster level to the node level, the connection tracing table will not be cleared. You are advised not to modify the Service affinity setting after the Service is created. If you need to modify it, create a Service again.
-  If the service affinity is set to the node level (that is, **externalTrafficPolicy** is set to **Local**), the cluster may fail to access the Service by using the ELB address. For details, see :ref:`Why a Cluster Fails to Access Services by Using the ELB Address <cce_10_0014__section52631714117>`.
-  CCE Turbo clusters support only cluster-level service affinity.
-  Dedicated ELB load balancers can be used only in clusters of v1.17 and later.
-  Dedicated load balancers must be the network type (TCP/UDP) supporting private networks (with a private IP). If the Service needs to support HTTP, the specifications of dedicated load balancers must use HTTP/HTTPS (application load balancing) in addition to TCP/UDP (network load balancing).
-  If you create a LoadBalancer Service on the CCE console, a random node port is automatically generated. If you use kubectl to create a LoadBalancer Service, a random node port is generated unless you specify one.
-  In a CCE cluster, if the cluster-level affinity is configured for a LoadBalancer Service, requests are distributed to the node ports of each node using SNAT when entering the cluster. The number of node ports cannot exceed the number of available node ports on the node. If the Service affinity is at the node level (local), there is no such constraint. In a CCE Turbo cluster, this constraint applies to shared ELB load balancers, but not dedicated ones. You are advised to use dedicated ELB load balancers in CCE Turbo clusters.
-  When the cluster service forwarding (proxy) mode is IPVS, the node IP cannot be configured as the external IP of the Service. Otherwise, the node is unavailable.
-  In a cluster using the IPVS proxy mode, if the ingress and Service use the same ELB load balancer, the ingress cannot be accessed from the nodes and containers in the cluster because kube-proxy mounts the LoadBalancer Service address to the ipvs-0 bridge. This bridge intercepts the traffic of the load balancer connected to the ingress. You are advised to use different ELB load balancers for the ingress and Service.

Creating a LoadBalancer Service
-------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster.
#. Choose **Networking** in the navigation pane and click **Create Service** in the upper right corner.
#. Set parameters.

   -  **Service Name**: Specify a Service name, which can be the same as the workload name.

   -  **Access Type**: Select **LoadBalancer**.

   -  **Namespace**: Namespace to which the workload belongs.

   -  **Service Affinity**: For details, see :ref:`externalTrafficPolicy (Service Affinity) <cce_10_0142__section18134208069>`.

      -  **Cluster level**: The IP addresses and access ports of all nodes in a cluster can be used to access the workload associated with the Service. Service access will cause performance loss due to route redirection, and the source IP address of the client cannot be obtained.
      -  **Node level**: Only the IP address and access port of the node where the workload is located can access the workload associated with the Service. Service access will not cause performance loss due to route redirection, and the source IP address of the client can be obtained.

   -  **Selector**: Add a label and click **Add**. A Service selects a pod based on the added label. You can also click **Reference Workload Label** to reference the label of an existing workload. In the dialog box that is displayed, select a workload and click **OK**.

   -  **Load Balancer**

      Select the load balancer to interconnect. Only load balancers in the same VPC as the cluster are supported. If no load balancer is available, click **Create Load Balancer** to create one on the ELB console.

      You can click **Edit** and configure load balancer parameters in the **Load Balancer** dialog box.

      -  **Distribution Policy**: Three algorithms are available: weighted round robin, weighted least connections algorithm, or source IP hash.

         .. note::

            -  **Weighted round robin**: Requests are forwarded to different servers based on their weights, which indicate server processing performance. Backend servers with higher weights receive proportionately more requests, whereas equal-weighted servers receive the same number of requests. This algorithm is often used for short connections, such as HTTP services.
            -  **Weighted least connections**: In addition to the weight assigned to each server, the number of connections processed by each backend server is also considered. Requests are forwarded to the server with the lowest connections-to-weight ratio. Building on **least connections**, the **weighted least connections** algorithm assigns a weight to each server based on their processing capability. This algorithm is often used for persistent connections, such as database connections.
            -  **Source IP hash**: The source IP address of each request is calculated using the hash algorithm to obtain a unique hash key, and all backend servers are numbered. The generated key allocates the client to a particular server. This enables requests from different clients to be distributed in load balancing mode and ensures that requests from the same client are forwarded to the same server. This algorithm applies to TCP connections without cookies.

      -  **Type**: This function is disabled by default. You can select **Source IP address**. Listeners ensure session stickiness based on IP addresses. Requests from the same IP address will be forwarded to the same backend server.
      -  **Health Check**: This function is disabled by default. The health check is for the load balancer. When TCP is selected during the :ref:`port settings <cce_10_0014__li388800117144>`, you can choose either TCP or HTTP. When UDP is selected during the :ref:`port settings <cce_10_0014__li388800117144>`, only UDP is supported.. By default, the service port (Node Port and container port of the Service) is used for health check. You can also specify another port for health check. After the port is specified, a service port named **cce-healthz** will be added for the Service.

   -  .. _cce_10_0014__li388800117144:

      **Port Settings**

      -  **Protocol**: protocol used by the Service.
      -  **Service Port**: port used by the Service. The port number ranges from 1 to 65535.
      -  **Container Port**: port on which the workload listens. For example, Nginx uses port 80 by default.

   -  **Annotation**: The LoadBalancer Service has some advanced CCE functions, which are implemented by annotations. For details, see :ref:`Service Annotations <cce_10_0385>`. When you use kubectl to create a container, annotations will be used. For details, see :ref:`Using kubectl to Create a Service (Using an Existing Load Balancer) <cce_10_0014__section1984211714368>` and :ref:`Using kubectl to Create a Service (Automatically Creating a Load Balancer) <cce_10_0014__section12168131904611>`.

#. Click **OK**.

.. _cce_10_0014__section1984211714368:

Using kubectl to Create a Service (Using an Existing Load Balancer)
-------------------------------------------------------------------

You can set the access type when creating a workload using kubectl. This section uses an Nginx workload as an example to describe how to add a LoadBalancer Service using kubectl.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create and edit the **nginx-deployment.yaml** and **nginx-elb-svc.yaml** files.

   The file names are user-defined. **nginx-deployment.yaml** and **nginx-elb-svc.yaml** are merely example file names.

   **vi nginx-deployment.yaml**

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
          spec:
            containers:
            - image: nginx
              name: nginx
            imagePullSecrets:
            - name: default-secret

   **vi nginx-elb-svc.yaml**

   .. note::

      Before enabling sticky session, ensure that the following conditions are met:

      -  The workload protocol is TCP.
      -  Anti-affinity has been configured between pods of the workload. That is, all pods of the workload are deployed on different nodes. For details, see :ref:`Scheduling Policy (Affinity/Anti-affinity) <cce_10_0232>`.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.id: 5083f225-9bf8-48fa-9c8b-67bd9693c4c0   # ELB ID. Replace it with the actual value.
          kubernetes.io/elb.class: union                   # Load balancer type
        name: nginx
      spec:
        ports:
        - name: service0
          port: 80     # Port for accessing the Service, which is also the listener port on the load balancer.
          protocol: TCP
          targetPort: 80  # Port used by a Service to access the target container. This port is closely related to the applications running in a container.
        selector:
          app: nginx
        type: LoadBalancer

   .. table:: **Table 1** Key parameters

      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Mandatory       | Type                                                     | Description                                                                                                                                                                                                                                                                                            |
      +===========================================+=================+==========================================================+========================================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.class                   | Yes             | String                                                   | Select a proper load balancer type as required.                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | The value can be:                                                                                                                                                                                                                                                                                      |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | -  **union**: shared load balancer                                                                                                                                                                                                                                                                     |
      |                                           |                 |                                                          | -  **performance**: dedicated load balancer, which can be used only in clusters of v1.17 and later.                                                                                                                                                                                                    |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-mode   | No              | String                                                   | Listeners ensure session stickiness based on IP addresses. Requests from the same IP address will be forwarded to the same backend server.                                                                                                                                                             |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | -  Disabling sticky session: Do not set this parameter.                                                                                                                                                                                                                                                |
      |                                           |                 |                                                          | -  Enabling sticky session: Set this parameter to **SOURCE_IP**, indicating that the sticky session is based on the source IP address.                                                                                                                                                                 |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-option | No              | :ref:`Table 2 <cce_10_0014__table43592047133910>` Object | This parameter specifies the sticky session timeout.                                                                                                                                                                                                                                                   |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.id                      | Yes             | String                                                   | This parameter indicates the ID of a load balancer. The value can contain 1 to 100 characters.                                                                                                                                                                                                         |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | Mandatory when an existing load balancer is to be associated.                                                                                                                                                                                                                                          |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | **Obtaining the load balancer ID:**                                                                                                                                                                                                                                                                    |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | On the management console, click **Service List**, and choose **Networking** > **Elastic Load Balance**. Click the name of the target load balancer. On the **Summary** tab page, find and copy the ID.                                                                                                |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | .. note::                                                                                                                                                                                                                                                                                              |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          |    The system preferentially interconnects with the load balancer based on the **kubernetes.io/elb.id** field. If this field is not specified, the **spec.loadBalancerIP** field is used (optional and available only in 1.23 and earlier versions).                                                   |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          |    Do not use the **spec.loadBalancerIP** field to connect to the load balancer. This field will be discarded by Kubernetes. For details, see `Deprecation <https://github.com/kubernetes/kubernetes/blob/8f2371bcceff7962ddb4901c36536c6ff659755b/CHANGELOG/CHANGELOG-1.24.md#changes-by-kind-13>`__. |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.subnet-id               | ``-``           | String                                                   | This parameter indicates the ID of the subnet where the cluster is located. The value can contain 1 to 100 characters.                                                                                                                                                                                 |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | -  Mandatory when a cluster of v1.11.7-r0 or earlier is to be automatically created.                                                                                                                                                                                                                   |
      |                                           |                 |                                                          | -  Optional for clusters later than v1.11.7-r0.                                                                                                                                                                                                                                                        |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.lb-algorithm            | No              | String                                                   | This parameter indicates the load balancing algorithm of the backend server group. The default value is **ROUND_ROBIN**.                                                                                                                                                                               |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | Options:                                                                                                                                                                                                                                                                                               |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | -  **ROUND_ROBIN**: weighted round robin algorithm                                                                                                                                                                                                                                                     |
      |                                           |                 |                                                          | -  **LEAST_CONNECTIONS**: weighted least connections algorithm                                                                                                                                                                                                                                         |
      |                                           |                 |                                                          | -  **SOURCE_IP**: source IP hash algorithm                                                                                                                                                                                                                                                             |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | When the value is **SOURCE_IP**, the weights of backend servers in the server group are invalid.                                                                                                                                                                                                       |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.health-check-flag       | No              | String                                                   | Whether to enable the ELB health check.                                                                                                                                                                                                                                                                |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | -  Enabling health check: Leave blank this parameter or set it to **on**.                                                                                                                                                                                                                              |
      |                                           |                 |                                                          | -  Disabling health check: Set this parameter to **off**.                                                                                                                                                                                                                                              |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | If this parameter is enabled, the :ref:`kubernetes.io/elb.health-check-option <cce_10_0014__table236017471397>` field must also be specified at the same time.                                                                                                                                         |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.health-check-option     | No              | :ref:`Table 3 <cce_10_0014__table236017471397>` Object   | ELB health check configuration items.                                                                                                                                                                                                                                                                  |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0014__table43592047133910:

   .. table:: **Table 2** Data structure of the elb.session-affinity-option field

      +---------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------+
      | Parameter           | Mandatory       | Type            | Description                                                                                                                  |
      +=====================+=================+=================+==============================================================================================================================+
      | persistence_timeout | Yes             | String          | Sticky session timeout, in minutes. This parameter is valid only when **elb.session-affinity-mode** is set to **SOURCE_IP**. |
      |                     |                 |                 |                                                                                                                              |
      |                     |                 |                 | Value range: 1 to 60. Default value: **60**                                                                                  |
      +---------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0014__table236017471397:

   .. table:: **Table 3** Data structure description of the **elb.health-check-option** field

      +-----------------+-----------------+-----------------+------------------------------------------------------------------------------------+
      | Parameter       | Mandatory       | Type            | Description                                                                        |
      +=================+=================+=================+====================================================================================+
      | delay           | No              | String          | Initial waiting time (in seconds) for starting the health check.                   |
      |                 |                 |                 |                                                                                    |
      |                 |                 |                 | Value range: 1 to 50. Default value: **5**                                         |
      +-----------------+-----------------+-----------------+------------------------------------------------------------------------------------+
      | timeout         | No              | String          | Health check timeout, in seconds.                                                  |
      |                 |                 |                 |                                                                                    |
      |                 |                 |                 | Value range: 1 to 50. Default value: **10**                                        |
      +-----------------+-----------------+-----------------+------------------------------------------------------------------------------------+
      | max_retries     | No              | String          | Maximum number of health check retries.                                            |
      |                 |                 |                 |                                                                                    |
      |                 |                 |                 | Value range: 1 to 10. Default value: **3**                                         |
      +-----------------+-----------------+-----------------+------------------------------------------------------------------------------------+
      | protocol        | No              | String          | Health check protocol.                                                             |
      |                 |                 |                 |                                                                                    |
      |                 |                 |                 | Default value: protocol of the associated Service                                  |
      |                 |                 |                 |                                                                                    |
      |                 |                 |                 | Value options: TCP, UDP, or HTTP                                                   |
      +-----------------+-----------------+-----------------+------------------------------------------------------------------------------------+
      | path            | No              | String          | Health check URL. This parameter needs to be configured when the protocol is HTTP. |
      |                 |                 |                 |                                                                                    |
      |                 |                 |                 | Default value: **/**                                                               |
      |                 |                 |                 |                                                                                    |
      |                 |                 |                 | The value can contain 1 to 10,000 characters.                                      |
      +-----------------+-----------------+-----------------+------------------------------------------------------------------------------------+

#. Create a workload.

   **kubectl create -f nginx-deployment.yaml**

   If information similar to the following is displayed, the workload has been created.

   .. code-block::

      deployment/nginx created

   **kubectl get pod**

   If information similar to the following is displayed, the workload is running.

   .. code-block::

      NAME                     READY     STATUS             RESTARTS   AGE
      nginx-2601814895-c1xhw   1/1       Running            0          6s

#. Create a Service.

   **kubectl create -f nginx-elb-svc.yaml**

   If information similar to the following is displayed, the Service has been created.

   .. code-block::

      service/nginx created

   **kubectl get svc**

   If information similar to the following is displayed, the access type has been set successfully, and the workload is accessible.

   .. code-block::

      NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
      kubernetes   ClusterIP      10.247.0.1       <none>        443/TCP        3d
      nginx        LoadBalancer   10.247.130.196   10.78.42.242   80:31540/TCP   51s

#. Enter the URL in the address box of the browser, for example, **10.78.42.242:80**. **10.78.42.242** indicates the IP address of the load balancer, and **80** indicates the access port displayed on the CCE console.

   The Nginx is accessible.


   .. figure:: /_static/images/en-us_image_0000001243981181.png
      :alt: **Figure 3** Accessing Nginx through the LoadBalancer Service

      **Figure 3** Accessing Nginx through the LoadBalancer Service

.. _cce_10_0014__section12168131904611:

Using kubectl to Create a Service (Automatically Creating a Load Balancer)
--------------------------------------------------------------------------

You can add a Service when creating a workload using kubectl. This section uses an Nginx workload as an example to describe how to add a LoadBalancer Service using kubectl.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create and edit the **nginx-deployment.yaml** and **nginx-elb-svc.yaml** files.

   The file names are user-defined. **nginx-deployment.yaml** and **nginx-elb-svc.yaml** are merely example file names.

   **vi nginx-deployment.yaml**

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
          spec:
            containers:
            - image: nginx
              name: nginx
            imagePullSecrets:
            - name: default-secret

   **vi nginx-elb-svc.yaml**

   .. note::

      Before enabling sticky session, ensure that the following conditions are met:

      -  The workload protocol is TCP.
      -  Anti-affinity has been configured between pods of the workload. That is, all pods of the workload are deployed on different nodes. For details, see :ref:`Scheduling Policy (Affinity/Anti-affinity) <cce_10_0232>`.

   Example of a Service using a shared, public network load balancer:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/elb.autocreate:
              '{
                  "type": "public",
                  "bandwidth_name": "cce-bandwidth-1551163379627",
                  "bandwidth_chargemode": "bandwidth",
                  "bandwidth_size": 5,
                  "bandwidth_sharetype": "PER",
                  "eip_type": "5_bgp"
              }'
        labels:
          app: nginx
        name: nginx
      spec:
        ports:
        - name: service0
          port: 80
          protocol: TCP
          targetPort: 80
        selector:
          app: nginx
        type: LoadBalancer

   Example Service using a public network dedicated load balancer (for clusters of v1.17 and later only):

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: nginx
        labels:
          app: nginx
        namespace: default
        annotations:
          kubernetes.io/elb.class: performance
          kubernetes.io/elb.autocreate:
              '{
                  "type": "public",
                  "bandwidth_name": "cce-bandwidth-1626694478577",
                  "bandwidth_chargemode": "bandwidth",
                  "bandwidth_size": 5,
                  "bandwidth_sharetype": "PER",
                  "eip_type": "5_bgp",
                  "available_zone": [
                      ""
                  ],
                  "l4_flavor_name": "L4_flavor.elb.s1.small"
              }'
      spec:
        selector:
          app: nginx
        ports:
        - name: cce-service-0
          targetPort: 80
          nodePort: 0
          port: 80
          protocol: TCP
        type: LoadBalancer

   .. table:: **Table 4** Key parameters

      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Mandatory       | Type                                                          | Description                                                                                                                                                                                                                                                                           |
      +===========================================+=================+===============================================================+=======================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.class                   | Yes             | String                                                        | Select a proper load balancer type as required.                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | The value can be:                                                                                                                                                                                                                                                                     |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | -  **union**: shared load balancer                                                                                                                                                                                                                                                    |
      |                                           |                 |                                                               | -  **performance**: dedicated load balancer, which can be used only in clusters of v1.17 and later.                                                                                                                                                                                   |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.subnet-id               | ``-``           | String                                                        | This parameter indicates the ID of the subnet where the cluster is located. The value can contain 1 to 100 characters.                                                                                                                                                                |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | -  Mandatory when a cluster of v1.11.7-r0 or earlier is to be automatically created.                                                                                                                                                                                                  |
      |                                           |                 |                                                               | -  Optional for clusters later than v1.11.7-r0.                                                                                                                                                                                                                                       |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-option | No              | :ref:`Table 2 <cce_10_0014__table43592047133910>` Object      | Sticky session timeout.                                                                                                                                                                                                                                                               |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.autocreate              | Yes             | :ref:`elb.autocreate <cce_10_0014__table939522754617>` object | Whether to automatically create a load balancer associated with the Service.                                                                                                                                                                                                          |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | **Example:**                                                                                                                                                                                                                                                                          |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | -  Automatically created public network load balancer:                                                                                                                                                                                                                                |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               |    {"type":"public","bandwidth_name":"cce-bandwidth-1551163379627","bandwidth_chargemode":"bandwidth","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}                                                                                               |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | -  Automatically created private network load balancer:                                                                                                                                                                                                                               |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               |    {"type":"inner","name":"A-location-d-test"}                                                                                                                                                                                                                                        |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.lb-algorithm            | No              | String                                                        | This parameter indicates the load balancing algorithm of the backend server group. The default value is **ROUND_ROBIN**.                                                                                                                                                              |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | Options:                                                                                                                                                                                                                                                                              |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | -  **ROUND_ROBIN**: weighted round robin algorithm                                                                                                                                                                                                                                    |
      |                                           |                 |                                                               | -  **LEAST_CONNECTIONS**: weighted least connections algorithm                                                                                                                                                                                                                        |
      |                                           |                 |                                                               | -  **SOURCE_IP**: source IP hash algorithm                                                                                                                                                                                                                                            |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | When the value is **SOURCE_IP**, the weights of backend servers in the server group are invalid.                                                                                                                                                                                      |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.health-check-flag       | No              | String                                                        | Whether to enable the ELB health check.                                                                                                                                                                                                                                               |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | -  Enabling health check: Leave blank this parameter or set it to **on**.                                                                                                                                                                                                             |
      |                                           |                 |                                                               | -  Disabling health check: Set this parameter to **off**.                                                                                                                                                                                                                             |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | If this parameter is enabled, the :ref:`kubernetes.io/elb.health-check-option <cce_10_0014__table236017471397>` field must also be specified at the same time.                                                                                                                        |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.health-check-option     | No              | :ref:`Table 3 <cce_10_0014__table236017471397>` Object        | ELB health check configuration items.                                                                                                                                                                                                                                                 |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-mode   | No              | String                                                        | Listeners ensure session stickiness based on IP addresses. Requests from the same IP address will be forwarded to the same backend server.                                                                                                                                            |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | -  Disabling sticky session: Do not set this parameter.                                                                                                                                                                                                                               |
      |                                           |                 |                                                               | -  Enabling sticky session: Set this parameter to **SOURCE_IP**, indicating that the sticky session is based on the source IP address.                                                                                                                                                |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-option | No              | :ref:`Table 2 <cce_10_0014__table43592047133910>` Object      | Sticky session timeout.                                                                                                                                                                                                                                                               |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/hws-hostNetwork             | No              | String                                                        | This parameter indicates whether the workload Services use the host network. Setting this parameter to **true** will enable the ELB load balancer to forward requests to the host network.                                                                                            |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | The host network is not used by default. The value can be **true** or **false**.                                                                                                                                                                                                      |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | externalTrafficPolicy                     | No              | String                                                        | If sticky session is enabled, add this parameter so that requests are transferred to a fixed node. If a LoadBalancer Service with this parameter set to **Local** is created, a client can access the target backend only if the client is installed on the same node as the backend. |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0014__table939522754617:

   .. table:: **Table 5** Data structure of the elb.autocreate field

      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter            | Mandatory                             | Type             | Description                                                                                                                                                                                                                                                                                                                                                                      |
      +======================+=======================================+==================+==================================================================================================================================================================================================================================================================================================================================================================================+
      | name                 | No                                    | String           | Name of the load balancer that is automatically created.                                                                                                                                                                                                                                                                                                                         |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | Value range: 1 to 64 characters, including lowercase letters, digits, and underscores (_). The value must start with a lowercase letter and end with a lowercase letter or digit.                                                                                                                                                                                                |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | Default: **cce-lb+service.UID**                                                                                                                                                                                                                                                                                                                                                  |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | type                 | No                                    | String           | Network type of the load balancer.                                                                                                                                                                                                                                                                                                                                               |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | -  **public**: public network load balancer                                                                                                                                                                                                                                                                                                                                      |
      |                      |                                       |                  | -  **inner**: private network load balancer                                                                                                                                                                                                                                                                                                                                      |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | Default: **inner**                                                                                                                                                                                                                                                                                                                                                               |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_name       | Yes for public network load balancers | String           | Bandwidth name. The default value is **cce-bandwidth-*****\***.                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | Value range: 1 to 64 characters, including lowercase letters, digits, and underscores (_). The value must start with a lowercase letter and end with a lowercase letter or digit.                                                                                                                                                                                                |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_chargemode | No                                    | String           | Bandwidth mode.                                                                                                                                                                                                                                                                                                                                                                  |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_size       | Yes for public network load balancers | Integer          | Bandwidth size. The default value is 1 to 2000 Mbit/s. Set this parameter based on the bandwidth range allowed in your region.                                                                                                                                                                                                                                                   |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_sharetype  | Yes for public network load balancers | String           | Bandwidth sharing mode.                                                                                                                                                                                                                                                                                                                                                          |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | -  **PER**: dedicated bandwidth                                                                                                                                                                                                                                                                                                                                                  |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | eip_type             | Yes for public network load balancers | String           | EIP type.                                                                                                                                                                                                                                                                                                                                                                        |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | -  **5_bgp**: dynamic BGP                                                                                                                                                                                                                                                                                                                                                        |
      |                      |                                       |                  | -  **5_sbgp**: static BGP                                                                                                                                                                                                                                                                                                                                                        |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | available_zone       | Yes                                   | Array of strings | AZ where the load balancer is located.                                                                                                                                                                                                                                                                                                                                           |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                                                   |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | l4_flavor_name       | Yes                                   | String           | Flavor name of the layer-4 load balancer.                                                                                                                                                                                                                                                                                                                                        |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                                                   |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | l7_flavor_name       | No                                    | String           | Flavor name of the layer-7 load balancer.                                                                                                                                                                                                                                                                                                                                        |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                                                   |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | elb_virsubnet_ids    | No                                    | Array of strings | Subnet where the backend server of the load balancer is located. If this parameter is left blank, the default cluster subnet is used. Load balancers occupy different number of subnet IP addresses based on their specifications. Therefore, you are not advised to use the subnet CIDR blocks of other resources (such as clusters and nodes) as the load balancer CIDR block. |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                                                   |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | Example:                                                                                                                                                                                                                                                                                                                                                                         |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | .. code-block::                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  |    "elb_virsubnet_ids": [                                                                                                                                                                                                                                                                                                                                                        |
      |                      |                                       |                  |       "14567f27-8ae4-42b8-ae47-9f847a4690dd"                                                                                                                                                                                                                                                                                                                                     |
      |                      |                                       |                  |     ]                                                                                                                                                                                                                                                                                                                                                                            |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create a workload.

   **kubectl create -f nginx-deployment.yaml**

   If information similar to the following is displayed, the workload is being created.

   .. code-block::

      deployment/nginx created

   **kubectl get po**

   If information similar to the following is displayed, the workload is running.

   .. code-block::

      NAME                     READY     STATUS             RESTARTS   AGE
      nginx-2601814895-c1xhw   1/1       Running            0          6s

#. Create a Service.

   **kubectl create -f nginx-elb-svc.yaml**

   If information similar to the following is displayed, the Service has been created.

   .. code-block::

      service/nginx created

   **kubectl get svc**

   If information similar to the following is displayed, the access type has been set successfully, and the workload is accessible.

   .. code-block::

      NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
      kubernetes   ClusterIP      10.247.0.1       <none>        443/TCP        3d
      nginx        LoadBalancer   10.247.130.196   10.78.42.242   80:31540/TCP   51s

#. Enter the URL in the address box of the browser, for example, **10.78.42.242:80**. **10.78.42.242** indicates the IP address of the load balancer, and **80** indicates the access port displayed on the CCE console.

   The Nginx is accessible.


   .. figure:: /_static/images/en-us_image_0000001199021334.png
      :alt: **Figure 4** Accessing Nginx through the LoadBalancer Service

      **Figure 4** Accessing Nginx through the LoadBalancer Service

ELB Forwarding
--------------

After a Service of the LoadBalancer type is created, you can view the listener forwarding rules of the load balancer on the ELB console.

You can find that a listener is created for the load balancer. Its backend server is the node where the pod is located, and the backend server port is the NodePort (node port) of the Service. When traffic passes through ELB, it is forwarded to *IP address of the node where the pod is located:Node port*. That is, the Service is accessed and then the pod is accessed, which is the same as that described in :ref:`Scenario <cce_10_0014__section19854101411508>`.

In the passthrough networking scenario (CCE Turbo + dedicated load balancer), after a LoadBalancer Service is created, you can view the listener forwarding rules of the load balancer on the ELB console.

You can see that a listener is created for the load balancer. The backend server address is the IP address of the pod, and the service port is the container port. This is because the pod uses an ENI or sub-ENI. When traffic passes through the load balancer, it directly forwards the traffic to the pod. This is the same as that described in :ref:`Scenario <cce_10_0014__section19854101411508>`.

.. _cce_10_0014__section52631714117:

Why a Cluster Fails to Access Services by Using the ELB Address
---------------------------------------------------------------

If the service affinity of a LoadBalancer Service is set to the node level, that is, the value of **externalTrafficPolicy** is **Local**, the ELB address may fail to be accessed from the cluster (specifically, nodes or containers). Information similar to the following is displayed:

.. code-block::

   upstream connect error or disconnect/reset before headers. reset reason: connection failure

This is because when the LoadBalancer Service is created, kube-proxy adds the ELB access address as the external IP to iptables or IPVS. If a client initiates a request to access the ELB address from inside the cluster, the address is considered as the external IP address of the service and is directly forwarded by kube-proxy without passing through the ELB outside the cluster.

When the value of **externalTrafficPolicy** is **Local**, the situation varies according to the container network model and service forwarding mode. The details are as follows:

+---------------------------------------------------------------------------+-----------------------------+---------------------------------------------------------------------+-------------------------------------------------------+------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------+
| Server                                                                    | Client                      | Tunnel Network Cluster (IPVS)                                       | VPC Network Cluster (IPVS)                            | Tunnel Network Cluster (iptables)                                                                    | VPC Network Cluster (iptables)                                                                       |
+---------------------------------------------------------------------------+-----------------------------+---------------------------------------------------------------------+-------------------------------------------------------+------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------+
| NodePort Service                                                          | Same node                   | OK. The node where the pod runs is accessible, not any other nodes. | OK. The node where the pod runs is accessible.        | OK. The node where the pod runs is accessible.                                                       | OK. The node where the pod runs is accessible.                                                       |
+---------------------------------------------------------------------------+-----------------------------+---------------------------------------------------------------------+-------------------------------------------------------+------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------+
|                                                                           | Cross-node                  | OK. The node where the pod runs is accessible, not any other nodes. | OK. The node where the pod runs is accessible.        | OK. The node where the pod runs is accessible by visiting the node IP + port, not by any other ways. | OK. The node where the pod runs is accessible by visiting the node IP + port, not by any other ways. |
+---------------------------------------------------------------------------+-----------------------------+---------------------------------------------------------------------+-------------------------------------------------------+------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------+
|                                                                           | Containers on the same node | OK. The node where the pod runs is accessible, not any other nodes. | OK. The node where the pod runs is not accessible.    | OK. The node where the pod runs is accessible.                                                       | OK. The node where the pod runs is not accessible.                                                   |
+---------------------------------------------------------------------------+-----------------------------+---------------------------------------------------------------------+-------------------------------------------------------+------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------+
|                                                                           | Containers across nodes     | OK. The node where the pod runs is accessible, not any other nodes. | OK. The node where the pod runs is accessible.        | OK. The node where the pod runs is accessible.                                                       | OK. The node where the pod runs is accessible.                                                       |
+---------------------------------------------------------------------------+-----------------------------+---------------------------------------------------------------------+-------------------------------------------------------+------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------+
| LoadBalancer Service using a dedicated load balancer                      | Same node                   | Accessible for public networks, not private networks.               | Accessible for public networks, not private networks. | Accessible for public networks, not private networks.                                                | Accessible for public networks, not private networks.                                                |
+---------------------------------------------------------------------------+-----------------------------+---------------------------------------------------------------------+-------------------------------------------------------+------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------+
|                                                                           | Containers on the same node | Accessible for public networks, not private networks.               | Accessible for public networks, not private networks. | Accessible for public networks, not private networks.                                                | Accessible for public networks, not private networks.                                                |
+---------------------------------------------------------------------------+-----------------------------+---------------------------------------------------------------------+-------------------------------------------------------+------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------+
| Local Service of the nginx-ingress add-on using a dedicated load balancer | Same node                   | Accessible for public networks, not private networks.               | Accessible for public networks, not private networks. | Accessible for public networks, not private networks.                                                | Accessible for public networks, not private networks.                                                |
+---------------------------------------------------------------------------+-----------------------------+---------------------------------------------------------------------+-------------------------------------------------------+------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------+
|                                                                           | Containers on the same node | Accessible for public networks, not private networks.               | Accessible for public networks, not private networks. | Accessible for public networks, not private networks.                                                | Accessible for public networks, not private networks.                                                |
+---------------------------------------------------------------------------+-----------------------------+---------------------------------------------------------------------+-------------------------------------------------------+------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------+

The following methods can be used to solve this problem:

-  (**Recommended**) In the cluster, use the ClusterIP Service or service domain name for access.

-  Set **externalTrafficPolicy** of the Service to **Cluster**, which means cluster-level service affinity. Note that this affects source address persistence.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/elb.autocreate: '{"type":"public","bandwidth_name":"cce-bandwidth","bandwidth_chargemode":"bandwidth","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}'
        labels:
          app: nginx
        name: nginx
      spec:
        externalTrafficPolicy: Cluster
        ports:
        - name: service0
          port: 80
          protocol: TCP
          targetPort: 80
        selector:
          app: nginx
        type: LoadBalancer
