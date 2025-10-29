:original_name: BatchCreateClusterTags.html

.. _BatchCreateClusterTags:

Adding Resource Tags to a Specified Cluster in Batches
======================================================

Function
--------

This API is used to add resource tags for a specified cluster in batches.

.. note::

   -  Each cluster supports a maximum of 20 resource tags.

   -  This API is idempotent. If the to-be-added tag has the same tag key and tag value as an existing tag, the tag will be added. If the to-be-added tag has the same key but different value as an existing tag, the tag will overwrite the existing one.

URI
---

POST /api/v3/projects/{project_id}/clusters/{cluster_id}/tags/create

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

.. table:: **Table 3** Request body parameters

   +-----------+-----------+----------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------+
   | Parameter | Mandatory | Type                                                                                                           | Description                                                                                       |
   +===========+===========+================================================================================================================+===================================================================================================+
   | tags      | Yes       | Array of :ref:`ResourceTag <batchcreateclustertags__en-us_topic_0000002422066916_request_resourcetag>` objects | List of cluster resource tags to be created. Each cluster supports a maximum of 20 resource tags. |
   +-----------+-----------+----------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------+

.. _batchcreateclustertags__en-us_topic_0000002422066916_request_resourcetag:

.. table:: **Table 4** ResourceTag

   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                                                 |
   +=================+=================+=================+=============================================================================================================+
   | key             | No              | String          | **Details**:                                                                                                |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | Key                                                                                                         |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | **Constraints**:                                                                                            |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | None                                                                                                        |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | **Options**:                                                                                                |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | -  The value cannot be empty and cannot start or end with spaces. A maximum of 128 characters is supported. |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | -  The value can contain letters, digits, and spaces in UTF-8 format.                                       |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | -  The value can contain the following special characters: \_.:=+-@                                         |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | -  The value cannot start with **\_sys\_**.                                                                 |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | **Default value**:                                                                                          |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | N/A                                                                                                         |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------------+
   | value           | No              | String          | **Details**:                                                                                                |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | Value                                                                                                       |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | **Constraints**:                                                                                            |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | None                                                                                                        |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | **Options**:                                                                                                |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | -  The value can be null but not the default. Max characters: 255                                           |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | -  Letters, digits, and spaces in UTF-8 format are supported.                                               |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | -  The value can contain the following special characters: ``_.:/=+-@``                                     |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | **Default value**:                                                                                          |
   |                 |                 |                 |                                                                                                             |
   |                 |                 |                 | N/A                                                                                                         |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------------+

Response Parameters
-------------------

**Status code: 204**

No Content

None

Example Requests
----------------

Adding Resource Tags to a Specified Cluster in Batches

.. code-block:: text

   POST /api/v3/projects/{project_id}/clusters/{cluster_id}/tags/create

   {
     "tags" : [ {
       "key" : "key1",
       "value" : "value1"
     }, {
       "key" : "key2",
       "value" : "value3"
     } ]
   }

Example Responses
-----------------

None

Status Codes
------------

=========== ===========
Status Code Description
=========== ===========
204         No Content
=========== ===========

Error Codes
-----------

See :ref:`Error Codes <cce_02_0250>`.
