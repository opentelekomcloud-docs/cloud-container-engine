:original_name: ShowNodePoolConfigurations.html

.. _ShowNodePoolConfigurations:

Obtaining the Parameters That Can Be Configured for a Node Pool
===============================================================

Function
--------

This API is used to obtain the parameters that can be configured for a node pool.

URI
---

GET /api/v3/projects/{project_id}/clusters/{cluster_id}/nodepools/{nodepool_id}/configuration

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
   | nodepool_id     | Yes             | String          | **Details**:                                                                                                             |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | Node pool ID. For details about how to obtain the ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`.  |
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

   +------------+---------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------+
   | Parameter  | Type                                                                                                                                  | Description                                        |
   +============+=======================================================================================================================================+====================================================+
   | apiVersion | String                                                                                                                                | API version                                        |
   +------------+---------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------+
   | kind       | String                                                                                                                                | API type. The value is fixed at **Configuration**. |
   +------------+---------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------+
   | metadata   | :ref:`ConfigurationMetadata <shownodepoolconfigurations__en-us_topic_0000002319296917_response_configurationmetadata>` object         | Configuration metadata                             |
   +------------+---------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------+
   | spec       | :ref:`ClusterConfigurationsSpec <shownodepoolconfigurations__en-us_topic_0000002319296917_response_clusterconfigurationsspec>` object | Configuration specifications                       |
   +------------+---------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------+
   | status     | Object                                                                                                                                | Configuration status                               |
   +------------+---------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------+

.. _shownodepoolconfigurations__en-us_topic_0000002319296917_response_configurationmetadata:

.. table:: **Table 4** ConfigurationMetadata

   +-----------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Type                  | Description                                                                                                                                                                                                                                                              |
   +=======================+=======================+==========================================================================================================================================================================================================================================================================+
   | name                  | String                | Configuration name                                                                                                                                                                                                                                                       |
   +-----------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | labels                | Map<String,String>    | A configuration label in a key-value pair.                                                                                                                                                                                                                               |
   |                       |                       |                                                                                                                                                                                                                                                                          |
   |                       |                       | -  **Key**: Enter 1 to 63 characters starting with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed. A DNS subdomain containing a maximum of 253 characters (such as **example.com/my-key**) can be prefixed to a key. |
   |                       |                       |                                                                                                                                                                                                                                                                          |
   |                       |                       | -  **Value**: The value can be left blank or contain 1 to 63 characters that start and end with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed.                                                                      |
   |                       |                       |                                                                                                                                                                                                                                                                          |
   |                       |                       | Example: "foo": "bar"                                                                                                                                                                                                                                                    |
   +-----------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _shownodepoolconfigurations__en-us_topic_0000002319296917_response_clusterconfigurationsspec:

.. table:: **Table 5** ClusterConfigurationsSpec

   +-----------+---------------------------------------------------------------------------------------------------------------+--------------------------------------+
   | Parameter | Type                                                                                                          | Description                          |
   +===========+===============================================================================================================+======================================+
   | packages  | Array of :ref:`packages <shownodepoolconfigurations__en-us_topic_0000002319296917_response_packages>` objects | Component configuration item details |
   +-----------+---------------------------------------------------------------------------------------------------------------+--------------------------------------+

.. _shownodepoolconfigurations__en-us_topic_0000002319296917_response_packages:

.. table:: **Table 6** packages

   +----------------+---------------------------------------------------------------------------------------------------------------------------------+-------------------------------+
   | Parameter      | Type                                                                                                                            | Description                   |
   +================+=================================================================================================================================+===============================+
   | name           | String                                                                                                                          | Component name                |
   +----------------+---------------------------------------------------------------------------------------------------------------------------------+-------------------------------+
   | configurations | Array of :ref:`ConfigurationItem <shownodepoolconfigurations__en-us_topic_0000002319296917_response_configurationitem>` objects | Component configuration items |
   +----------------+---------------------------------------------------------------------------------------------------------------------------------+-------------------------------+

.. _shownodepoolconfigurations__en-us_topic_0000002319296917_response_configurationitem:

.. table:: **Table 7** ConfigurationItem

   +-----------------------+-----------------------+---------------------------------------------------------------------------------------------------------+
   | Parameter             | Type                  | Description                                                                                             |
   +=======================+=======================+=========================================================================================================+
   | name                  | String                | **Details**:                                                                                            |
   |                       |                       |                                                                                                         |
   |                       |                       | Configuration overrides of the default components in a cluster.                                         |
   |                       |                       |                                                                                                         |
   |                       |                       | **Constraints**:                                                                                        |
   |                       |                       |                                                                                                         |
   |                       |                       | If you specify a component or parameter that is not supported, this configuration item will be ignored. |
   |                       |                       |                                                                                                         |
   |                       |                       | **Options**:                                                                                            |
   |                       |                       |                                                                                                         |
   |                       |                       | N/A                                                                                                     |
   |                       |                       |                                                                                                         |
   |                       |                       | **Default value**:                                                                                      |
   |                       |                       |                                                                                                         |
   |                       |                       | N/A                                                                                                     |
   +-----------------------+-----------------------+---------------------------------------------------------------------------------------------------------+
   | value                 | AnyType               | **Details**:                                                                                            |
   |                       |                       |                                                                                                         |
   |                       |                       | Configuration overrides of the default components in a cluster.                                         |
   |                       |                       |                                                                                                         |
   |                       |                       | **Constraints**:                                                                                        |
   |                       |                       |                                                                                                         |
   |                       |                       | If you specify a component or parameter that is not supported, this configuration item will be ignored. |
   |                       |                       |                                                                                                         |
   |                       |                       | **Options**:                                                                                            |
   |                       |                       |                                                                                                         |
   |                       |                       | N/A                                                                                                     |
   |                       |                       |                                                                                                         |
   |                       |                       | **Default value**:                                                                                      |
   |                       |                       |                                                                                                         |
   |                       |                       | N/A                                                                                                     |
   +-----------------------+-----------------------+---------------------------------------------------------------------------------------------------------+

Example Requests
----------------

None

Example Responses
-----------------

**Status code: 200**

The parameter configurations of the node pool are obtained successfully.

.. code-block::

   {
     "kind" : "Configuration",
     "apiVersion" : "v3",
     "metadata" : {
       "name" : "configuration",
       "labels" : {
         "nodepool_id" : "61de338d-a1f9-11ed-8891-0255ac100036"
       }
     },
     "spec" : {
       "packages" : [ {
         "name" : "kube-apiserver",
         "configurations" : [ {
           "name" : "event-rate-limit-qps",
           "value" : 200
         }, {
           "name" : "support-overload",
           "value" : false
         } ]
       }, {
         "name" : "kube-scheduler",
         "configurations" : [ {
           "name" : "kube-api-qps",
           "value" : 100
         }, {
           "name" : "default-scheduler",
           "value" : "kube-scheduler"
         } ]
       } ]
     },
     "status" : { }
   }

Status Codes
------------

+-------------+--------------------------------------------------------------------------+
| Status Code | Description                                                              |
+=============+==========================================================================+
| 200         | The parameter configurations of the node pool are obtained successfully. |
+-------------+--------------------------------------------------------------------------+

Error Codes
-----------

See :ref:`Error Codes <cce_02_0250>`.
