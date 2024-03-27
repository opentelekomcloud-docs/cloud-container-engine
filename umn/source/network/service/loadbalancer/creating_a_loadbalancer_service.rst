:original_name: cce_10_0681.html

.. _cce_10_0681:

Creating a LoadBalancer Service
===============================

Scenario
--------

LoadBalancer Services can access workloads from the public network through ELB, which is more reliable than EIP-based access. The LoadBalancer access address is in the format of *IP address of public network load balancer*:*Access port*, for example, **10.117.117.117:80**.

In this access mode, requests are transmitted through an ELB load balancer to a node and then forwarded to the destination pod through the Service.


.. figure:: /_static/images/en-us_image_0000001695736989.png
   :alt: **Figure 1** LoadBalancer

   **Figure 1** LoadBalancer

When **CCE Turbo clusters and dedicated load balancers** are used, passthrough networking is supported to reduce service latency and ensure zero performance loss.

External access requests are directly forwarded from a load balancer to pods. Internal access requests can be forwarded to a pod through a Service.


.. figure:: /_static/images/en-us_image_0000001647417328.png
   :alt: **Figure 2** Passthrough networking

   **Figure 2** Passthrough networking

Constraints
-----------

-  LoadBalancer Services allow workloads to be accessed from public networks through ELB. This access mode has the following restrictions:

   -  Automatically created load balancers should not be used by other resources. Otherwise, these load balancers cannot be completely deleted.
   -  Do not change the listener name for the load balancer in clusters of v1.15 and earlier. Otherwise, the load balancer cannot be accessed.

-  After a Service is created, if the affinity setting is switched from the cluster level to the node level, the connection tracing table will not be cleared. You are advised not to modify the Service affinity setting after the Service is created. To modify it, create a Service again.
-  If the service affinity is set to the node level (that is, :ref:`externalTrafficPolicy <cce_10_0249__section18134208069>` is set to **Local**), the cluster may fail to access the Service by using the ELB address. For details, see :ref:`Why a Service Fail to Be Accessed from Within the Cluster <cce_10_0249__section52631714117>`.
-  CCE Turbo clusters support only cluster-level service affinity.
-  Dedicated ELB load balancers can be used only in clusters of v1.17 and later.
-  Dedicated load balancers must be of the network type (TCP/UDP) supporting private networks (with a private IP). If the Service needs to support HTTP, the specifications of dedicated load balancers must use HTTP/HTTPS (application load balancing) in addition to TCP/UDP (network load balancing).
-  In a CCE cluster, if the cluster-level affinity is configured for a LoadBalancer Service, requests are distributed to the node ports of each node using SNAT when entering the cluster. The number of node ports cannot exceed the number of available node ports on the node. If the service affinity is at the node level (Local), there is no such constraint. In a CCE Turbo cluster, this constraint applies to shared load balancers, but not dedicated ones. Use dedicated load balancers in CCE Turbo clusters.
-  When the cluster service forwarding (proxy) mode is IPVS, the node IP cannot be configured as the external IP of the Service. Otherwise, the node is unavailable.
-  In a cluster using the IPVS proxy mode, if the ingress and Service use the same ELB load balancer, the ingress cannot be accessed from the nodes and containers in the cluster because kube-proxy mounts the LoadBalancer Service address to the ipvs-0 bridge. This bridge intercepts the traffic of the load balancer connected to the ingress. You are advised to use different ELB load balancers for the ingress and Service.


Creating a LoadBalancer Service
-------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Networking** in the navigation pane and click **Create Service** in the upper right corner.
#. Configure parameters.

   -  **Service Name**: Specify a Service name, which can be the same as the workload name.

   -  **Service Type**: Select **LoadBalancer**.

   -  **Namespace**: Namespace to which the workload belongs.

   -  **Service Affinity**: For details, see :ref:`externalTrafficPolicy (Service Affinity) <cce_10_0249__section18134208069>`.

      -  **Cluster level**: The IP addresses and access ports of all nodes in a cluster can be used to access the workload associated with the Service. Service access will cause performance loss due to route redirection, and the source IP address of the client cannot be obtained.
      -  **Node level**: Only the IP address and access port of the node where the workload is located can access the workload associated with the Service. Service access will not cause performance loss due to route redirection, and the source IP address of the client can be obtained.

   -  **Selector**: Add a label and click **Confirm**. A Service selects a pod based on the added label. You can also click **Reference Workload Label** to reference the label of an existing workload. In the dialog box that is displayed, select a workload and click **OK**.

   -  **Load Balancer**

      Select the load balancer to interconnect. Only load balancers in the same VPC as the cluster are supported. If no load balancer is available, click **Create Load Balancer** to create one on the ELB console.

      The CCE console supports automatic creation of load balancers. Select **Auto create** from the drop-down list box and set the following parameters:

      -  **Instance Name**: Enter a load balancer name.
      -  **Public Access**: If enabled, an EIP with 5 Mbit/s bandwidth will be created.
      -  **Subnet**, **AZ**, and **Specifications** (available only for dedicated load balancers): Configure the subnet AZ, and specifications. Currently, only dedicated load balancers of the network type (TCP/UDP) can be automatically created.

      You can click **Edit** in the **Set ELB** area and configure load balancer parameters in the **Set ELB** dialog box.

      -  .. _cce_10_0681__li8170555132211:

         **Algorithm**: Three algorithms are available: weighted round robin, weighted least connections algorithm, or source IP hash.

         .. note::

            -  **Weighted round robin**: Requests are forwarded to different servers based on their weights, which indicate server processing performance. Backend servers with higher weights receive proportionately more requests, whereas equal-weighted servers receive the same number of requests. This algorithm is often used for short connections, such as HTTP services.
            -  **Weighted least connections**: In addition to the weight assigned to each server, the number of connections processed by each backend server is also considered. Requests are forwarded to the server with the lowest connections-to-weight ratio. Building on **least connections**, the **weighted least connections** algorithm assigns a weight to each server based on their processing capability. This algorithm is often used for persistent connections, such as database connections.
            -  **Source IP hash**: The source IP address of each request is calculated using the hash algorithm to obtain a unique hash key, and all backend servers are numbered. The generated key allocates the client to a particular server. This enables requests from different clients to be distributed in load balancing mode and ensures that requests from the same client are forwarded to the same server. This algorithm applies to TCP connections without cookies.

      -  **Type**: This function is disabled by default. You can select **Source IP address**. Source IP address-based sticky session means that access requests from the same IP address are forwarded to the same backend server.

         .. note::

            When the :ref:`distribution policy <cce_10_0681__li8170555132211>` uses the source IP address algorithm, sticky session cannot be set.

   -  .. _cce_10_0681__li15274642132013:

      **Health Check**: Configure health check for the load balancer.

      -  **Global health check**: applies only to ports using the same protocol. You are advised to select **Custom health check**.
      -  **Custom health check**: applies to :ref:`ports <cce_10_0681__li388800117144>` using different protocols. For details about the YAML definition for custom health check, see :ref:`Configuring Health Check for Multiple Ports <cce_10_0684>`.

      .. _cce_10_0681__table11219123154614:

      .. table:: **Table 1** Health check parameters

         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                               |
         +===================================+===========================================================================================================================================================================================================================================================+
         | Protocol                          | When the protocol of :ref:`Port <cce_10_0681__li388800117144>` is set to TCP, the TCP and HTTP are supported. When the protocol of :ref:`Port <cce_10_0681__li388800117144>` is set to UDP, the UDP is supported.                                         |
         |                                   |                                                                                                                                                                                                                                                           |
         |                                   | -  **Check Path** (supported only by the HTTP): specifies the health check URL. The check path must start with a slash (/) and contain 1 to 80 characters.                                                                                                |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Port                              | By default, the service port (Node Port and container port of the Service) is used for health check. You can also specify another port for health check. After the port is specified, a service port named **cce-healthz** will be added for the Service. |
         |                                   |                                                                                                                                                                                                                                                           |
         |                                   | -  **Node Port**: If a shared load balancer is used or no ENI instance is associated, the node port is used as the health check port. If this parameter is not specified, a random port is used. The value ranges from 30000 to 32767.                    |
         |                                   | -  **Container Port**: When a dedicated load balancer is associated with an ENI instance, the container port is used for health check. The value ranges from 1 to 65535.                                                                                  |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Check Period (s)                  | Specifies the maximum interval between health checks. The value ranges from 1 to 50.                                                                                                                                                                      |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Timeout (s)                       | Specifies the maximum timeout duration for each health check. The value ranges from 1 to 50.                                                                                                                                                              |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Max. Retries                      | Specifies the maximum number of health check retries. The value ranges from 1 to 10.                                                                                                                                                                      |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  .. _cce_10_0681__li388800117144:

      **Port**

      -  **Protocol**: protocol used by the Service.
      -  **Service Port**: port used by the Service. The port number ranges from 1 to 65535.
      -  **Container Port**: port on which the workload listens. For example, Nginx uses port 80 by default.
      -  **Health Check**: If :ref:`Health Check <cce_10_0681__li15274642132013>` is set to **Custom health check**, you can configure health check for ports using different protocols. For details, see :ref:`Table 1 <cce_10_0681__table11219123154614>`.

      .. note::

         When a LoadBalancer Service is created, a random node port number (NodePort) is automatically generated.

   -  **Annotation**: The LoadBalancer Service has some advanced CCE functions, which are implemented by annotations. For details, see :ref:`Using Annotations to Configure Load Balancing <cce_10_0385>`.

#. Click **OK**.

.. _cce_10_0681__section74196215320:

Using kubectl to Create a Service (Using an Existing Load Balancer)
-------------------------------------------------------------------

You can set the Service when creating a workload using kubectl. This section uses an Nginx workload as an example to describe how to add a LoadBalancer Service using kubectl.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create the files named **nginx-deployment.yaml** and **nginx-elb-svc.yaml** and edit them.

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
        name: nginx
        annotations:
          kubernetes.io/elb.id: <your_elb_id>                         # ELB ID. Replace it with the actual value.
          kubernetes.io/elb.class: union                   # Load balancer type
          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN                   # Load balancer algorithm
          kubernetes.io/elb.session-affinity-mode: SOURCE_IP          # The sticky session type is source IP address.
          kubernetes.io/elb.session-affinity-option: '{"persistence_timeout": "30"}'     # Stickiness duration (min)
          kubernetes.io/elb.health-check-flag: 'on'                   # Enable the ELB health check function.
          kubernetes.io/elb.health-check-option: '{
            "protocol":"TCP",
            "delay":"5",
            "timeout":"10",
            "max_retries":"3"
          }'
      spec:
        selector:
           app: nginx
        ports:
        - name: service0
          port: 80     # Port for accessing the Service, which is also the listener port on the load balancer.
          protocol: TCP
          targetPort: 80  # Port used by a Service to access the target container. This port is closely related to the applications running in a container.
          nodePort: 31128  # Port number of the node. If this parameter is not specified, a random port number ranging from 30000 to 32767 is generated.
        type: LoadBalancer

   The preceding example uses annotations to implement some advanced functions of load balancing, such as sticky session and health check. For details, see :ref:`Table 2 <cce_10_0681__table5352104717398>`.

   In addition to the functions in this example, for more annotations and examples related to advanced functions, see :ref:`Using Annotations to Configure Load Balancing <cce_10_0385>`.

   .. _cce_10_0681__table5352104717398:

   .. table:: **Table 2** annotations parameters

      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Mandatory       | Type                                                     | Description                                                                                                                                                                                                                                                                                            |
      +===========================================+=================+==========================================================+========================================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.id                      | Yes             | String                                                   | ID of an enhanced load balancer.                                                                                                                                                                                                                                                                       |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | Mandatory when an existing load balancer is to be associated.                                                                                                                                                                                                                                          |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | **How to obtain**:                                                                                                                                                                                                                                                                                     |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | On the management console, click **Service List**, and choose **Networking** > **Elastic Load Balance**. Click the name of the target load balancer. On the **Summary** tab page, find and copy the ID.                                                                                                |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | .. note::                                                                                                                                                                                                                                                                                              |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          |    The system preferentially connects to the load balancer based on the **kubernetes.io/elb.id** field. If this field is not specified, the **spec.loadBalancerIP** field is used (optional and available only in 1.23 and earlier versions).                                                          |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          |    Do not use the **spec.loadBalancerIP** field to connect to the load balancer. This field will be discarded by Kubernetes. For details, see `Deprecation <https://github.com/kubernetes/kubernetes/blob/8f2371bcceff7962ddb4901c36536c6ff659755b/CHANGELOG/CHANGELOG-1.24.md#changes-by-kind-13>`__. |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.class                   | Yes             | String                                                   | Select a proper load balancer type.                                                                                                                                                                                                                                                                    |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | -  **union**: shared load balancer                                                                                                                                                                                                                                                                     |
      |                                           |                 |                                                          | -  **performance**: dedicated load balancer, which can be used only in clusters of v1.17 and later.                                                                                                                                                                                                    |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | .. note::                                                                                                                                                                                                                                                                                              |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          |    If a LoadBalancer Service accesses an existing dedicated load balancer, the dedicated load balancer must support TCP/UDP networking.                                                                                                                                                                |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.lb-algorithm            | No              | String                                                   | Specifies the load balancing algorithm of the backend server group. The default value is **ROUND_ROBIN**.                                                                                                                                                                                              |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | Options:                                                                                                                                                                                                                                                                                               |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | -  **ROUND_ROBIN**: weighted round robin algorithm                                                                                                                                                                                                                                                     |
      |                                           |                 |                                                          | -  **LEAST_CONNECTIONS**: weighted least connections algorithm                                                                                                                                                                                                                                         |
      |                                           |                 |                                                          | -  **SOURCE_IP**: source IP hash algorithm                                                                                                                                                                                                                                                             |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | .. note::                                                                                                                                                                                                                                                                                              |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          |    If this parameter is set to **SOURCE_IP**, the weight setting (**weight** field) of backend servers bound to the backend server group is invalid, and sticky session cannot be enabled.                                                                                                             |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-mode   | No              | String                                                   | Source IP address-based sticky session is supported. That is, access requests from the same IP address are forwarded to the same backend server.                                                                                                                                                       |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | -  Disabling sticky session: Do not configure this parameter.                                                                                                                                                                                                                                          |
      |                                           |                 |                                                          | -  Enabling sticky session: Set this parameter to **SOURCE_IP**, indicating that the sticky session is based on the source IP address.                                                                                                                                                                 |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | .. note::                                                                                                                                                                                                                                                                                              |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          |    When **kubernetes.io/elb.lb-algorithm** is set to **SOURCE_IP** (source IP address algorithm), sticky session cannot be enabled.                                                                                                                                                                    |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-option | No              | :ref:`Table 3 <cce_10_0681__table43592047133910>` object | Sticky session timeout.                                                                                                                                                                                                                                                                                |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.health-check-flag       | No              | String                                                   | Whether to enable the ELB health check.                                                                                                                                                                                                                                                                |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | -  Enabling health check: Leave blank this parameter or set it to **on**.                                                                                                                                                                                                                              |
      |                                           |                 |                                                          | -  Disabling health check: Set this parameter to **off**.                                                                                                                                                                                                                                              |
      |                                           |                 |                                                          |                                                                                                                                                                                                                                                                                                        |
      |                                           |                 |                                                          | If this parameter is enabled, the :ref:`kubernetes.io/elb.health-check-option <cce_10_0681__table236017471397>` field must also be specified at the same time.                                                                                                                                         |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.health-check-option     | No              | :ref:`Table 4 <cce_10_0681__table236017471397>` object   | ELB health check configuration items.                                                                                                                                                                                                                                                                  |
      +-------------------------------------------+-----------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0681__table43592047133910:

   .. table:: **Table 3** Data structure of the **elb.session-affinity-option** field

      +---------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------+
      | Parameter           | Mandatory       | Type            | Description                                                                                                                  |
      +=====================+=================+=================+==============================================================================================================================+
      | persistence_timeout | Yes             | String          | Sticky session timeout, in minutes. This parameter is valid only when **elb.session-affinity-mode** is set to **SOURCE_IP**. |
      |                     |                 |                 |                                                                                                                              |
      |                     |                 |                 | Value range: 1 to 60. Default value: **60**                                                                                  |
      +---------------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0681__table236017471397:

   .. table:: **Table 4** Data structure description of the **elb.health-check-option** field

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
      |                 |                 |                 | Value options: TCP or HTTP                                                         |
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

   If information similar to the following is displayed, the access type has been set, and the workload is accessible.

   .. code-block::

      NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
      kubernetes   ClusterIP      10.247.0.1       <none>        443/TCP        3d
      nginx        LoadBalancer   10.247.130.196   10.78.42.242   80:31540/TCP   51s

#. Enter the URL in the address box of the browser, for example, **10.78.42.242:80**. **10.78.42.242** indicates the IP address of the load balancer, and **80** indicates the access port displayed on the CCE console.

   The Nginx is accessible.


   .. figure:: /_static/images/en-us_image_0000001695736993.png
      :alt: **Figure 3** Accessing Nginx through the LoadBalancer Service

      **Figure 3** Accessing Nginx through the LoadBalancer Service

.. _cce_10_0681__section6422152185311:

Using kubectl to Create a Service (Automatically Creating a Load Balancer)
--------------------------------------------------------------------------

You can set the Service when creating a workload using kubectl. This section uses an Nginx workload as an example to describe how to add a LoadBalancer Service using kubectl.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create the files named **nginx-deployment.yaml** and **nginx-elb-svc.yaml** and edit them.

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

   Example of a Service using a public network shared load balancer:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/elb.autocreate: '{
            "type": "public",
            "bandwidth_name": "cce-bandwidth-1551163379627",
            "bandwidth_chargemode": "bandwidth",
            "bandwidth_size": 5,
            "bandwidth_sharetype": "PER",
            "eip_type": "5_bgp"
          }'

          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN                   # Load balancer algorithm
          kubernetes.io/elb.session-affinity-mode: SOURCE_IP          # The sticky session type is source IP address.
          kubernetes.io/elb.session-affinity-option: '{"persistence_timeout": "30"}'     # Stickiness duration (min)
          kubernetes.io/elb.health-check-flag: 'on'                   # Enable the ELB health check function.
          kubernetes.io/elb.health-check-option: '{
            "protocol":"TCP",
            "delay":"5",
            "timeout":"10",
            "max_retries":"3"
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

   Example Service using a public network dedicated load balancer (only for clusters of v1.17 and later):

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
          kubernetes.io/elb.autocreate: '{
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

          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN                   # Load balancer algorithm
          kubernetes.io/elb.session-affinity-mode: SOURCE_IP          # The sticky session type is source IP address.
          kubernetes.io/elb.session-affinity-option: '{"persistence_timeout": "30"}'     # Stickiness duration (min)
          kubernetes.io/elb.health-check-flag: 'on'                   # Enable the ELB health check function.
          kubernetes.io/elb.health-check-option: '{
            "protocol":"TCP",
            "delay":"5",
            "timeout":"10",
            "max_retries":"3"
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

   The preceding example uses annotations to implement some advanced functions of load balancing, such as sticky session and health check. For details, see :ref:`Table 5 <cce_10_0681__table133089105019>`.

   In addition to the functions in this example, for more annotations and examples related to advanced functions, see :ref:`Using Annotations to Configure Load Balancing <cce_10_0385>`.

   .. _cce_10_0681__table133089105019:

   .. table:: **Table 5** annotations parameters

      +-------------------------------------------+-----------------+---------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Mandatory       | Type                                                          | Description                                                                                                                                                                                |
      +===========================================+=================+===============================================================+============================================================================================================================================================================================+
      | kubernetes.io/elb.class                   | Yes             | String                                                        | Select a proper load balancer type.                                                                                                                                                        |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               | -  **union**: shared load balancer                                                                                                                                                         |
      |                                           |                 |                                                               | -  **performance**: dedicated load balancer, which can be used only in clusters of v1.17 and later.                                                                                        |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.autocreate              | Yes             | :ref:`elb.autocreate <cce_10_0681__table939522754617>` object | Whether to automatically create a load balancer associated with the Service.                                                                                                               |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               | **Example**                                                                                                                                                                                |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               | -  If a public network load balancer will be automatically created, set this parameter to the following value:                                                                             |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               |    {"type":"public","bandwidth_name":"cce-bandwidth-1551163379627","bandwidth_chargemode":"bandwidth","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}    |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               | -  If a private network load balancer will be automatically created, set this parameter to the following value:                                                                            |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               |    {"type":"inner","name":"A-location-d-test"}                                                                                                                                             |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.subnet-id               | None            | String                                                        | ID of the subnet where the cluster is located. The value can contain 1 to 100 characters.                                                                                                  |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               | -  Mandatory when a cluster of v1.11.7-r0 or earlier is to be automatically created.                                                                                                       |
      |                                           |                 |                                                               | -  Optional for clusters later than v1.11.7-r0.                                                                                                                                            |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.lb-algorithm            | No              | String                                                        | Specifies the load balancing algorithm of the backend server group. The default value is **ROUND_ROBIN**.                                                                                  |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               | Options:                                                                                                                                                                                   |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               | -  **ROUND_ROBIN**: weighted round robin algorithm                                                                                                                                         |
      |                                           |                 |                                                               | -  **LEAST_CONNECTIONS**: weighted least connections algorithm                                                                                                                             |
      |                                           |                 |                                                               | -  **SOURCE_IP**: source IP hash algorithm                                                                                                                                                 |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               | .. note::                                                                                                                                                                                  |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               |    If this parameter is set to **SOURCE_IP**, the weight setting (**weight** field) of backend servers bound to the backend server group is invalid, and sticky session cannot be enabled. |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-mode   | No              | String                                                        | Source IP address-based sticky session is supported. That is, access requests from the same IP address are forwarded to the same backend server.                                           |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               | -  Disabling sticky session: Do not configure this parameter.                                                                                                                              |
      |                                           |                 |                                                               | -  Enabling sticky session: Set this parameter to **SOURCE_IP**, indicating that the sticky session is based on the source IP address.                                                     |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               | .. note::                                                                                                                                                                                  |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               |    When **kubernetes.io/elb.lb-algorithm** is set to **SOURCE_IP** (source IP address algorithm), sticky session cannot be enabled.                                                        |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.session-affinity-option | No              | :ref:`Table 3 <cce_10_0681__table43592047133910>` object      | Sticky session timeout.                                                                                                                                                                    |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.health-check-flag       | No              | String                                                        | Whether to enable the ELB health check.                                                                                                                                                    |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               | -  Enabling health check: Leave blank this parameter or set it to **on**.                                                                                                                  |
      |                                           |                 |                                                               | -  Disabling health check: Set this parameter to **off**.                                                                                                                                  |
      |                                           |                 |                                                               |                                                                                                                                                                                            |
      |                                           |                 |                                                               | If this parameter is enabled, the :ref:`kubernetes.io/elb.health-check-option <cce_10_0681__table236017471397>` field must also be specified at the same time.                             |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.health-check-option     | No              | :ref:`Table 4 <cce_10_0681__table236017471397>` object        | ELB health check configuration items.                                                                                                                                                      |
      +-------------------------------------------+-----------------+---------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0681__table939522754617:

   .. table:: **Table 6** Data structure of the **elb.autocreate** field

      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter            | Mandatory                             | Type             | Description                                                                                                                                                                                                                                                                                                                                                                      |
      +======================+=======================================+==================+==================================================================================================================================================================================================================================================================================================================================================================================+
      | name                 | No                                    | String           | Name of the automatically created load balancer.                                                                                                                                                                                                                                                                                                                                 |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | The value can contain 1 to 64 characters. Only letters, digits, underscores (_), hyphens (-), and periods (.) are allowed.                                                                                                                                                                                                                                                       |
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
      |                      |                                       |                  | The value can contain 1 to 64 characters. Only letters, digits, underscores (_), hyphens (-), and periods (.) are allowed.                                                                                                                                                                                                                                                       |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_chargemode | No                                    | String           | Bandwidth mode.                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | -  **bandwidth**: billed by bandwidth                                                                                                                                                                                                                                                                                                                                            |
      |                      |                                       |                  | -  **traffic**: billed by traffic                                                                                                                                                                                                                                                                                                                                                |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | Default: **bandwidth**                                                                                                                                                                                                                                                                                                                                                           |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_size       | Yes for public network load balancers | Integer          | Bandwidth size. The default value is 1 to 2000 Mbit/s. Configure this parameter based on the bandwidth range allowed in your region.                                                                                                                                                                                                                                             |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | The minimum increment for bandwidth adjustment varies depending on the bandwidth range.                                                                                                                                                                                                                                                                                          |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | -  The minimum increment is 1 Mbit/s if the allowed bandwidth does not exceed 300 Mbit/s.                                                                                                                                                                                                                                                                                        |
      |                      |                                       |                  | -  The minimum increment is 50 Mbit/s if the allowed bandwidth ranges from 300 Mbit/s to 1000 Mbit/s.                                                                                                                                                                                                                                                                            |
      |                      |                                       |                  | -  The minimum increment is 500 Mbit/s if the allowed bandwidth exceeds 1000 Mbit/s.                                                                                                                                                                                                                                                                                             |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_sharetype  | Yes for public network load balancers | String           | Bandwidth sharing mode.                                                                                                                                                                                                                                                                                                                                                          |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | -  **PER**: dedicated bandwidth                                                                                                                                                                                                                                                                                                                                                  |
      +----------------------+---------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | eip_type             | Yes for public network load balancers | String           | EIP type.                                                                                                                                                                                                                                                                                                                                                                        |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | -  **5_bgp**: dynamic BGP                                                                                                                                                                                                                                                                                                                                                        |
      |                      |                                       |                  |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                      |                                       |                  | The specific type varies with regions. For details, see the EIP console.                                                                                                                                                                                                                                                                                                         |
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
      |                      |                                       |                  | This parameter is available only for dedicated load balancers. The value of this parameter must be the same as that of **l4_flavor_name**, that is, both are elastic specifications or fixed specifications.                                                                                                                                                                     |
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

   If information similar to the following is displayed, the access type has been set, and the workload is accessible.

   .. code-block::

      NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
      kubernetes   ClusterIP      10.247.0.1       <none>        443/TCP        3d
      nginx        LoadBalancer   10.247.130.196   10.78.42.242   80:31540/TCP   51s

#. Enter the URL in the address box of the browser, for example, **10.**\ *XXX.XXX.XXX*\ **:80**. **10.**\ *XXX.XXX.XXX* indicates the IP address of the load balancer, and **80** indicates the access port displayed on the CCE console.

   The Nginx is accessible.


   .. figure:: /_static/images/en-us_image_0000001647576596.png
      :alt: **Figure 4** Accessing Nginx through the LoadBalancer Service

      **Figure 4** Accessing Nginx through the LoadBalancer Service
