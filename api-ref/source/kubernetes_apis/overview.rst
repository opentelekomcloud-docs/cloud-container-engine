:original_name: kubernetesapi.html

.. _kubernetesapi:

Overview
========

Description
-----------

Kubernetes APIs are resource-based (RESTful) programming interfaces provided through HTTP. It supports query, creation, update, and deletion of various cluster resources using standard HTTP request methods (POST, PUT, PATCH, DELETE, and GET).

CCE allows you to use native `Kubernetes APIs <https://kubernetes.io/docs/reference/kubernetes-api/>`__ in the following ways:

-  :ref:`Calling Kubernetes APIs Through API Gateway <kubernetesapi__section41207155509>`
-  :ref:`Calling Kubernetes APIs Through the API Server <kubernetesapi__section14674391312>`

.. _kubernetesapi__section41207155509:

Calling Kubernetes APIs Through API Gateway
-------------------------------------------

You can call Kubernetes native APIs through API Gateway using the URL in the format of **https://{clusterid}.Endpoint/uri**. In the URL, *{clusterid}* indicates the cluster ID, and *uri* indicates the resource path, that is, the path for API access.

.. table:: **Table 1** URL parameters

   +-------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter   | Description                                                                                                                                        |
   +=============+====================================================================================================================================================+
   | {clusterid} | Cluster ID. After a cluster is created, call the :ref:`API for obtaining a cluster in a specified project <cce_02_0239>` to obtain the cluster ID. |
   +-------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | Endpoint    | Web service entry URL. It can be obtained from `Regions and Endpoints <https://docs.otc.t-systems.com/regions-and-endpoints/index.html>`__.        |
   +-------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | uri         | Path in which the resource requested by the API is located. You can obtain the path from the URI of the API.                                       |
   +-------------+----------------------------------------------------------------------------------------------------------------------------------------------------+

.. _kubernetesapi__section14674391312:

Calling Kubernetes APIs Through the API Server
----------------------------------------------

You can use the API server of a Kubernetes cluster to call Kubernetes native APIs.

#. Call the :ref:`API for obtaining the cluster certificate <cce_02_0248>` to obtain the cluster certificates.

   There are three certificates:

   -  ca.crt
   -  client.crt
   -  client.key

#. Go to the cluster details page and obtain the API server address (private or public network address).

   With the certificates and API server address, you can call Kubernetes native APIs.

   For example, if you run the **curl** command to call the API to view the pod information, you only need to carry the certificate in the command as follows:

   **curl --cert ./client.crt --key ./client.key https://192.168.0.198:5443/api/v1/namespaces/default/pods/**
