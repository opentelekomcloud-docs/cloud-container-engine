:original_name: cce_02_0349.html

.. _cce_02_0349:

Querying All API Versions
=========================

Function
--------

This API is used to query all available API versions.

URI
---

GET /

Request
-------

N/A

Response
--------

**Response parameters:**

:ref:`Table 1 <cce_02_0349__table986610460219>` describes the response parameters.

.. _cce_02_0349__table986610460219:

.. table:: **Table 1** Response parameters

   ========= ===== =================
   Parameter Type  Description
   ========= ===== =================
   versions  Array API version list.
   ========= ===== =================

.. table:: **Table 2** Data structure of the versions field

   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                                           |
   +=================+=================+=================+=======================================================================================================+
   | id              | Yes             | String          | API version ID, for example, v3.                                                                      |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------+
   | links           | Yes             | String          | API URL.                                                                                              |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------+
   | min_version     | No              | String          | API microversion.                                                                                     |
   |                 |                 |                 |                                                                                                       |
   |                 |                 |                 | -  If the API has microversions, the parameter value is the earliest version number supported.        |
   |                 |                 |                 | -  If the API does not have microversions, the parameter is left blank.                               |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------+
   | status          | Yes             | String          | API version status.                                                                                   |
   |                 |                 |                 |                                                                                                       |
   |                 |                 |                 | -  **CURRENT**: The version is a mainstream version.                                                  |
   |                 |                 |                 | -  **SUPPORTED**: The version is not the latest one but it is still in use.                           |
   |                 |                 |                 | -  **DEPRECATED**: The version is deprecated and will probably be deleted.                            |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------+
   | updated         | Yes             | String          | Version release time in UTC. For example, the parameter value for v1 is 2014-06-28T12:20:21Z.         |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------+
   | version         | Yes             | String          | API version number.                                                                                   |
   |                 |                 |                 |                                                                                                       |
   |                 |                 |                 | -  If the API version has microversions, the parameter value is the highest version number supported. |
   |                 |                 |                 | -  If the API version does not have API mini versions, the parameter is left blank.                   |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------+

**Example response:**

.. code-block::

   {
       "versions": [
           {
               "id": "v3",
               "links": [
                   {
                       "href": "https://container.eu-de.***.t-systems.com/v3",
                       "rel": "self"
                   }
               ],
               "min_version": "",
               "status": "CURRENT",
               "updated": "2018-09-15T00:00:00Z",
               "version": ""
           }
       ]
   }

Status Code
-----------

:ref:`Table 3 <cce_02_0349__t8935d48c19714740abd2e888a39be462>` describes the status code of the API.

.. _cce_02_0349__t8935d48c19714740abd2e888a39be462:

.. table:: **Table 3** Status code

   =========== ==================================
   Status Code Description
   =========== ==================================
   200         The query operation is successful.
   =========== ==================================

For details about error status codes, see :ref:`Status Code <cce_02_0084>`.
