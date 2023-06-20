:original_name: cce_10_0249.html

.. _cce_10_0249:

Service Overview
================

Direct Access to a Pod
----------------------

After a pod is created, the following problems may occur if you directly access the pod:

-  The pod can be deleted and recreated at any time by a controller such as a Deployment, and the result of accessing the pod becomes unpredictable.
-  The IP address of the pod is allocated only after the pod is started. Before the pod is started, the IP address of the pod is unknown.
-  An application is usually composed of multiple pods that run the same image. Accessing pods one by one is not efficient.

For example, an application uses Deployments to create the frontend and backend. The frontend calls the backend for computing, as shown in :ref:`Figure 1 <cce_10_0249__en-us_topic_0249851121_fig2173165051811>`. Three pods are running in the backend, which are independent and replaceable. When a backend pod is re-created, the new pod is assigned with a new IP address, of which the frontend pod is unaware.

.. _cce_10_0249__en-us_topic_0249851121_fig2173165051811:

.. figure:: /_static/images/en-us_image_0000001517743624.png
   :alt: **Figure 1** Inter-pod access

   **Figure 1** Inter-pod access

Using Services for Pod Access
-----------------------------

Kubernetes Services are used to solve the preceding pod access problems. A Service has a fixed IP address. (When a CCE cluster is created, a Service CIDR block is set, which is used to allocate IP addresses to Services.) A Service forwards requests accessing the Service to pods based on labels, and at the same time, perform load balancing for these pods.

In the preceding example, a Service is added for the frontend pod to access the backend pods. In this way, the frontend pod does not need to be aware of the changes on backend pods, as shown in :ref:`Figure 2 <cce_10_0249__en-us_topic_0249851121_fig163156154816>`.

.. _cce_10_0249__en-us_topic_0249851121_fig163156154816:

.. figure:: /_static/images/en-us_image_0000001517743432.png
   :alt: **Figure 2** Accessing pods through a Service

   **Figure 2** Accessing pods through a Service

Service Types
-------------

Kubernetes allows you to specify a Service of a required type. The values and actions of different types of Services are as follows:

-  :ref:`ClusterIP <cce_10_0011>`

   A ClusterIP Service allows workloads in the same cluster to use their cluster-internal domain names to access each other.

-  :ref:`NodePort <cce_10_0142>`

   A NodePort Service is exposed on each node's IP at a static port. A ClusterIP Service, to which the NodePort Service routes, is automatically created. By requesting <*NodeIP*>:<*NodePort*>, you can access a NodePort Service from outside the cluster.

-  :ref:`LoadBalancer <cce_10_0014>`

   A workload can be accessed from public networks through a load balancer, which is more secure and reliable than EIP.
