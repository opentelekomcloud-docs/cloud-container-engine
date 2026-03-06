:original_name: DeleteJob.html

.. _DeleteJob:

Deleting a Job
==============

Function
--------

This API is used to delete a job in a specified project.

URI
---

DELETE /api/v2/projects/{project_id}/jobs/{job_id}

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
   | job_id          | Yes             | String          | **Definition**                                                                                                           |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | Job ID. For details about how to obtain the value, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`.     |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | **Constraints**                                                                                                          |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | N/A                                                                                                                      |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | **Range**                                                                                                                |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | N/A                                                                                                                      |
   |                 |                 |                 |                                                                                                                          |
   |                 |                 |                 | **Default Value**                                                                                                        |
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

**Status code: 204**

The job has been deleted.

None

Example Requests
----------------

None

Example Responses
-----------------

None

Status Codes
------------

=========== =========================
Status Code Description
=========== =========================
204         The job has been deleted.
=========== =========================

Error Codes
-----------

See :ref:`Error Codes <cce_02_0250>`.
