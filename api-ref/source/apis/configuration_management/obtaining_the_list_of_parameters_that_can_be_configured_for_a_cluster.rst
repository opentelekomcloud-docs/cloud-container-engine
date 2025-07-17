:original_name: ShowClusterConfigurationDetails.html

.. _ShowClusterConfigurationDetails:

Obtaining the List of Parameters That Can Be Configured for a Cluster
=====================================================================

Function
--------

This API is used to obtain the list of parameters that can be configured for a CCE cluster.

URI
---

GET /api/v3/projects/{project_id}/clusters/{cluster_id}/configuration/detail

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

   +--------------------+----------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------+
   | Parameter          | Type                                                                                                                             | Description                                                               |
   +====================+==================================================================================================================================+===========================================================================+
   | {User defined key} | Map<String,Array<:ref:`PackageOptions <showclusterconfigurationdetails__en-us_topic_0000002284800664_response_packageoptions>`>> | Response body for obtaining the parameter configuration list of a cluster |
   +--------------------+----------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------+

.. _showclusterconfigurationdetails__en-us_topic_0000002284800664_response_packageoptions:

.. table:: **Table 4** PackageOptions

   +-----------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Type                  | Description                                                                                                                                                         |
   +=======================+=======================+=====================================================================================================================================================================+
   | name                  | String                | Parameter name                                                                                                                                                      |
   +-----------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | default               | Object                | Default parameter value. If this parameter is not specified, the default value will take effect. The returned parameter can be of integer, string, or Boolean type. |
   +-----------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | validAt               | String                | How parameters will take effect.                                                                                                                                    |
   |                       |                       |                                                                                                                                                                     |
   |                       |                       | -  **static**: The parameters take effect when the cluster or node pool is being created and cannot be changed later.                                               |
   |                       |                       |                                                                                                                                                                     |
   |                       |                       | -  **immediately**: The parameters can be modified when the cluster or node pool is running, and the modification takes effect immediately.                         |
   +-----------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | empty                 | Boolean               | Whether the configuration item can be empty. Options:                                                                                                               |
   |                       |                       |                                                                                                                                                                     |
   |                       |                       | -  **true**: If this parameter is set to **true**, no value will take effect.                                                                                       |
   |                       |                       |                                                                                                                                                                     |
   |                       |                       | -  **false**: If this parameter is set to **false**, the default value will take effect.                                                                            |
   +-----------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | schema                | String                | Parameter category                                                                                                                                                  |
   +-----------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | type                  | String                | Parameter type                                                                                                                                                      |
   +-----------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Example Requests
----------------

None

Example Responses
-----------------

**Status code: 200**

A list of parameters that can be configured for a cluster is obtained successfully.

.. code-block::

   {
     "kube-apiserver" : [ {
       "name" : "default-not-ready-toleration-seconds",
       "default" : 300,
       "validAt" : "immediately",
       "empty" : true,
       "schema" : "kubernetes",
       "type" : "int"
     } ]
   }

Status Codes
------------

+-------------+-------------------------------------------------------------------------------------+
| Status Code | Description                                                                         |
+=============+=====================================================================================+
| 200         | A list of parameters that can be configured for a cluster is obtained successfully. |
+-------------+-------------------------------------------------------------------------------------+

Error Codes
-----------

See :ref:`Error Codes <cce_02_0250>`.
