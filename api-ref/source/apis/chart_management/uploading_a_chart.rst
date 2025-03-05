:original_name: UploadChart.html

.. _UploadChart:

Uploading a Chart
=================

Function
--------

This API is used to upload a chart.

URI
---

POST /v2/charts

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

.. table:: **Table 2** FormData parameters

   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                                                                          |
   +=================+=================+=================+======================================================================================================================================+
   | parameters      | No              | String          | Parameter configurations for uploading a chart. The following is an example: "{"override":true,"skip_lint":true,"source":"package"}" |
   |                 |                 |                 |                                                                                                                                      |
   |                 |                 |                 | -  skip_lint: whether lint uploaded chart                                                                                            |
   |                 |                 |                 | -  override: whether override existed chart                                                                                          |
   |                 |                 |                 | -  visible: update chart visible                                                                                                     |
   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | content         | Yes             | File            | Chart package file                                                                                                                   |
   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------+

Response Parameters
-------------------

**Status code: 201**

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

If the **FormData** parameter exists, the value of **Content-Type** must be **multipart/form-data**, and the file path must be specified.

.. code-block:: text

   POST /v2/charts -H "X-Auth-Token:$token" -H "Content-Type:multipart/form-data" -F parameters='{"skip_lint":true,"override":true,"source":"package"}' -F content=@/root/neo4j-3.0.1.tgz

Example Responses
-----------------

**Status code: 201**

Created

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
201         Created
=========== ===========

Error Codes
-----------

See :ref:`Error Codes <cce_02_0250>`.
