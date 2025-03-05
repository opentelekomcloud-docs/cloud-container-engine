:original_name: ListCharts.html

.. _ListCharts:

Obtaining a Chart List
======================

Function
--------

This API is used to obtain the chart list.

URI
---

GET /v2/charts

Request Parameters
------------------

.. table:: **Table 1** Request header parameters

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

.. table:: **Table 2** Response body parameters

   +-----------+-------------------------------------------------------------------------------------------------+-------------+
   | Parameter | Type                                                                                            | Description |
   +===========+=================================================================================================+=============+
   | [items]   | Array of :ref:`ChartResp <listcharts__en-us_topic_0000001850048554_response_chartresp>` objects | Chart list  |
   +-----------+-------------------------------------------------------------------------------------------------+-------------+

.. _listcharts__en-us_topic_0000001850048554_response_chartresp:

.. table:: **Table 3** ChartResp

   =========== ======= ===========================
   Parameter   Type    Description
   =========== ======= ===========================
   id          String  Chart ID
   name        String  Chart name
   values      String  Chart value
   translate   String  Chart translation resources
   instruction String  Chart description
   version     String  Chart version
   description String  Chart description
   source      String  Chart source
   icon_url    String  URL to chart icons
   public      Boolean Whether the chart is public
   chart_url   String  URL to the chart
   create_at   String  Created at
   update_at   String  Updated at
   =========== ======= ===========================

Example Requests
----------------

None

Example Responses
-----------------

**Status code: 200**

OK

.. code-block::

   [ {
     "id" : "1abd3bd6-0258-11ec-b8b0-0255ac100b05",
     "name" : "magento-mysql",
     "values" : "{\"basic\":{\"admin_password\":\"******\",\"admin_username\":\"username\",\"app_name\":\"magento\",\"mysql_database\":\"magento\",\"mysql_name\":\"mysql\",\"mysql_password\":\"******\",\"mysql_port\":3306,\"mysql_root_password\":\"******\",\"mysql_user\":\"magento\",\"storage_class\":\"csi-nas\",\"storage_mode\":\"ReadWriteMany\",\"storage_size\":\"10G\"},\"global\":{\"magento_EIP\":\"100.100.100.100\",\"magento_EPORT\":32080,\"namespace\":\"default\"},\"image\":{\"magento_image\":\"example.com/everest/magento:latest\",\"mysql_image\":\"example.com/everest/mysql:5.7.14\"}}",
     "translate" : "",
     "instruction" : "",
     "version" : "1.0.0",
     "description" : "chart description",
     "source" : "",
     "icon_url" : "https://example.com/magento-stack-110x117.png",
     "public" : false,
     "chart_url" : "magento-mysql-1.0.0.tgz",
     "create_at" : "2021-08-20T08:00:29Z",
     "update_at" : "2021-08-20T08:00:29Z"
   } ]

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
