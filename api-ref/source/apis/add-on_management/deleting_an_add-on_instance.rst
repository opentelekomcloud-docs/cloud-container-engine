:original_name: cce_02_0324.html

.. _cce_02_0324:

Deleting an Add-on Instance
===========================

Function
--------

This API is used to delete an add-on instance.

.. note::

   The URL for add-on management is in the format of **https://{clusterid}.Endpoint/uri.** In the URL, *{clusterid}* indicates the cluster ID, and *uri* indicates the resource path, that is, the path for API access.

URI
---

DELETE /api/v3/addons/{id}?cluster_id={cluster_id}

:ref:`Table 1 <cce_02_0324__table493910491373>` describes the parameters of this API.

.. _cce_02_0324__table493910491373:

.. table:: **Table 1** Parameter description

   +------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Mandatory | Type   | Description                                                                                                                   |
   +============+===========+========+===============================================================================================================================+
   | cluster_id | Yes       | String | Cluster ID. For details about how to obtain the cluster ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------------+
   | id         | Yes       | String | Add-on instance ID. For details about how to obtain the ID, see :ref:`Table 4 <cce_02_0323__table12181142512286>`.            |
   +------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------------+

Request
-------

**Request parameters:**

:ref:`Table 2 <cce_02_0324__table061744914219>` list the request parameters.

.. _cce_02_0324__table061744914219:

.. table:: **Table 2** Parameters in the request header

   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Mandatory             | Description                                                                                                                                                                                                                                                                   |
   +=======================+=======================+===============================================================================================================================================================================================================================================================================+
   | Content-Type          | Yes                   | Message body type (format).                                                                                                                                                                                                                                                   |
   |                       |                       |                                                                                                                                                                                                                                                                               |
   |                       |                       | Default: **application/json**                                                                                                                                                                                                                                                 |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | X-Auth-Token          | Yes                   | Requests for calling an API can be authenticated using either a token or AK/SK. If token-based authentication is used, this parameter is mandatory and must be set to a user token. For details on how to obtain a user token, see :ref:`API Usage Guidelines <cce_02_0344>`. |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

**Example request:**

N/A

Response
--------

**Response parameters:**

N/A

**Example response:**

.. code-block::

   success

Status Codes
------------

.. table:: **Table 3** Status codes

   =========== ===========
   Status Code Description
   =========== ===========
   200         OK
   =========== ===========

For the description about error status codes, see :ref:`Status Code <cce_02_0084>`.
