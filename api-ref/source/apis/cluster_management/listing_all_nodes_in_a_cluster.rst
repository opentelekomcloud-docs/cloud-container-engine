:original_name: cce_02_0244.html

.. _cce_02_0244:

Listing All Nodes in a Cluster
==============================

Function
--------

This API is used to obtain details about all nodes in a cluster with a specified cluster ID.

URI
---

GET /api/v3/projects/{project_id}/clusters/{cluster_id}/nodes

:ref:`Table 1 <cce_02_0244__table2027961241820>` describes the parameters of the API.

.. _cce_02_0244__table2027961241820:

.. table:: **Table 1** Parameter description

   +------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Mandatory | Description                                                                                                                   |
   +============+===========+===============================================================================================================================+
   | project_id | Yes       | Project ID. For details about how to obtain the project ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
   | cluster_id | Yes       | Cluster ID. For details about how to obtain the cluster ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+

Request
-------

**Request parameters**:

:ref:`Table 2 <cce_02_0244__table13638079467>` describes the request parameters.

.. _cce_02_0244__table13638079467:

.. table:: **Table 2** Parameters in the request header

   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Mandatory             | Description                                                                                                                                                                                                                                                                   |
   +=======================+=======================+===============================================================================================================================================================================================================================================================================+
   | Content-Type          | Yes                   | Message body type (format). Possible values:                                                                                                                                                                                                                                  |
   |                       |                       |                                                                                                                                                                                                                                                                               |
   |                       |                       | -  application/json;charset=utf-8                                                                                                                                                                                                                                             |
   |                       |                       | -  application/json                                                                                                                                                                                                                                                           |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | X-Auth-Token          | Yes                   | Requests for calling an API can be authenticated using either a token or AK/SK. If token-based authentication is used, this parameter is mandatory and must be set to a user token. For details on how to obtain a user token, see :ref:`API Usage Guidelines <cce_02_0004>`. |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

**Example request**:

N/A

Response
--------

**Response parameters**:

:ref:`Table 3 <cce_02_0244__en-us_topic_0079616779_en-us_topic_0079614912_ref458774242>` describes the response parameters.

.. _cce_02_0244__en-us_topic_0079616779_en-us_topic_0079614912_ref458774242:

.. table:: **Table 3** Response parameters

   +------------+--------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Type   | Description                                                                                                                                                                                                                  |
   +============+========+==============================================================================================================================================================================================================================+
   | kind       | String | API type. The value is fixed to **List**.                                                                                                                                                                                    |
   +------------+--------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | apiVersion | String | API version. The value is fixed at **v3**.                                                                                                                                                                                   |
   +------------+--------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | items      | Object | A list of details for all nodes in the current cluster. You can filter nodes by **items.metadata.name**. For more information, see :ref:`Table 4 <cce_02_0243__en-us_topic_0079616779_en-us_topic_0079614912_ref458774242>`. |
   +------------+--------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

**Example response**:

.. code-block::

   {
     "kind": "List",
     "apiVersion": "v3",
     "items": [
       {
         "kind": "Node",
         "apiversion": "v3",
         "metadata": {
           "name": "myhost",
           "uid": "4d1ecb2c-229a-11e8-9c75-0255ac100ceb",
           "creationTimestamp": "2020-02-02 07:37:24.005071325 +0000 UTC",
           "updateTimestamp": "2020-02-02 07:44:04.965500815 +0000 UTC",
           "annotations": {
            "kubernetes.io/node-pool.id": "eu-de-01#s1.medium#EulerOS 2.5"
           }
         },
         "spec": {
           "flavor": "s1.medium",
           "az": "eu-de-01",
           "os": "EulerOS 2.5",
           "login": {
             "sshKey": "KeyPair-demo"
           },
           "rootVolume": {
             "volumetype": "SAS",
             "size": 40
           },
           "dataVolumes": [
             {
               "volumetype": "SAS",
               "size": 100
             }
           ],
           "storage": {
               "storageSelectors": [
                   {
                       "name": "cceUse",
                       "storageType": "evs",
                       "matchLabels": {
                           "size": "100",
                           "volumeType": "SAS",
                           "count": "1"
                       }
                   }
               ],
               "storageGroups": [
                   {
                       "name": "vgpaas",
                       "selectorNames": [
                           "cceUse"
                       ],
                       "cceManaged": true,
                       "virtualSpaces": [
                           {
                               "name": "runtime",
                               "size": "90%"
                           },
                           {
                               "name": "kubernetes",
                               "size": "10%"
                           }
                       ]
                   }
               ]
           },
           "publicIP": {
             "eip": {
               "bandwidth": {}
             }
           },
         "nodeNicSpec": {
             "primaryNic": {
             "subnetId": "c90b3ce5-e1f1-4c87-a006-644d78846438"
            }
        },
         "billingMode": 0
         },
         "status": {
           "phase": "Active",
           "serverId": "456789abc-9368-46f3-8f29-d1a95622a568",
           "publicIP": "10.34.56.78",
           "privateIP": "192.168.1.23"
         }
       }
     ]
   }

Status Code
-----------

:ref:`Table 4 <cce_02_0244__en-us_topic_0079614900_table46761928>` describes the status code of this API.

.. _cce_02_0244__en-us_topic_0079614900_table46761928:

.. table:: **Table 4** Status code

   +-------------+------------------------------------------------------------------------------------+
   | Status Code | Description                                                                        |
   +=============+====================================================================================+
   | 200         | Information about all the nodes in the specified cluster is successfully obtained. |
   +-------------+------------------------------------------------------------------------------------+

For details about error status codes, see :ref:`Status Code <cce_02_0084>`.
