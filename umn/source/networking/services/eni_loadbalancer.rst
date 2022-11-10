:original_name: cce_01_0114.html

.. _cce_01_0114:

ENI LoadBalancer
================

.. _cce_01_0114__section025118182286:

Scenario
--------

An ENI LoadBalancer Service directs traffic from a load balancer at backend pods, reducing the latency and avoiding performance loss for containerized applications.

External access requests are directly forwarded from a load balancer to pods. Internal access requests can be forwarded to a pod through a Service.

|image1|

Notes and Constraints
---------------------

-  ENI LoadBalancer is available only in certain regions.
-  Only dedicated load balancers are supported, and they must support layer-4 networking (TCP/UDP).
-  After a load balancer is created, its flavor cannot be changed. Therefore, in CCE, after you create a Service, you cannot connect the automatically created load balancer to other objects. If no load balancer is automatically created, you can connect any existing one to the Service.
-  The cluster version must be 1.17 or later.
-  ENI LoadBalancer Services can be created only for workloads (containers) bound with elastic network interfaces (ENIs).

.. _cce_01_0114__section17753911588:

Adding a Service When Creating a Workload
-----------------------------------------

You can set the Service when creating a workload on the CCE console. An Nginx workload is used as an example.

#. In the **Set Application Access** step of :ref:`Creating a Deployment <cce_01_0047>`, :ref:`Creating a StatefulSet <cce_01_0048>`, or :ref:`Creating a DaemonSet <cce_01_0216>`, click **Add Service** and set the following parameters:

   -  **Access Type**: Select **ENI LoadBalancer (ELB)**. This option is available only if you have selected **Attach ENI to Pod** when specifying basic workload information during workload creation.
   -  **Service Name**: Specify a Service name, which can be the same as the workload name.

   **ELB Configuration**

   -  **Elastic Load Balancer**: Only dedicated load balancers are supported.

      **Dedicated**: Resources are shared among load balancers, and the performance of a load balancer is not affected by other load balancers. IPv6 is supported.

      You can create **public network** or **private network** load balancers.

      -  **Public network**: You can select an existing public network load balancer or have the system automatically create a new one.
      -  **Private network**: You can select an existing private network load balancer or have the system automatically create a new one.

      The selected or created load balancer must be in the same VPC as the current cluster, and it must match the load balancer type (private or public network).

      -  **Enterprise Project**: Select an enterprise project in which the load balancer is created.
      -  **Specifications**: This field is displayed only when you select **Public network** and **Automatically created** for **Elastic Load Balancer**. You can click |image2| to modify the name, specifications, billing mode, and bandwidth of the load balancer.
      -  Configure Dedicated Load Balancer

         -  **AZ**: Dedicated load balancers can be deployed across AZs to provide higher reliability.

         -  **Subnet**: subnet where the backend server of the load balancer is located.

            Load balancers occupy different number of subnet IP addresses based on their specifications. Therefore, you are not advised to use the subnet CIDR blocks of other resources (such as clusters and nodes) as the load balancer CIDR block.

         -  **Specifications**: Specifications determine the types of listeners that can be added to a load balancer. Select specifications that best fit your needs. For details, see `Specifications of Dedicated Load Balancers <https://docs.otc.t-systems.com/usermanual/elb/en-us_topic_0287737145.html>`__.

      -  **Algorithm Type**: You can select **Weighted round robin**, **Weighted least connections**, or **Source IP hash**. The weight is dynamically adjusted based on the number of pods of the workload associated with the Service on each node.

         .. note::

            -  **Weighted round robin**: Requests are forwarded to different servers based on their weights, which indicate server processing performance. Backend servers with higher weights receive proportionately more requests, whereas equal-weighted servers receive the same number of requests. This algorithm is often used for short connections, such as HTTP services.
            -  **Weighted least connections**: In addition to the weight assigned to each server, the number of connections processed by each backend server is also considered. Requests are forwarded to the server with the lowest connections-to-weight ratio. Building on **least connections**, the **weighted least connections** algorithm assigns a weight to each server based on their processing performance. This algorithm is often used for persistent connections, such as database connections.
            -  **Source IP hash**: The source IP address of each request is calculated using the hash algorithm to obtain a unique hash key, and all backend servers are numbered. The generated key allocates the client to a particular server. This allows requests from different clients to be routed based on source IP addresses and ensures that a client is directed to the same server as always. This algorithm applies to TCP connections without cookies.

      -  **Sticky Session**: This function is disabled by default. You can select **Based on source IP address**. Listeners ensure session stickiness based on IP addresses. Requests from the same IP address will be forwarded to the same backend server.
      -  **Health Check**: This function is enabled by default. Enabling it will perform health checks on your load balancer. For details about how to configure the ELB health check parameters, see `Configuring a Health Check <https://docs.otc.t-systems.com/en-us/usermanual/elb/en-us_topic_0162227063.html>`__.

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

   The parameters are the same as those in :ref:`Adding a Service When Creating a Workload <cce_01_0114__section17753911588>`.

#. Click **Create**. An ENI LoadBalancer Service will be added for the workload.

Using kubectl to Create a Service (Automatically Creating a Load Balancer)
--------------------------------------------------------------------------

An ENI LoadBalancer Service supports only dedicated ELBs. You do not need to specify NodePort when creating a Service.

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
       name: example
       annotations:
           kubernetes.io/elb.class: performance
           kubernetes.io/elb.autocreate:
             '
             {
                 "type": "public",
                 "bandwidth_name": "cce-bandwidth-1630813564682",
                 "bandwidth_chargemode": "traffic",
                 "bandwidth_size": 5,
                 "bandwidth_sharetype": "PER",
                 "eip_type": "5_bgp",
                 "available_zone": [
                     "eu-de-01"
                 ],
                 "l7_flavor_name": "L7_flavor.elb.s2.medium",
                 "l4_flavor_name": "L4_flavor.elb.s1.small"
             }
             '
   spec:
       selector:
           app: example
       ports:
           -   name: cce-service-0
               targetPort: 80
               port: 8082
               protocol: TCP
       type: LoadBalancer

For details about the parameters, see :ref:`Table 4 <cce_01_0014__table133089105019>`.

Using kubectl to Create a Service (Using an Existing Load Balancer)
-------------------------------------------------------------------

When creating a Service using an existing load balancer, you only need to specify the ID of the load balancer.

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
       name: example
       annotations:
           kubernetes.io/elb.id: bcc44e84-d0b5-4192-8bec-b2ca55ce5025     # ID of the load balancer. Replace it with the actual value.
   spec:
       selector:
           app: example
       ports:
           -   name: cce-service-0
               targetPort: 80
               port: 8082
               protocol: TCP
       type: LoadBalancer

ELB Forwarding
--------------

After an ENI LoadBalancer Service is created, you can view the listener forwarding rules of the load balancer on the ELB console.


.. figure:: /_static/images/en-us_image_0000001204449561.png
   :alt: **Figure 1** ELB forwarding

   **Figure 1** ELB forwarding

You can find that a listener is created for the load balancer. The backend server address is the IP address of the pod, and the service port is the container port. This is because the pod uses an ENI or sub-ENI. When traffic passes through the load balancer, it directly forwards the traffic to the pod. This is the same as that described in :ref:`Scenario <cce_01_0114__section025118182286>`.

.. |image1| image:: /_static/images/en-us_image_0000001152953258.png
.. |image2| image:: /_static/images/en-us_image_0000001126243447.png
