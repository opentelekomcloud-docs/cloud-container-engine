:original_name: ShowUserChartsQuotas.html

.. _ShowUserChartsQuotas:

Obtaining the Quota of a User Chart
===================================

Function
--------

This API is used to obtain the quota of a user chart.

URI
---

GET /v2/charts/{project_id}/quotas

.. table:: **Table 1** Path Parameters

   +------------+-----------+--------+--------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Mandatory | Type   | Description                                                                                                              |
   +============+===========+========+==========================================================================================================================+
   | project_id | Yes       | String | Project ID. For details about how to obtain the value, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+--------+--------------------------------------------------------------------------------------------------------------------------+

Request Parameters
------------------

.. table:: **Table 2** Request header parameters

   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                                                                                                                                                                                     |
   +=================+=================+=================+=================================================================================================================================================================================================================================================+
   | Content-Type    | Yes             | String          | Details:                                                                                                                                                                                                                                        |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | Request body type or format.                                                                                                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | Constraints:                                                                                                                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | The GET method is not verified.                                                                                                                                                                                                                 |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | Options:                                                                                                                                                                                                                                        |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | -  application/json                                                                                                                                                                                                                             |
   |                 |                 |                 | -  application/json;charset=utf-8                                                                                                                                                                                                               |
   |                 |                 |                 | -  application/x-pem-file                                                                                                                                                                                                                       |
   |                 |                 |                 | -  multipart/form-data (It can be used when the **FormData** parameter exists.)                                                                                                                                                                 |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | Default value:                                                                                                                                                                                                                                  |
   |                 |                 |                 |                                                                                                                                                                                                                                                 |
   |                 |                 |                 | N/A                                                                                                                                                                                                                                             |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | X-Auth-Token    | Yes             | String          | Requests for calling an API can be authenticated using either a token or AK/SK. If token-based authentication is used, this parameter is mandatory and must be set to a user token. For details, see :ref:`API Usage Guidelines <cce_02_0004>`. |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Response Parameters
-------------------

**Status code: 200**

.. table:: **Table 3** Response body parameters

   +-----------+-------------------------------------------------------------------------------------------+-------------+
   | Parameter | Type                                                                                      | Description |
   +===========+===========================================================================================+=============+
   | quotas    | :ref:`quotas <showuserchartsquotas__en-us_topic_0000001850048626_response_quotas>` object | Chart quota |
   +-----------+-------------------------------------------------------------------------------------------+-------------+

.. _showuserchartsquotas__en-us_topic_0000001850048626_response_quotas:

.. table:: **Table 4** quotas

   +-----------+-----------------------------------------------------------------------------------------------------------+-------------+
   | Parameter | Type                                                                                                      | Description |
   +===========+===========================================================================================================+=============+
   | resources | Array of :ref:`resources <showuserchartsquotas__en-us_topic_0000001850048626_response_resources>` objects | Resources   |
   +-----------+-----------------------------------------------------------------------------------------------------------+-------------+

.. _showuserchartsquotas__en-us_topic_0000001850048626_response_resources:

.. table:: **Table 5** resources

   ========= ======= ==============
   Parameter Type    Description
   ========= ======= ==============
   type      String  Resource type
   quota     Integer Resource quota
   used      Integer Used resources
   ========= ======= ==============

Example Requests
----------------

None

Example Responses
-----------------

**Status code: 200**

OK

.. code-block::

   {
     "quotas" : {
       "resources" : [ {
         "type" : "Charts",
         "quota" : 200,
         "used" : 2
       } ]
     }
   }

Status Codes
------------

=========== ===========
Status Code Description
=========== ===========
200         OK
=========== ===========

Error Codes
-----------

See :ref:`Error Codes <cce_02_0250>`.
