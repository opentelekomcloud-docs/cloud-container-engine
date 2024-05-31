:original_name: cce_02_0004.html

.. _cce_02_0004:

API Usage Guidelines
====================

Cloud APIs comply with the RESTful API design principles. REST-based web services are organized into resources. Each resource is identified by one or more Uniform Resource Identifiers (URIs). An application accesses a resource based on the resource's Unified Resource Locator (URL). A URL is usually in the following format: *https://Endpoint/uri*. In the URL, *uri* indicates the resource path, that is, the API access path.

Cloud APIs use HTTPS as the transmission protocol. Requests/Responses are transmitted by using JSON messages, with media type represented by **Application/json**.

-  The URL of APIs described in :ref:`Cluster Management <cce_02_0092>` is in the format of *https://Endpoint/uri*. In the URL, uri indicates the resource path, that is, the API access path. Use X-Auth-Token as a header.

-  The URL of Kubernetes-native APIs described in :ref:`Add-on Management <cce_02_0320>` and :ref:`Kubernetes APIs <cce_02_0340>` is in the format of **https://{clusterid}.Endpoint/uri**. In the URL, {clusterid} indicates a cluster ID, and uri indicates the resource path, that is, the API access path. Use X-Auth-Token as a header.

-  The URL of Kubernetes-native APIs is in the format of **https://{publicip}:5443/uri**. In the URL, **{publicip}** indicates EIP of the cluster, and **uri** indicates the resource path, that is, the API access path. Use X-Remote-User or Authorization as a header.

   Before using X-Remote-User as a header, obtain the required certificate in advance. Two types of certificates are supported:

   -  Self-owned certificate uploaded during cluster creation. For details, see `Creating a Cluster <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0028.html>`__.

      |image1|

   -  Cluster certificate generated and downloaded after cluster creation. For details, see `Obtaining a Cluster Certificate <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0175.html>`__.

      |image2|

For details about how to use APIs, see `API Usage Guidelines <https://docs.otc.t-systems.com/en-us/api/apiug/apig-en-api-180328001.html?tag=API%20Documents>`__.

CCE provides two methods to authenticate requests for calling an API: token and AK/SK. Select an authentication method based on actual requirements. If token-based authentication is used, you can call service APIs by using either of the following methods after obtaining a token:

-  Method 1: Add **X-Auth-Token** to the request header and set **X-Auth-Token: ${token}** with the obtained IAM token.
-  Method 2: Add **Authorization** to the request header and set **Authorization: Bearer ${token}** with the obtained IAM token or token from Kubernetes service account.
-  Method 3: Add **X-Remote-User** to the request header and set **X-Remote-User**: **user** with a valid certificate.

.. note::

   Method 3 requires that the CA root certificate must has been uploaded before you create a cluster on the CCE console.

.. |image1| image:: /_static/images/en-us_image_0000001854551680.png
.. |image2| image:: /_static/images/en-us_image_0000001868229221.png
