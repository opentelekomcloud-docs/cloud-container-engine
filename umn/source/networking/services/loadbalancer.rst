:original_name: cce_01_0014.html

.. _cce_01_0014:

LoadBalancer
============

Scenario
--------

A workload can be accessed from public networks through a load balancer, which is more secure and reliable than EIP.

The LoadBalancer access address is in the format of <IP address of public network load balancer>:<access port>, for example, **10.117.117.117:80**.

In this access mode, requests are transmitted through an ELB load balancer to a node and then forwarded to the destination pod through the Service.


.. figure:: /_static/images/en-us_image_0000001163928763.png
   :alt: **Figure 1** LoadBalancer

   **Figure 1** LoadBalancer

Notes and Constraints
---------------------

-  LoadBalancer Services allow workloads to be accessed from public networks through **ELB**. This access mode has the following restrictions:

   -  It is recommended that automatically created load balancers not be used by other resources. Otherwise, these load balancers cannot be completely deleted, causing residual resources.
   -  Do not change the listener name for the load balancer in clusters of v1.15 and earlier. Otherwise, the load balancer cannot be accessed.

-  After a Service is created, if the :ref:`affinity setting <cce_01_0014__li36098269511>` is switched from the cluster level to the node level, the connection tracing table will not be cleared. You are advised not to modify the Service affinity setting after the Service is created. If you need to modify it, create a Service again.
-  If the service affinity is set to the node level (that is, **externalTrafficPolicy** is set to **Local**), the cluster may fail to access the Service by using the ELB address. For details, see :ref:`Why a Cluster Fails to Access Services by Using the ELB Address <cce_01_0014__section52631714117>`.
-  CCE Turbo clusters support only cluster-level service affinity.
-  Dedicated ELB load balancers can be used only in clusters of v1.17 and later.
-  The specifications of dedicated load balancers must use TCP/UDP (network load balancing) and support private networks. If the Service needs to support HTTP, the specifications of dedicated load balancers must use HTTP (application load balancing) in addition to TCP/UDP (network load balancing).
-  If you create a LoadBalancer Service on the CCE console, a random node port is automatically generated. If you use kubectl to create a LoadBalancer Service, a random node port is generated unless you specify one.
-  In a CCE cluster, if the cluster-level affinity is configured for a LoadBalancer Service, requests are distributed to the node ports of each node using SNAT when entering the cluster. The number of node ports cannot exceed the number of available node ports on the node. If the Service affinity is at the node level (local), there is no such constraint. In a CCE Turbo cluster, this constraint applies to shared ELB load balancers, but not dedicated ones. You are advised to use dedicated ELB load balancers in CCE Turbo clusters.
-  When the cluster service forwarding (proxy) mode is IPVS, the node IP cannot be configured as the external IP of the Service. Otherwise, the node is unavailable.
-  Dedicated load balancers are available only in certain regions.

.. _cce_01_0014__section744117150366:

Adding a Service When Creating a Workload
-----------------------------------------

You can set the Service when creating a workload on the CCE console. An Nginx workload is used as an example.

#. In the **Set Application Access** step of :ref:`Creating a Deployment <cce_01_0047>`, :ref:`Creating a StatefulSet <cce_01_0048>`, or :ref:`Creating a DaemonSet <cce_01_0216>`, click **Add Service** and set the following parameters:

   -  **Access Type**: Select **LoadBalancer (ELB)**.

   -  **Service Name**: Specify a Service name, which can be the same as the workload name.

   -  .. _cce_01_0014__li36098269511:

      **Service Affinity:**

      -  Cluster level: The IP addresses and access ports of all nodes in a cluster can access the workload associated with the Service. Service access will cause performance loss due to route redirection, and the source IP address of the client cannot be obtained.
      -  Node level: Only the IP address and access port of the node where the workload is located can access the workload associated with the Service. Service access will not cause performance loss due to route redirection, and the source IP address of the client can be obtained.

   **ELB Configuration**

   -  **Elastic Load Balancer**: A load balancer automatically distributes Internet access traffic to multiple nodes where the workload is located.

      -  **Shared**: Shared load balancers provide domain name- and URL-based route balancing. Resources are shared among load balancers, and the performance of a load balancer is affected by other load balancers.
      -  **Dedicated**: Resources are exclusively used by a load balancer, and the performance of a load balancer is not affected by other load balancers. IPv6 is supported.

         -  **AZ**: Dedicated load balancers can be deployed across AZs to provide higher reliability.

         -  **Subnet**: subnet where the backend server of the load balancer is located.

            Load balancers occupy different number of subnet IP addresses based on their specifications. Therefore, you are not advised to use the subnet CIDR blocks of other resources (such as clusters and nodes) as the load balancer subnet CIDR block.

         -  **Specifications**: Specifications determine the types of listeners that can be added to a load balancer. Select specifications that best fit your needs.

      You can create **public network** or **private network** load balancers.

      -  **Public network**: You can select an existing public network load balancer or have the system automatically create a new one.
      -  **Private network**: You can select an existing private network load balancer or have the system automatically create a new private network load balancer.

      The selected or created load balancer must be in the same VPC as the current cluster, and it must match the load balancer type (private or public network).

      -  **Enterprise Project**: Select an enterprise project in which the load balancer is created.
      -  **Specifications**: This field is displayed only when you select **Public network** and **Automatically created** for **Elastic Load Balancer**. You can click |image1| to modify the name, specifications, billing mode, and bandwidth of the load balancer.
      -  **Algorithm Type**: Three algorithms are available: weighted round robin, weighted least connections algorithm, or source IP hash.

         .. note::

            -  **Weighted round robin**: Requests are forwarded to different servers based on their weights, which indicate server processing performance. Backend servers with higher weights receive proportionately more requests, whereas equal-weighted servers receive the same number of requests. This algorithm is often used for short connections, such as HTTP services.
            -  **Weighted least connections**: In addition to the weight assigned to each server, the number of connections processed by each backend server is also considered. Requests are forwarded to the server with the lowest connections-to-weight ratio. Building on **least connections**, the **weighted least connections** algorithm assigns a weight to each server based on their processing capability. This algorithm is often used for persistent connections, such as database connections.
            -  **Source IP hash**: The source IP address of each request is calculated using the hash algorithm to obtain a unique hash key, and all backend servers are numbered. The generated key allocates the client to a particular server. This enables requests from different clients to be distributed in load balancing mode and ensures that requests from the same client are forwarded to the same server. This algorithm applies to TCP connections without cookies.

      -  **Sticky Session**: This function is disabled by default. You can select **Based on source IP address**. Listeners ensure session stickiness based on IP addresses. Requests from the same IP address will be forwarded to the same backend server.
      -  **Health Check**: This function is enabled by default. Enabling it will perform health checks on your load balancer. By default, the Service ports (Node Port and container port of the Service) is used for health check. You can also specify another port for health check. After the port is specified, a Service port (name: **cce-healthz**; protocol: **TCP**) will be added for the Service.

   -  **Port Settings**

      -  **Protocol**: protocol used by the Service.
      -  **Container Port**: port defined in the container image and on which the workload listens. The Nginx application listens on port 80.
      -  **Access Port**: port mapped to the container port at the load balancer's IP address. The workload can be accessed at <*Load balancer's IP address*>:<*Access port*>. The port number range is 1-65535.

#. After the configuration is complete, click **OK**.

#. On the workload creation page, click **Next: Configure Advanced Settings**. On the page displayed, click **Create**.

#. After the workload is successfully created, choose **Workloads** > **Deployments** or **Workloads** > **StatefulSets** on the CCE console. Click the name of the workload to view its details. On the workload details page, click the **Services** tab and obtain the access address.

#. Click the access address.

Adding a Service After Creating a Workload
------------------------------------------

You can set the Service after creating a workload. This has no impact on the workload status and takes effect immediately. The procedure is as follows:

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Network**.

#. On the **Services** tab page, click **Create Service**.

   The parameters are the same as those in :ref:`Adding a Service When Creating a Workload <cce_01_0014__section744117150366>`.

#. Click **Create**.

.. _cce_01_0014__section1984211714368:

Using kubectl to Create a Service (Using an Existing Load Balancer)
-------------------------------------------------------------------

You can set the access type when creating a workload using kubectl. This section uses an Nginx workload as an example to describe how to add a LoadBalancer Service using kubectl.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

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
      -  Anti-affinity has been configured between pods of the workload. That is, all pods of the workload are deployed on different nodes. For details, see :ref:`Workload-Node Anti-Affinity <cce_01_0226>`.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.id: 3c7caa5a-a641-4bff-801a-feace27424b6          # Load balancer ID. Replace it with the actual value.
          kubernetes.io/elb.class: performance                               # Load balancer type
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

   .. table:: **Table 1** Key parameters

      +-------------------------------------------+-----------------+----------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Mandatory       | Type                                                     | Description                                                                                                                                                                                             |
      +===========================================+=================+==========================================================+=========================================================================================================================================================================================================+
      | kubernetes.io/elb.class                   | No              | String                                                   | Select a proper load balancer type as required.                                                                                                                                                         |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | The value can be:                                                                                                                                                                                       |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | -  **union**: shared load balancer                                                                                                                                                                      |
      |                                           |                 |                                                          | -  **performance**: dedicated load balancer, which can be used only in clusters of v1.17 and later.                                                                                                     |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | Default value: **union**                                                                                                                                                                                |
      +-------------------------------------------+-----------------+----------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-mode   | No              | String                                                   | Listeners ensure session stickiness based on IP addresses. Requests from the same IP address will be forwarded to the same backend server.                                                              |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | -  Disabling sticky session: Do not set this parameter.                                                                                                                                                 |
      |                                           |                 |                                                          | -  Enabling sticky session: Set this parameter to **SOURCE_IP**, indicating that the sticky session is based on the source IP address.                                                                  |
      +-------------------------------------------+-----------------+----------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-option | No              | :ref:`Table 2 <cce_01_0014__table43592047133910>` Object | This parameter specifies the sticky session timeout.                                                                                                                                                    |
      +-------------------------------------------+-----------------+----------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.id                      | Yes             | String                                                   | This parameter indicates the ID of a load balancer. The value can contain 1 to 100 characters.                                                                                                          |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | Mandatory when an existing load balancer is to be associated.                                                                                                                                           |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | **Obtaining the load balancer ID:**                                                                                                                                                                     |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | On the management console, click **Service List**, and choose **Networking** > **Elastic Load Balance**. Click the name of the target load balancer. On the **Summary** tab page, find and copy the ID. |
      +-------------------------------------------+-----------------+----------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.subnet-id               | -               | String                                                   | This parameter indicates the ID of the subnet where the cluster is located. The value can contain 1 to 100 characters.                                                                                  |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | -  Mandatory when a cluster of v1.11.7-r0 or earlier is to be automatically created.                                                                                                                    |
      |                                           |                 |                                                          | -  Optional for clusters later than v1.11.7-r0.                                                                                                                                                         |
      +-------------------------------------------+-----------------+----------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.lb-algorithm            | No              | String                                                   | This parameter indicates the load balancing algorithm of the backend server group. The default value is **ROUND_ROBIN**.                                                                                |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | Options:                                                                                                                                                                                                |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | -  **ROUND_ROBIN**: weighted round robin algorithm                                                                                                                                                      |
      |                                           |                 |                                                          | -  **LEAST_CONNECTIONS**: weighted least connections algorithm                                                                                                                                          |
      |                                           |                 |                                                          | -  **SOURCE_IP**: source IP hash algorithm                                                                                                                                                              |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | When the value is **SOURCE_IP**, the weights of backend servers in the server group are invalid.                                                                                                        |
      +-------------------------------------------+-----------------+----------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.health-check-flag       | No              | String                                                   | Whether to enable the ELB health check.                                                                                                                                                                 |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | Enabled by default.                                                                                                                                                                                     |
      |                                           |                 |                                                          |                                                                                                                                                                                                         |
      |                                           |                 |                                                          | -  Enabling health check: Leave blank this parameter or set it to **on**.                                                                                                                               |
      |                                           |                 |                                                          | -  Disabling health check: Set this parameter to **off**.                                                                                                                                               |
      +-------------------------------------------+-----------------+----------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.health-check-option     | No              | :ref:`Table 3 <cce_01_0014__table236017471397>` Object   | ELB health check configuration items.                                                                                                                                                                   |
      +-------------------------------------------+-----------------+----------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | port                                      | Yes             | Integer                                                  | Access port that is registered on the load balancer and mapped to the cluster-internal IP address.                                                                                                      |
      +-------------------------------------------+-----------------+----------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | targetPort                                | Yes             | String                                                   | Container port set on the CCE console.                                                                                                                                                                  |
      +-------------------------------------------+-----------------+----------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_01_0014__table43592047133910:

   .. table:: **Table 2** Data structure of the elb.session-affinity-option field

      +---------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------+
      | Parameter           | Mandatory       | Type            | Description                                                                                                                  |
      +=====================+=================+=================+==============================================================================================================================+
      | persistence_timeout | Yes             | String          | Sticky session timeout, in minutes. This parameter is valid only when **elb.session-affinity-mode** is set to **SOURCE_IP**. |
      |                     |                 |                 |                                                                                                                              |
      |                     |                 |                 | Value range: 1 to 60. Default value: **60**                                                                                  |
      +---------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_01_0014__table236017471397:

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
      |                 |                 |                 | Value options: TCP, UDP_CONNECT, or HTTP                                           |
      +-----------------+-----------------+-----------------+------------------------------------------------------------------------------------+
      | path            | No              | String          | Health check URL. This parameter needs to be configured when the protocol is HTTP. |
      |                 |                 |                 |                                                                                    |
      |                 |                 |                 | Default value: **/**                                                               |
      |                 |                 |                 |                                                                                    |
      |                 |                 |                 | The value contains 1 to 10,000 characters.                                         |
      +-----------------+-----------------+-----------------+------------------------------------------------------------------------------------+

#. Create a workload.

   **kubectl create -f nginx-deployment.yaml**

   If information similar to the following is displayed, the workload has been created.

   .. code-block::

      deployment "nginx" created

   **kubectl get pod**

   If information similar to the following is displayed, the workload is running.

   .. code-block::

      NAME                     READY     STATUS             RESTARTS   AGE
      nginx-2601814895-c1xwh   1/1       Running            0          6s

#. Create a Service.

   **kubectl create -f nginx-elb-svc.yaml**

   If information similar to the following is displayed, the Service has been created.

   .. code-block::

      service "nginx" created

   **kubectl get svc**

   If information similar to the following is displayed, the access type has been set successfully, and the workload is accessible.

   .. code-block::

      NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
      kubernetes   ClusterIP      10.247.0.1       <none>        443/TCP        3d
      nginx        LoadBalancer   10.247.130.196   10.78.42.242   80:31540/TCP   51s

#. Enter the URL in the address box of the browser, for example, **10.78.42.242:80**. **10.78.42.242** indicates the IP address of the load balancer, and **80** indicates the access port displayed on the CCE console.

   The Nginx is accessible.


   .. figure:: /_static/images/en-us_image_0276664171.png
      :alt: **Figure 2** Accessing Nginx through the LoadBalancer Service

      **Figure 2** Accessing Nginx through the LoadBalancer Service

Using kubectl to Create a Service (Automatically Creating a Load Balancer)
--------------------------------------------------------------------------

You can add a Service when creating a workload using kubectl. This section uses an Nginx workload as an example to describe how to add a LoadBalancer Service using kubectl.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

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
      -  Anti-affinity has been configured between pods of the workload. That is, all pods of the workload are deployed on different nodes. For details, see :ref:`Workload-Node Anti-Affinity <cce_01_0226>`.

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
                  "bandwidth_chargemode":"traffic",
                  "bandwidth_size": 5,
                  "bandwidth_sharetype": "PER",
                  "eip_type": "5_bgp",
                  "name": "james"
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

   Example of a Service using a dedicated, public network load balancer:

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
                  "bandwidth_chargemode": "traffic",
                  "bandwidth_size": 5,
                  "bandwidth_sharetype": "PER",
                  "eip_type": "5_bgp",
                  "available_zone": [
                      "eu-de-01"
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

   .. _cce_01_0014__table133089105019:

   .. table:: **Table 4** Key parameters

      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Mandatory       | Type                                                          | Description                                                                                                                                                                                                                                                                           |
      +===========================================+=================+===============================================================+=======================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.class                   | No              | String                                                        | Select a proper load balancer type as required.                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | The value can be:                                                                                                                                                                                                                                                                     |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | -  **union**: shared load balancer                                                                                                                                                                                                                                                    |
      |                                           |                 |                                                               | -  **performance**: dedicated load balancer, which can be used only in clusters of v1.17 and later.                                                                                                                                                                                   |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | Default value: **union**                                                                                                                                                                                                                                                              |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.subnet-id               | -               | String                                                        | This parameter indicates the ID of the subnet where the cluster is located. The value can contain 1 to 100 characters.                                                                                                                                                                |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | -  Mandatory when a cluster of v1.11.7-r0 or earlier is to be automatically created.                                                                                                                                                                                                  |
      |                                           |                 |                                                               | -  Optional for clusters later than v1.11.7-r0.                                                                                                                                                                                                                                       |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.enterpriseID            | No              | String                                                        | **Clusters of v1.15 and later versions support this field. In clusters earlier than v1.15, load balancers are created in the default project by default.**                                                                                                                            |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | This parameter indicates the ID of the enterprise project in which the ELB load balancer will be created.                                                                                                                                                                             |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | If this parameter is not specified or is set to **0**, resources will be bound to the default enterprise project.                                                                                                                                                                     |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | **How to obtain**:                                                                                                                                                                                                                                                                    |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | Log in to the management console and choose **Enterprise** > **Project Management** on the top menu bar. In the list displayed, click the name of the target enterprise project, and copy the ID on the enterprise project details page.                                              |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-option | No              | :ref:`Table 2 <cce_01_0014__table43592047133910>` Object      | Sticky session timeout.                                                                                                                                                                                                                                                               |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.autocreate              | Yes             | :ref:`elb.autocreate <cce_01_0014__table939522754617>` object | Whether to automatically create a load balancer associated with the Service.                                                                                                                                                                                                          |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | **Example:**                                                                                                                                                                                                                                                                          |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | -  Automatically created public network load balancer:                                                                                                                                                                                                                                |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               |    {"type":"public","bandwidth_name":"cce-bandwidth-1551163379627","bandwidth_chargemode":"traffic","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}                                                                                                 |
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
      |                                           |                 |                                                               | Disabled by default.                                                                                                                                                                                                                                                                  |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | -  Enabling health check: Leave blank this parameter or set it to **on**.                                                                                                                                                                                                             |
      |                                           |                 |                                                               | -  Disabling health check: Set this parameter to **off**.                                                                                                                                                                                                                             |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.health-check-option     | No              | :ref:`Table 3 <cce_01_0014__table236017471397>` Object        | ELB health check configuration items.                                                                                                                                                                                                                                                 |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-mode   | No              | String                                                        | Listeners ensure session stickiness based on IP addresses. Requests from the same IP address will be forwarded to the same backend server.                                                                                                                                            |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | -  Disabling sticky session: Do not set this parameter.                                                                                                                                                                                                                               |
      |                                           |                 |                                                               | -  Enabling sticky session: Set this parameter to **SOURCE_IP**, indicating that the sticky session is based on the source IP address.                                                                                                                                                |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-option | No              | :ref:`Table 2 <cce_01_0014__table43592047133910>` Object      | Sticky session timeout.                                                                                                                                                                                                                                                               |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/hws-hostNetwork             | No              | String                                                        | This parameter indicates whether the workload Services use the host network. Setting this parameter to **true** will enable the load balancer to forward requests to the host network.                                                                                                |
      |                                           |                 |                                                               |                                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                               | The host network is not used by default. The value can be **true** or **false**.                                                                                                                                                                                                      |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | externalTrafficPolicy                     | No              | String                                                        | If sticky session is enabled, add this parameter so that requests are transferred to a fixed node. If a LoadBalancer Service with this parameter set to **Local** is created, a client can access the target backend only if the client is installed on the same node as the backend. |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_01_0014__table939522754617:

   .. table:: **Table 5** Data structure of the elb.autocreate field

      +----------------------+---------------------------------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter            | Mandatory                             | Type             | Description                                                                                                                                                                                                                                |
      +======================+=======================================+==================+============================================================================================================================================================================================================================================+
      | name                 | No                                    | String           | Name of the automatically created load balancer.                                                                                                                                                                                           |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | Value range: a string of 1 to 64 characters, including lowercase letters, digits, and underscores (_). The value must start with a lowercase letter and end with a lowercase letter or digit.                                              |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | Default name: **cce-lb+service.UID**                                                                                                                                                                                                       |
      +----------------------+---------------------------------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | type                 | No                                    | String           | Network type of the load balancer.                                                                                                                                                                                                         |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | -  **public**: public network load balancer                                                                                                                                                                                                |
      |                      |                                       |                  | -  **inner**: private network load balancer                                                                                                                                                                                                |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | Default value: **inner**                                                                                                                                                                                                                   |
      +----------------------+---------------------------------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_name       | Yes for public network load balancers | String           | Bandwidth name. The default value is **cce-bandwidth-*****\***.                                                                                                                                                                            |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | Value range: a string of 1 to 64 characters, including lowercase letters, digits, and underscores (_). The value must start with a lowercase letter and end with a lowercase letter or digit.                                              |
      +----------------------+---------------------------------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_chargemode | No                                    | String           | Bandwidth billing mode.                                                                                                                                                                                                                    |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | -  **traffic**: billed by traffic                                                                                                                                                                                                          |
      +----------------------+---------------------------------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_size       | Yes for public network load balancers | Integer          | Bandwidth size. The default value is 1 to 2000 Mbit/s. Set this parameter based on the bandwidth range allowed in your region.                                                                                                             |
      +----------------------+---------------------------------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_sharetype  | Yes for public network load balancers | String           | Bandwidth sharing mode.                                                                                                                                                                                                                    |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | -  **PER**: dedicated bandwidth                                                                                                                                                                                                            |
      +----------------------+---------------------------------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | eip_type             | Yes for public network load balancers | String           | EIP type, which may vary depending on sites. For details, see the type parameter specified when `creating an EIP <https://docs.otc.t-systems.com/api/eip/eip_api_0001.html#eip_api_0001__en-us_topic_0201534274_table44471219>`__.         |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | -  **5_bgp**: dynamic BGP                                                                                                                                                                                                                  |
      |                      |                                       |                  | -  **5_gray**: dedicated load balancer                                                                                                                                                                                                     |
      +----------------------+---------------------------------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | available_zone       | Yes                                   | Array of strings | AZ where the load balancer is located.                                                                                                                                                                                                     |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                             |
      +----------------------+---------------------------------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | l4_flavor_name       | Yes                                   | String           | Flavor name of the layer-4 load balancer.                                                                                                                                                                                                  |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                             |
      +----------------------+---------------------------------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | l7_flavor_name       | No                                    | String           | Flavor name of the layer-7 load balancer.                                                                                                                                                                                                  |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                             |
      +----------------------+---------------------------------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | elb_virsubnet_ids    | No                                    | Array of strings | Subnet where the backend server of the load balancer is located. If this parameter is left blank, the default cluster subnet is used.                                                                                                      |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | Load balancers occupy different number of subnet IP addresses based on their specifications. Therefore, you are not advised to use the subnet CIDR blocks of other resources (such as clusters and nodes) as the load balancer CIDR block. |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                             |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | Example:                                                                                                                                                                                                                                   |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  | .. code-block::                                                                                                                                                                                                                            |
      |                      |                                       |                  |                                                                                                                                                                                                                                            |
      |                      |                                       |                  |    "elb_virsubnet_ids": [                                                                                                                                                                                                                  |
      |                      |                                       |                  |       "14567f27-8ae4-42b8-ae47-9f847a4690dd"                                                                                                                                                                                               |
      |                      |                                       |                  |     ]                                                                                                                                                                                                                                      |
      +----------------------+---------------------------------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create a workload.

   **kubectl create -f nginx-deployment.yaml**

   If information similar to the following is displayed, the workload is being created.

   .. code-block::

      deployment "nginx" created

   **kubectl get po**

   If information similar to the following is displayed, the workload is running.

   .. code-block::

      NAME                     READY     STATUS             RESTARTS   AGE
      nginx-2601814895-c1xwh   1/1       Running            0          6s

#. Create a Service.

   **kubectl create -f nginx-elb-svc.yaml**

   If information similar to the following is displayed, the Service has been created.

   .. code-block::

      service "nginx" created

   **kubectl get svc**

   If information similar to the following is displayed, the access type has been set successfully, and the workload is accessible.

   .. code-block::

      NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
      kubernetes   ClusterIP      10.247.0.1       <none>        443/TCP        3d
      nginx        LoadBalancer   10.247.130.196   10.78.42.242   80:31540/TCP   51s

#. Enter the URL in the address box of the browser, for example, **10.78.42.242:80**. **10.78.42.242** indicates the IP address of the load balancer, and **80** indicates the access port displayed on the CCE console.

   The Nginx is accessible.


   .. figure:: /_static/images/en-us_image_0000001093275701.png
      :alt: **Figure 3** Accessing Nginx through the LoadBalancer Service

      **Figure 3** Accessing Nginx through the LoadBalancer Service

.. _cce_01_0014__section52631714117:

Why a Cluster Fails to Access Services by Using the ELB Address
---------------------------------------------------------------

If the service affinity of a LoadBalancer Service is set to the node level, that is, the value of **externalTrafficPolicy** is **Local**, the ELB address may fail to be accessed from the cluster (specifically, nodes or containers).

This is because when the LoadBalancer Service is created, kube-proxy adds the ELB access address (external-ip) to iptables or IPVS. When the ELB address is accessed from the cluster, the ELB load balancer is not used. Instead, kube-proxy directly forwards the access request. The case depends on which container network model and service forwarding mode you use.

The following methods can be used to solve this problem:

-  (**Recommended**) In the cluster, use the ClusterIP Service or service domain name for access.

-  Set **externalTrafficPolicy** of the Service to **Cluster**, which means cluster-level service affinity. Note that this affects source address persistence.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/elb.autocreate: '{"type":"public","bandwidth_name":"cce-bandwidth","bandwidth_chargemode":"traffic","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}'
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

.. |image1| image:: /_static/images/en-us_image_0298565473.png
