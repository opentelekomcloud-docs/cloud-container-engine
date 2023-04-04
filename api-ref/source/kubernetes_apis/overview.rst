:original_name: kubernetesapi.html

.. _kubernetesapi:

Overview
========

Description
-----------

You can use Kubernetes native APIs to manage your Kubernetes clusters. For details, see `Kubernetes API Concepts <https://kubernetes.io/docs/reference/using-api/api-concepts/>`__.

To find detailed API definitions for different Kubernetes versions, visit the following links:

-  1.19: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/
-  1.21: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.21/
-  1.23: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/
-  1.25: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/

Based on the open source Kubernetes APIs, CCE enhances and adapts following functions.

-  Ingress: For details, see `Using kubectl to Create an ELB Ingress <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0252.html>`__.
-  PV and PVC: For details, see `PV <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0379.html>`__ and `PVC <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0378.html>`__.

   -  EVS: For details, see `Creating a Pod Mounted with an EVS Volume <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0257.html>`__.
   -  SFS Turbo: For details, see `Creating a Deployment Mounted with an SFS Turbo Volume <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0274.html>`__ and `Creating a StatefulSet Mounted with an SFS Turbo Volume <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0273.html>`__.
   -  OBS: For details, see `Creating a Deployment Mounted with an OBS Volume <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0269.html>`__ and `Creating a StatefulSet Mounted with an OBS Volume <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0268.html>`__.
   -  SFS: For details, see `Creating a Deployment Mounted with an SFS Volume <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0263.html>`__ and `Creating a StatefulSet Mounted with an SFS Volume <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0262.html>`__.

-  Network Policies: For details, see `Network Policies <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0059.html>`__.

Calling Kubernetes APIs Through API Gateway
-------------------------------------------

You can call Kubernetes native APIs through API Gateway using the URL in the format of **https://{clusterid}.Endpoint/uri**. In the URL, *{clusterid}* indicates the cluster ID, and *uri* indicates the resource path, that is, the path for API access.

.. table:: **Table 1** URL parameters

   +-------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter   | Description                                                                                                                                        |
   +=============+====================================================================================================================================================+
   | {clusterid} | Cluster ID. After a cluster is created, call the :ref:`API for obtaining a cluster in a specified project <cce_02_0239>` to obtain the cluster ID. |
   +-------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | Endpoint    | Web service entry URL. It can be obtained from `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.               |
   +-------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | uri         | Path in which the resource requested by the API is located. You can obtain the path from the URI of the API.                                       |
   +-------------+----------------------------------------------------------------------------------------------------------------------------------------------------+

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
