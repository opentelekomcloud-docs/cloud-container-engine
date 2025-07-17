:original_name: ResizeCluster.html

.. _ResizeCluster:

Modifying Cluster Specifications
================================

Function
--------

This API is used to modify the specifications of a cluster.

.. note::

   -  The URL for cluster management is in the format of **https://Endpoint/uri**, in which *uri* indicates the resource path, that is, the path for API access.

URI
---

POST /api/v3/projects/{project_id}/clusters/{cluster_id}/operation/resize

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

   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                                                                                                                                                                                     |
   +=================+=================+=================+=================================================================================================================================================================================================================================================+
   | Content-Type    | Yes             | String          | **Details**:                                                                                                                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | The request body type or format                                                                                                                                                                                                                 |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | **Constraints**:                                                                                                                                                                                                                                |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | The GET method is not verified.                                                                                                                                                                                                                 |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | **Options**:                                                                                                                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | -  application/json                                                                                                                                                                                                                             |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | -  application/json;charset=utf-8                                                                                                                                                                                                               |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | -  application/x-pem-file                                                                                                                                                                                                                       |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | -  multipart/form-data (used when the **FormData** parameter is present)                                                                                                                                                                        |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | **Default value**:                                                                                                                                                                                                                              |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | N/A                                                                                                                                                                                                                                             |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | X-Auth-Token    | Yes             | String          | **Details**:                                                                                                                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | Requests for calling an API can be authenticated using either a token or AK/SK. If token-based authentication is used, this parameter is mandatory and must be set to a user token. For details, see :ref:`API Usage Guidelines <cce_02_0004>`. |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | **Constraints**:                                                                                                                                                                                                                                |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | None                                                                                                                                                                                                                                            |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | **Options**:                                                                                                                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | N/A                                                                                                                                                                                                                                             |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | **Default value**:                                                                                                                                                                                                                              |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | N/A                                                                                                                                                                                                                                             |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. table:: **Table 3** Request body parameters

   +-----------------+-----------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type                                                                                        | Description                                                                                                                                                                                                                                                                                                                                                                                                         |
   +=================+=================+=============================================================================================+=====================================================================================================================================================================================================================================================================================================================================================================================================================+
   | flavorResize    | Yes             | String                                                                                      | The target flavor to be changed. You can only adjust the maximum number of worker nodes in a cluster. The number of master nodes is fixed and cannot be altered, and the cluster flavors cannot be downgraded. Suppose the original cluster flavor is **cce.s2.medium**. In that case, you can only upgrade it to **cce.s2.large** or higher, but you cannot downgrade it to **cce.s2.small** or **cce.s1.medium**. |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             | -  **cce.s1.small**: a small-scale CCE cluster with one master node and a maximum of 50 worker nodes                                                                                                                                                                                                                                                                                                                |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             | -  **cce.s1.medium**: a medium-scale CCE cluster with one master node and a maximum of 200 worker nodes                                                                                                                                                                                                                                                                                                             |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             | -  **cce.s2.small**: a small-scale CCE cluster with three master nodes and a maximum of 50 worker nodes                                                                                                                                                                                                                                                                                                             |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             | -  **cce.s2.medium**: a medium-scale CCE cluster with three master nodes and a maximum of 200 worker nodes                                                                                                                                                                                                                                                                                                          |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             | -  **cce.s2.large**: a large-scale CCE cluster with three master nodes and a maximum of 1,000 worker nodes                                                                                                                                                                                                                                                                                                          |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             | -  **cce.s2.xlarge**: an ultra-large-scale CCE cluster with three master nodes and a maximum of 2,000 worker nodes                                                                                                                                                                                                                                                                                                  |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             | .. note::                                                                                                                                                                                                                                                                                                                                                                                                           |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             |    The fields in the parameters are described as follows:                                                                                                                                                                                                                                                                                                                                                           |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             |    -  **s1**: specifies a cluster with one master node. If the master node is faulty, the cluster will become unavailable, but running workloads in the cluster are not affected.                                                                                                                                                                                                                                   |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             |    -  **s2**: specifies an HA cluster with three master nodes. If one of the master nodes is faulty, the cluster is still available.                                                                                                                                                                                                                                                                                |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             |    -  **small**: specifies that a cluster can manage a maximum of 50 worker nodes.                                                                                                                                                                                                                                                                                                                                  |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             |    -  **medium**: specifies that a cluster can manage a maximum of 200 worker nodes.                                                                                                                                                                                                                                                                                                                                |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             |    -  **large**: specifies that a cluster can manage a maximum of 1,000 worker nodes.                                                                                                                                                                                                                                                                                                                               |
   |                 |                 |                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                                             |    -  **xlarge**: specifies that a cluster can manage a maximum of 2,000 worker nodes.                                                                                                                                                                                                                                                                                                                              |
   +-----------------+-----------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | extendParam     | No              | :ref:`extendParam <resizecluster__en-us_topic_0000002284800612_request_extendparam>` object | Extended field for changing cluster specifications                                                                                                                                                                                                                                                                                                                                                                  |
   +-----------------+-----------------+---------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _resizecluster__en-us_topic_0000002284800612_request_extendparam:

.. table:: **Table 4** extendParam

   +-----------------+-----------+--------+-------------------------------------------------------------------+
   | Parameter       | Mandatory | Type   | Description                                                       |
   +=================+===========+========+===================================================================+
   | decMasterFlavor | No        | String | Specifications of the master node in the dedicated hybrid cluster |
   +-----------------+-----------+--------+-------------------------------------------------------------------+

Response Parameters
-------------------

**Status code: 201**

.. table:: **Table 5** Response body parameters

   ========= ====== ===========
   Parameter Type   Description
   ========= ====== ===========
   jobID     String Job ID
   ========= ====== ===========

Example Requests
----------------

-  Modifying the specifications of the pay-per-use cluster

   .. code-block:: text

      POST /api/v3/projects/{project_id}/clusters/{cluster_id}/operation/resize

      {
        "flavorResize" : "cce.s1.medium"
      }

Example Responses
-----------------

**Status code: 201**

The job for modifying the specifications of the pay-per-use cluster is delivered.

.. code-block::

   {
     "jobID" : "13b8d958-8fcf-11ed-aef3-0255ac1001bd"
   }

Status Codes
------------

+-------------+-----------------------------------------------------------------------------------+
| Status Code | Description                                                                       |
+=============+===================================================================================+
| 201         | The job for modifying the specifications of the pay-per-use cluster is delivered. |
+-------------+-----------------------------------------------------------------------------------+

Error Codes
-----------

See :ref:`Error Codes <cce_02_0250>`.
