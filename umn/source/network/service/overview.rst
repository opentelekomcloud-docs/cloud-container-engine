:original_name: cce_10_0249.html

.. _cce_10_0249:

Overview
========

Direct Access to a Pod
----------------------

After a pod is created, the following problems may occur if you directly access to the pod:

-  The pod can be deleted and created again at any time by a controller such as a Deployment, and the result of accessing the pod becomes unpredictable.
-  The IP address of the pod is allocated only after the pod is started. Before the pod is started, the IP address of the pod is unknown.
-  An application is usually composed of multiple pods that run the same image. Accessing pods one by one is not efficient.

For example, an application uses Deployments to create the frontend and backend. The frontend calls the backend for computing, as shown in :ref:`Figure 1 <cce_10_0249__en-us_topic_0249851121_fig2173165051811>`. Three pods are running in the backend, which are independent and replaceable. When a backend pod is created again, the new pod is assigned with a new IP address, of which the frontend pod is unaware.

.. _cce_10_0249__en-us_topic_0249851121_fig2173165051811:

.. figure:: /_static/images/en-us_image_0000001647417852.png
   :alt: **Figure 1** Inter-pod access

   **Figure 1** Inter-pod access

Using Services for Pod Access
-----------------------------

Kubernetes Services are used to solve the preceding pod access problems. A Service has a fixed IP address. (When a CCE cluster is created, a Service CIDR block is set, which is used to allocate IP addresses to Services.) A Service forwards requests accessing the Service to pods based on labels, and at the same time, performs load balancing for these pods.

In the preceding example, a Service is added for the frontend pod to access the backend pods. In this way, the frontend pod does not need to be aware of the changes on backend pods, as shown in :ref:`Figure 2 <cce_10_0249__en-us_topic_0249851121_fig163156154816>`.

.. _cce_10_0249__en-us_topic_0249851121_fig163156154816:

.. figure:: /_static/images/en-us_image_0000001695896373.png
   :alt: **Figure 2** Accessing pods through a Service

   **Figure 2** Accessing pods through a Service

Service Types
-------------

Kubernetes allows you to specify a Service of a required type. The values and actions of different types of Services are as follows:

-  :ref:`ClusterIP <cce_10_0011>`

   A ClusterIP Service allows workloads in the same cluster to use their cluster-internal domain names to access each other.

-  :ref:`NodePort <cce_10_0142>`

   A Service is exposed on each node's IP address at a static port (NodePort). A ClusterIP Service, to which the NodePort Service will route, is automatically created. By requesting <NodeIP>:<NodePort>, you can access a NodePort Service from outside the cluster.

-  :ref:`LoadBalancer <cce_10_0014>`

   LoadBalancer Services can access workloads from the public network through a load balancer, which is more reliable than EIP-based access. LoadBalancer Services are recommended for accessing workloads from outside the cluster.

.. _cce_10_0249__section18134208069:

externalTrafficPolicy (Service Affinity)
----------------------------------------

For a NodePort and LoadBalancer Service, requests are first sent to the node port, then the Service, and finally the pod backing the Service. The backing pod may be not located in the node receiving the requests. By default, the backend workload can be accessed from any node IP address and service port. If the pod is not on the node that receives the request, the request will be redirected to the node where the pod is located, which may cause performance loss.

**externalTrafficPolicy** is a configuration parameter of the Service.

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx-nodeport
   spec:
     externalTrafficPolicy: Local
     ports:
     - name: service
       nodePort: 30000
       port: 80
       protocol: TCP
       targetPort: 80
     selector:
       app: nginx
     type: NodePort

If the value of **externalTrafficPolicy** is **Local**, requests sent from *Node IP address:Service port* will be forwarded only to the pod on the local node. If the node does not have a pod, the requests are suspended.

If the value of **externalTrafficPolicy** is **Cluster**, requests are forwarded within the cluster and the backend workload can be accessed from any node IP address and service port.

If **externalTrafficPolicy** is not set, the default value **Cluster** will be used.

You can set this parameter when creating a Service of the NodePort type on the CCE console.

The values of **externalTrafficPolicy** are as follows:

-  **Cluster**: The IP addresses and access ports of all nodes in a cluster can access the workload associated with the Service. Service access will cause performance loss due to route redirection, and the source IP address of the client cannot be obtained.
-  **Local**: Only the IP address and access port of the node where the workload is located can access the workload associated with the Service. Service access will not cause performance loss due to route redirection, and the source IP address of the client can be obtained. In this scenario, Services may fail to be accessed from within the cluster. For details, see :ref:`Why a Service Fail to Be Accessed from Within the Cluster <cce_10_0249__section52631714117>`.

.. _cce_10_0249__section52631714117:

Why a Service Fail to Be Accessed from Within the Cluster
---------------------------------------------------------

If the service affinity of a Service is set to the node level, that is, the value of **externalTrafficPolicy** is **Local**, the Service may fail to be accessed from within the cluster (specifically, nodes or containers). Information similar to the following is displayed:

.. code-block::

   upstream connect error or disconnect/reset before headers. reset reason: connection failure
   Or
   curl: (7) Failed to connect to 192.168.10.36 port 900: Connection refused

It is common that a load balancer in a cluster cannot be accessed. The reason is as follows: When Kubernetes creates a Service, kube-proxy adds the access address of the load balancer as an external IP address (External-IP, as shown in the following command output) to iptables or IPVS. If a client inside the cluster initiates a request to access the load balancer, the address is considered as the external IP address of the Service, and the request is directly forwarded by kube-proxy without passing through the load balancer outside the cluster.

.. code-block::

   # kubectl get svc nginx
   NAME    TYPE           CLUSTER-IP      EXTERNAL-IP                   PORT(S)        AGE
   nginx   LoadBalancer   10.247.76.156   123.**.**.**,192.168.0.133   80:32146/TCP   37s

When the value of **externalTrafficPolicy** is **Local**, the access failures in different container network models and service forwarding modes are as follows:

.. note::

   -  For a multi-pod workload, ensure that all pods are accessible. Otherwise, there is a possibility that the access to the workload fails.
   -  CCE Turbo clusters using Cloud Native 2.0 networking do not support node-level service affinity.

+------------------------------------------------------+------------------------+----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| Service Type Released on the Server                  | Access Type            | Request Initiation Location on the Client                | Tunnel Network Cluster (IPVS)                                                                                           | VPC Network Cluster (IPVS)                                                                                       | Tunnel Network Cluster (iptables)                                                                                | VPC Network Cluster (iptables)                                                                                   |
+======================================================+========================+==========================================================+=========================================================================================================================+==================================================================================================================+==================================================================================================================+==================================================================================================================+
| NodePort Service                                     | Public/Private network | Same node as the service pod                             | Access the IP address and NodePort on the node where the server is located: The access is successful.                   | Access the IP address and NodePort on the node where the server is located: The access is successful.            | Access the IP address and NodePort on the node where the server is located: The access is successful.            | Access the IP address and NodePort on the node where the server is located: The access is successful.            |
|                                                      |                        |                                                          |                                                                                                                         |                                                                                                                  |                                                                                                                  |                                                                                                                  |
|                                                      |                        |                                                          | Access the IP address and NodePort on a node other than the node where the server is located: The access failed.        | Access the IP address and NodePort on a node other than the node where the server is located: The access failed. | Access the IP address and NodePort on a node other than the node where the server is located: The access failed. | Access the IP address and NodePort on a node other than the node where the server is located: The access failed. |
+------------------------------------------------------+------------------------+----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
|                                                      |                        | Different nodes from the service pod                     | Access the IP address and NodePort on the node where the server is located: The access is successful.                   | Access the IP address and NodePort on the node where the server is located: The access is successful.            | The access is successful.                                                                                        | The access is successful.                                                                                        |
|                                                      |                        |                                                          |                                                                                                                         |                                                                                                                  |                                                                                                                  |                                                                                                                  |
|                                                      |                        |                                                          | Access the IP address and NodePort on a node other than the node where the server is located: The access failed.        | Access the IP address and NodePort on a node other than the node where the server is located: The access failed. |                                                                                                                  |                                                                                                                  |
+------------------------------------------------------+------------------------+----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
|                                                      |                        | Other containers on the same node as the service pod     | Access the IP address and NodePort on the node where the server is located: The access is successful.                   | The access failed.                                                                                               | Access the IP address and NodePort on the node where the server is located: The access is successful.            | The access failed.                                                                                               |
|                                                      |                        |                                                          |                                                                                                                         |                                                                                                                  |                                                                                                                  |                                                                                                                  |
|                                                      |                        |                                                          | Access the IP address and NodePort on a node other than the node where the server is located: The access is successful. |                                                                                                                  | Access the IP address and NodePort on a node other than the node where the server is located: The access failed. |                                                                                                                  |
+------------------------------------------------------+------------------------+----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
|                                                      |                        | Other containers on different nodes from the service pod | Access the IP address and NodePort on the node where the server is located: The access is successful.                   | Access the IP address and NodePort on the node where the server is located: The access is successful.            | Access the IP address and NodePort on the node where the server is located: The access is successful.            | Access the IP address and NodePort on the node where the server is located: The access is successful.            |
|                                                      |                        |                                                          |                                                                                                                         |                                                                                                                  |                                                                                                                  |                                                                                                                  |
|                                                      |                        |                                                          | Access the IP address and NodePort on a node other than the node where the server is located: The access failed.        | Access the IP address and NodePort on a node other than the node where the server is located: The access failed. | Access the IP address and NodePort on a node other than the node where the server is located: The access failed. | Access the IP address and NodePort on a node other than the node where the server is located: The access failed. |
+------------------------------------------------------+------------------------+----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
| LoadBalancer Service using a dedicated load balancer | Private network        | Same node as the service pod                             | The access failed.                                                                                                      | The access failed.                                                                                               | The access failed.                                                                                               | The access failed.                                                                                               |
+------------------------------------------------------+------------------------+----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
|                                                      |                        | Other containers on the same node as the service pod     | The access failed.                                                                                                      | The access failed.                                                                                               | The access failed.                                                                                               | The access failed.                                                                                               |
+------------------------------------------------------+------------------------+----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+

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
