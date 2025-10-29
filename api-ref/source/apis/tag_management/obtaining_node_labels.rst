:original_name: GetLabels.html

.. _GetLabels:

Obtaining Node Labels
=====================

Function
--------

This API is used to obtain the labels of all nodes in a cluster.

URI
---

GET /api/v3/projects/{project_id}/clusters/{cluster_id}/labels

.. table:: **Table 1** Path Parameters

   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                                                              |
   +=================+=================+=================+==========================================================================================================================+
   | project_id      | Yes             | String          | **Details**:                                                                                                             |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | Project ID. For details about how to obtain the value, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | **Constraints**:                                                                                                         |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | None                                                                                                                     |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | **Options**:                                                                                                             |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | Project IDs of the account                                                                                               |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | **Default value**:                                                                                                       |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | N/A                                                                                                                      |
   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------+
   | cluster_id      | Yes             | String          | **Details**:                                                                                                             |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | Cluster ID. For details about how to obtain the value, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | **Constraints**:                                                                                                         |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | None                                                                                                                     |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | **Options**:                                                                                                             |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | Cluster IDs                                                                                                              |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | **Default value**:                                                                                                       |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | N/A                                                                                                                      |
   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------+

Request Parameters
------------------

.. table:: **Table 2** Request header parameters

   +-----------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                                                                                                                                                                                       |
   +=================+=================+=================+===================================================================================================================================================================================================================================================+
   | Content-Type    | Yes             | String          | **Details**:                                                                                                                                                                                                                                      |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | The request body type or format                                                                                                                                                                                                                   |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | **Constraints**:                                                                                                                                                                                                                                  |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | The GET method is not verified.                                                                                                                                                                                                                   |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | **Options**:                                                                                                                                                                                                                                      |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | -  application/json                                                                                                                                                                                                                               |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | -  application/json;charset=utf-8                                                                                                                                                                                                                 |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | -  application/x-pem-file                                                                                                                                                                                                                         |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | -  multipart/form-data (used when the **FormData** parameter is present)                                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | **Default value**:                                                                                                                                                                                                                                |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | N/A                                                                                                                                                                                                                                               |
   +-----------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | X-Auth-Token    | Yes             | String          | **Details**:                                                                                                                                                                                                                                      |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | Requests for calling an API can be authenticated using either a token or AK/SK. If token-based authentication is used, this parameter is mandatory and must be set to a user token. For details, see :ref:`Obtaining a User Token <cce_02_0004>`. |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | **Constraints**:                                                                                                                                                                                                                                  |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | None                                                                                                                                                                                                                                              |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | **Options**:                                                                                                                                                                                                                                      |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | N/A                                                                                                                                                                                                                                               |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | **Default value**:                                                                                                                                                                                                                                |
   |                 |                 |                 |                                                                                                                                                                                                                                                   |
   |                 |                 |                 | N/A                                                                                                                                                                                                                                               |
   +-----------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Response Parameters
-------------------

**Status code: 200**

.. table:: **Table 3** Response body parameters

   +-----------------------+---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Type                                  | Description                                                                                                                                                                |
   +=======================+=======================================+============================================================================================================================================================================+
   | kind                  | String                                | **Definition**                                                                                                                                                             |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | API type                                                                                                                                                                   |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | **Constraints**                                                                                                                                                            |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | N/A                                                                                                                                                                        |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | **Range**                                                                                                                                                                  |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | N/A                                                                                                                                                                        |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | **Default Value**                                                                                                                                                          |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | Labels                                                                                                                                                                     |
   +-----------------------+---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | apiVersion            | String                                | **Definition**                                                                                                                                                             |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | API version                                                                                                                                                                |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | **Constraints**                                                                                                                                                            |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | N/A                                                                                                                                                                        |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | **Range**                                                                                                                                                                  |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | N/A                                                                                                                                                                        |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | **Default Value**                                                                                                                                                          |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | v3                                                                                                                                                                         |
   +-----------------------+---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | spec                  | Map<String,Map<String,Array<String>>> | **Definition**                                                                                                                                                             |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | Node label, which is classified by node pool.                                                                                                                              |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | -  **key** indicates the node pool ID. The value for the default node pool is **DefaultPool**.                                                                             |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | -  **value** indicates the label in the format of key-value pairs. **key** indicates the label key, and **value** indicates the value list corresponding to the label key. |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | **Constraints**                                                                                                                                                            |
   |                       |                                       |                                                                                                                                                                            |
   |                       |                                       | N/A                                                                                                                                                                        |
   +-----------------------+---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Example Requests
----------------

None

Example Responses
-----------------

**Status code: 200**

The node labels have been obtained.

.. code-block::

   {
     "kind" : "Labels",
     "apiVersion" : "v3",
     "spec" : {
       "nodePoolId" : {
         "key1" : [ "value1", "value2" ]
       },
       "DefaultPool" : {
         "key2" : [ "value1", "value2" ]
       }
     }
   }

Status Codes
------------

=========== ===================================
Status Code Description
=========== ===================================
200         The node labels have been obtained.
=========== ===================================

Error Codes
-----------

See :ref:`Error Codes <cce_02_0250>`.
