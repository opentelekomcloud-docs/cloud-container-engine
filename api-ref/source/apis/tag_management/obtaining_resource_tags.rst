:original_name: GetResourceTags.html

.. _GetResourceTags:

Obtaining Resource Tags
=======================

Function
--------

This API is used to obtain resource tags.

URI
---

GET /cce/v1/{project_id}/{resource_type}/{resource_id}/tags

.. table:: **Table 1** Path Parameters

   +-----------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                                                                                        |
   +=================+=================+=================+====================================================================================================================================================+
   | project_id      | Yes             | String          | **Details**:                                                                                                                                       |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | Project ID. For details about how to obtain the value, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`.                           |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | **Constraints**:                                                                                                                                   |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | None                                                                                                                                               |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | **Options**:                                                                                                                                       |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | Project IDs of the account                                                                                                                         |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | **Default value**:                                                                                                                                 |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | N/A                                                                                                                                                |
   +-----------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | resource_type   | Yes             | String          | **Definition**                                                                                                                                     |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | Resource type                                                                                                                                      |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | **Constraints**                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | N/A                                                                                                                                                |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | **Range**                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | -  **cce-cluster**: cluster                                                                                                                        |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | **Default Value**                                                                                                                                  |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | N/A                                                                                                                                                |
   +-----------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | resource_id     | Yes             | String          | **Definition**                                                                                                                                     |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | Resource ID, for example, cluster ID. For details about how to obtain the value, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | **Constraints**                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | N/A                                                                                                                                                |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | **Range**                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | N/A                                                                                                                                                |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | **Default Value**                                                                                                                                  |
   |                 |                 |                 |                                                                                                                                                    |
   |                 |                 |                 | N/A                                                                                                                                                |
   +-----------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------+

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

   +-----------------------+----------------------------------------------------------------------------------------------------------+-----------------------+
   | Parameter             | Type                                                                                                     | Description           |
   +=======================+==========================================================================================================+=======================+
   | tags                  | Array of :ref:`ResourceTag <getresourcetags__en-us_topic_0000002455466049_response_resourcetag>` objects | **Definition**        |
   |                       |                                                                                                          |                       |
   |                       |                                                                                                          | Custom tags           |
   |                       |                                                                                                          |                       |
   |                       |                                                                                                          | **Constraints**       |
   |                       |                                                                                                          |                       |
   |                       |                                                                                                          | N/A                   |
   +-----------------------+----------------------------------------------------------------------------------------------------------+-----------------------+
   | sys_tags              | Array of :ref:`ResourceTag <getresourcetags__en-us_topic_0000002455466049_response_resourcetag>` objects | **Definition**        |
   |                       |                                                                                                          |                       |
   |                       |                                                                                                          | System tags           |
   |                       |                                                                                                          |                       |
   |                       |                                                                                                          | **Constraints**       |
   |                       |                                                                                                          |                       |
   |                       |                                                                                                          | N/A                   |
   +-----------------------+----------------------------------------------------------------------------------------------------------+-----------------------+

.. _getresourcetags__en-us_topic_0000002455466049_response_resourcetag:

.. table:: **Table 4** ResourceTag

   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------+
   | Parameter             | Type                  | Description                                                                                                 |
   +=======================+=======================+=============================================================================================================+
   | key                   | String                | **Details**:                                                                                                |
   |                       |                       |                                                                                                             |
   |                       |                       | Key                                                                                                         |
   |                       |                       |                                                                                                             |
   |                       |                       | **Constraints**:                                                                                            |
   |                       |                       |                                                                                                             |
   |                       |                       | None                                                                                                        |
   |                       |                       |                                                                                                             |
   |                       |                       | **Options**:                                                                                                |
   |                       |                       |                                                                                                             |
   |                       |                       | -  The value cannot be empty and cannot start or end with spaces. A maximum of 128 characters is supported. |
   |                       |                       |                                                                                                             |
   |                       |                       | -  The value can contain letters, digits, and spaces in UTF-8 format.                                       |
   |                       |                       |                                                                                                             |
   |                       |                       | -  The value can contain the following special characters: \_.:=+-@                                         |
   |                       |                       |                                                                                                             |
   |                       |                       | -  The value cannot start with **\_sys\_**.                                                                 |
   |                       |                       |                                                                                                             |
   |                       |                       | **Default value**:                                                                                          |
   |                       |                       |                                                                                                             |
   |                       |                       | N/A                                                                                                         |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------+
   | value                 | String                | **Details**:                                                                                                |
   |                       |                       |                                                                                                             |
   |                       |                       | Value                                                                                                       |
   |                       |                       |                                                                                                             |
   |                       |                       | **Constraints**:                                                                                            |
   |                       |                       |                                                                                                             |
   |                       |                       | None                                                                                                        |
   |                       |                       |                                                                                                             |
   |                       |                       | **Options**:                                                                                                |
   |                       |                       |                                                                                                             |
   |                       |                       | -  The value can be null but not the default. Max characters: 255                                           |
   |                       |                       |                                                                                                             |
   |                       |                       | -  Letters, digits, and spaces in UTF-8 format are supported.                                               |
   |                       |                       |                                                                                                             |
   |                       |                       | -  The value can contain the following special characters: ``_.:/=+-@``                                     |
   |                       |                       |                                                                                                             |
   |                       |                       | **Default value**:                                                                                          |
   |                       |                       |                                                                                                             |
   |                       |                       | N/A                                                                                                         |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------+

Example Requests
----------------

None

Example Responses
-----------------

**Status code: 200**

The resource tags have been obtained.

.. code-block::

   {
     "tags" : [ {
       "key" : "key1",
       "value" : "value1"
     } ],
     "sys_tags" : [ {
       "key" : "key2",
       "value" : "value2"
     } ]
   }

Status Codes
------------

=========== =====================================
Status Code Description
=========== =====================================
200         The resource tags have been obtained.
=========== =====================================

Error Codes
-----------

See :ref:`Error Codes <cce_02_0250>`.
