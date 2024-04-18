:original_name: RemoveNode.html

.. _RemoveNode:

Removing a Node
===============

Function
--------

This API is used to remove a node from a specified cluster.

.. note::

   The URL for cluster management is in the format of https://Endpoint/uri. In the URL, **uri** indicates the resource path, that is, the path for API access.

URI
---

PUT /api/v3/projects/{project_id}/clusters/{cluster_id}/nodes/operation/remove

.. table:: **Table 1** Path Parameters

   +------------+-----------+--------+--------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Mandatory | Type   | Description                                                                                                              |
   +============+===========+========+==========================================================================================================================+
   | project_id | Yes       | String | Project ID. For details about how to obtain the value, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+--------+--------------------------------------------------------------------------------------------------------------------------+
   | cluster_id | Yes       | String | Cluster ID. For details about how to obtain the value, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+--------+--------------------------------------------------------------------------------------------------------------------------+

Request Parameters
------------------

.. table:: **Table 2** Request header parameters

   +--------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter    | Mandatory | Type   | Description                                                                                                                                                                                                                                     |
   +==============+===========+========+=================================================================================================================================================================================================================================================+
   | Content-Type | Yes       | String | Message body type (format).                                                                                                                                                                                                                     |
   +--------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | X-Auth-Token | Yes       | String | Requests for calling an API can be authenticated using either a token or AK/SK. If token-based authentication is used, this parameter is mandatory and must be set to a user token. For details, see :ref:`API Usage Guidelines <cce_02_0004>`. |
   +--------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. table:: **Table 3** Request body parameters

   +------------+-----------+--------------------------------------------------------------------------------------------------+------------------------------------------------------+
   | Parameter  | Mandatory | Type                                                                                             | Description                                          |
   +============+===========+==================================================================================================+======================================================+
   | apiVersion | No        | String                                                                                           | API version. The value is fixed at **v3**.           |
   +------------+-----------+--------------------------------------------------------------------------------------------------+------------------------------------------------------+
   | kind       | No        | String                                                                                           | API type. The value is fixed at **RemoveNodesTask**. |
   +------------+-----------+--------------------------------------------------------------------------------------------------+------------------------------------------------------+
   | spec       | Yes       | :ref:`RemoveNodesSpec <removenode__en-us_topic_0000001417833320_request_removenodesspec>` object | Configuration information.                           |
   +------------+-----------+--------------------------------------------------------------------------------------------------+------------------------------------------------------+

.. _removenode__en-us_topic_0000001417833320_request_removenodesspec:

.. table:: **Table 4** RemoveNodesSpec

   +-----------+-----------+----------------------------------------------------------------------------------------------+-------------------------------+
   | Parameter | Mandatory | Type                                                                                         | Description                   |
   +===========+===========+==============================================================================================+===============================+
   | login     | Yes       | :ref:`Login <removenode__en-us_topic_0000001417833320_request_login>` object                 | Node login mode.              |
   +-----------+-----------+----------------------------------------------------------------------------------------------+-------------------------------+
   | nodes     | Yes       | Array of :ref:`NodeItem <removenode__en-us_topic_0000001417833320_request_nodeitem>` objects | List of nodes to be operated. |
   +-----------+-----------+----------------------------------------------------------------------------------------------+-------------------------------+

.. _removenode__en-us_topic_0000001417833320_request_login:

.. table:: **Table 5** Login

   +-----------------+-----------------+-----------------+---------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                             |
   +=================+=================+=================+=========================================================+
   | sshKey          | No              | String          | Name of the key pair used for login.                    |
   +-----------------+-----------------+-----------------+---------------------------------------------------------+
   | userPassword    | No              | String          | Password used for node login.                           |
   |                 |                 |                 |                                                         |
   |                 |                 |                 | .. note::                                               |
   |                 |                 |                 |                                                         |
   |                 |                 |                 |    This field is not supported for the current version. |
   +-----------------+-----------------+-----------------+---------------------------------------------------------+

.. _removenode__en-us_topic_0000001417833320_request_nodeitem:

.. table:: **Table 6** NodeItem

   ========= ========= ====== ===========
   Parameter Mandatory Type   Description
   ========= ========= ====== ===========
   uid       Yes       String Node ID.
   ========= ========= ====== ===========

Response Parameters
-------------------

**Status code: 200**

.. table:: **Table 7** Response body parameters

   +------------+---------------------------------------------------------------------------------------------------+------------------------------------------------------+
   | Parameter  | Type                                                                                              | Description                                          |
   +============+===================================================================================================+======================================================+
   | apiVersion | String                                                                                            | API version. The value is fixed at **v3**.           |
   +------------+---------------------------------------------------------------------------------------------------+------------------------------------------------------+
   | kind       | String                                                                                            | API type. The value is fixed at **RemoveNodesTask**. |
   +------------+---------------------------------------------------------------------------------------------------+------------------------------------------------------+
   | spec       | :ref:`RemoveNodesSpec <removenode__en-us_topic_0000001417833320_response_removenodesspec>` object | Configuration information.                           |
   +------------+---------------------------------------------------------------------------------------------------+------------------------------------------------------+
   | status     | :ref:`TaskStatus <removenode__en-us_topic_0000001417833320_response_taskstatus>` object           | Job status.                                          |
   +------------+---------------------------------------------------------------------------------------------------+------------------------------------------------------+

.. _removenode__en-us_topic_0000001417833320_response_removenodesspec:

.. table:: **Table 8** RemoveNodesSpec

   +-----------+-----------------------------------------------------------------------------------------------+-------------------------------+
   | Parameter | Type                                                                                          | Description                   |
   +===========+===============================================================================================+===============================+
   | login     | :ref:`Login <removenode__en-us_topic_0000001417833320_response_login>` object                 | Node login mode.              |
   +-----------+-----------------------------------------------------------------------------------------------+-------------------------------+
   | nodes     | Array of :ref:`NodeItem <removenode__en-us_topic_0000001417833320_response_nodeitem>` objects | List of nodes to be operated. |
   +-----------+-----------------------------------------------------------------------------------------------+-------------------------------+

.. _removenode__en-us_topic_0000001417833320_response_login:

.. table:: **Table 9** Login

   +-----------------------+-----------------------+---------------------------------------------------------+
   | Parameter             | Type                  | Description                                             |
   +=======================+=======================+=========================================================+
   | sshKey                | String                | Name of the key pair used for login.                    |
   +-----------------------+-----------------------+---------------------------------------------------------+
   | userPassword          | String                | Password used for node login.                           |
   |                       |                       |                                                         |
   |                       |                       | .. note::                                               |
   |                       |                       |                                                         |
   |                       |                       |    This field is not supported for the current version. |
   +-----------------------+-----------------------+---------------------------------------------------------+

.. _removenode__en-us_topic_0000001417833320_response_nodeitem:

.. table:: **Table 10** NodeItem

   ========= ====== ===========
   Parameter Type   Description
   ========= ====== ===========
   uid       String Node ID.
   ========= ====== ===========

.. _removenode__en-us_topic_0000001417833320_response_taskstatus:

.. table:: **Table 11** TaskStatus

   +-----------+--------+----------------------------------------------------------------+
   | Parameter | Type   | Description                                                    |
   +===========+========+================================================================+
   | jobID     | String | Job ID, which is used by the caller to query the job progress. |
   +-----------+--------+----------------------------------------------------------------+

Example Requests
----------------

Removing a Node

.. code-block:: text

   PUT /api/v3/projects/{project_id}/clusters/{cluster_id}/nodes/operation/remove

   {
     "spec" : {
       "login" : {
         "sshKey" : "KeyPair-001"
       },
       "nodes" : [ {
         "uid" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       }, {
         "uid" : "yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy"
       } ]
     }
   }

Example Responses
-----------------

**Status code: 200**

The job for removing a node in a specified cluster is successfully delivered.

.. code-block::

   {
     "spec" : {
       "login" : {
         "sshKey" : "KeyPair-001"
       },
       "nodes" : [ {
         "uid" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       }, {
         "uid" : "yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy"
       } ]
     },
     "status" : {
       "jobID" : "2ec9b78d-9368-46f3-8f29-d1a95622a568"
     }
   }

Status Codes
------------

+-------------+-------------------------------------------------------------------------------+
| Status Code | Description                                                                   |
+=============+===============================================================================+
| 200         | The job for removing a node in a specified cluster is successfully delivered. |
+-------------+-------------------------------------------------------------------------------+

Error Codes
-----------

See :ref:`Error Codes <cce_02_0250>`.
