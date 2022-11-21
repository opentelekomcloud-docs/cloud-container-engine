:original_name: cce_02_0350.html

.. _cce_02_0350:

Querying Information About API v3
=================================

Function
--------

This API is used to query information about API v3.

URI
---

GET /v3

Request
-------

N/A

Response
--------

**Response parameters:**

:ref:`Table 1 <cce_02_0350__table986610460219>` describes the response parameters.

.. _cce_02_0350__table986610460219:

.. table:: **Table 1** Response parameters

   ========= ===== =================
   Parameter Type  Description
   ========= ===== =================
   versions  Array API version list.
   ========= ===== =================

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

:ref:`Table 2 <cce_02_0350__t8935d48c19714740abd2e888a39be462>` describes the status code of the API.

.. _cce_02_0350__t8935d48c19714740abd2e888a39be462:

.. table:: **Table 2** Status code

   =========== ==================================
   Status Code Description
   =========== ==================================
   200         The query operation is successful.
   =========== ==================================

For details about error status codes, see :ref:`Status Code <cce_02_0084>`.
