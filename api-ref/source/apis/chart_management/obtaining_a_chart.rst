:original_name: ShowChart.html

.. _ShowChart:

Obtaining a Chart
=================

Function
--------

This API is used to obtain a chart.

URI
---

GET /v2/charts/{chart_id}

.. table:: **Table 1** Path Parameters

   ========= ========= ====== ===========
   Parameter Mandatory Type   Description
   ========= ========= ====== ===========
   chart_id  Yes       String Chart ID
   ========= ========= ====== ===========

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

   {
     "id" : "e99a7e86-afdd-11eb-aca3-0255ac100b0e",
     "name" : "neo4j",
     "values" : "{\"acceptLicenseAgreement\":\"no\",\"affinity\":{},\"authEnabled\":true,\"clusterDomain\":\"cluster.local\",\"core\":{\"initContainers\":[],\"numberOfServers\":3,\"persistentVolume\":{\"enabled\":true,\"mountPath\":\"/data\",\"size\":\"10Gi\"},\"sidecarContainers\":[]},\"defaultDatabase\":\"neo4j\",\"image\":\"neo4j\",\"imagePullPolicy\":\"IfNotPresent\",\"imageTag\":\"4.0.3-enterprise\",\"name\":\"neo4j\",\"nodeSelector\":{},\"podDisruptionBudget\":{},\"readReplica\":{\"autoscaling\":{\"enabled\":false,\"maxReplicas\":3,\"minReplicas\":1,\"targetAverageUtilization\":70},\"initContainers\":[],\"numberOfServers\":0,\"resources\":{},\"sidecarContainers\":[]},\"resources\":{},\"testImage\":\"markhneedham/k8s-kubectl\",\"testImageTag\":\"master\",\"tolerations\":[],\"useAPOC\":\"true\"}",
     "translate" : "",
     "instruction" : "README.md",
     "version" : "3.0.1",
     "description" : "DEPRECATED Neo4j is the world's leading graph database",
     "source" : "",
     "icon_url" : "https://info.neo4j.com/rs/773-GON-065/images/neo4j_logo.png",
     "public" : false,
     "chart_url" : "neo4j-3.0.1.tgz",
     "create_at" : "2021-05-08T08:53:13Z",
     "update_at" : "2021-05-08T08:53:13Z"
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
