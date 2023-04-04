:original_name: cce_02_0246.html

.. _cce_02_0246:

Deleting a Node
===============

Function
--------

This API is used to delete a specified node.

URI
---

DELETE /api/v3/projects/{project_id}/clusters/{cluster_id}/nodes/{node_id}

:ref:`Table 1 <cce_02_0246__table2027961241820>` describes the parameters of the API.

.. _cce_02_0246__table2027961241820:

.. table:: **Table 1** Description

   +------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Mandatory | Description                                                                                                                   |
   +============+===========+===============================================================================================================================+
   | project_id | Yes       | Project ID. For details about how to obtain the project ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
   | cluster_id | Yes       | Cluster ID. For details about how to obtain the cluster ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
   | node_id    | Yes       | Cluster ID. For details about how to obtain the cluster ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+

Request
-------

**Request parameters**:

:ref:`Table 2 <cce_02_0246__table7347141814502>` describes the request parameters.

.. _cce_02_0246__table7347141814502:

.. table:: **Table 2** Parameters in the request header

   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Mandatory             | Description                                                                                                                                                                                                                                                                   |
   +=======================+=======================+===============================================================================================================================================================================================================================================================================+
   | Content-Type          | Yes                   | Message body type (format). Possible values:                                                                                                                                                                                                                                  |
   |                       |                       |                                                                                                                                                                                                                                                                               |
   |                       |                       | -  application/json;charset=utf-8                                                                                                                                                                                                                                             |
   |                       |                       | -  application/json                                                                                                                                                                                                                                                           |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | X-Auth-Token          | Yes                   | Requests for calling an API can be authenticated using either a token or AK/SK. If token-based authentication is used, this parameter is mandatory and must be set to a user token. For details on how to obtain a user token, see :ref:`API Usage Guidelines <cce_02_0344>`. |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

**Example request**:

N/A

Response
--------

**Response parameters**:

:ref:`Table 3 <cce_02_0246__en-us_topic_0079616779_en-us_topic_0079614912_ref458774242>` describes the response parameters.

.. _cce_02_0246__en-us_topic_0079616779_en-us_topic_0079614912_ref458774242:

.. table:: **Table 3** Response parameters

   +------------+---------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Type                                                    | Description                                                                                                             |
   +============+=========================================================+=========================================================================================================================+
   | kind       | String                                                  | API type. The value is fixed at **Node** and cannot be changed.                                                         |
   +------------+---------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | apiVersion | String                                                  | API version. The value is fixed at **v3** and cannot be changed.                                                        |
   +------------+---------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | metadata   | :ref:`metadata <cce_02_0243__table669019286188>` object | Node's metadata, which is a collection of attributes.                                                                   |
   +------------+---------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | spec       | :ref:`spec <cce_02_0242__table13949117115810>` object   | Detailed description of the node targeted by this API. CCE creates or updates objects by defining or updating its spec. |
   +------------+---------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | status     | :ref:`status <cce_02_0246__table1741714540447>` object  | Node status and jobID of the node deletion job.                                                                         |
   +------------+---------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0246__table1741714540447:

.. table:: **Table 4** Data structure of the **status** field

   +-----------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   | Parameter             | Type                                                            | Description                                                                                                  |
   +=======================+=================================================================+==============================================================================================================+
   | phase                 | String                                                          | Node status.                                                                                                 |
   |                       |                                                                 |                                                                                                              |
   |                       |                                                                 | -  **Build**: The VM that hosts the node is being created.                                                   |
   |                       |                                                                 | -  **Active**: The node is ready for use.                                                                    |
   |                       |                                                                 | -  **Abnormal**: The node is unready for use.                                                                |
   |                       |                                                                 | -  **Deleting**: The node is being deleted.                                                                  |
   |                       |                                                                 | -  **Installing**: The node is being installed.                                                              |
   |                       |                                                                 | -  **Upgrading**: The node is being upgraded.                                                                |
   +-----------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   | jobID                 | String                                                          | ID of the node deletion job. You can query job progress by job ID to keep updated on node deletion progress. |
   +-----------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   | serverId              | String                                                          | ID of the ECS where the node resides.                                                                        |
   +-----------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   | publicIP              | String                                                          | EIP used by the node to access public networks.                                                              |
   +-----------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   | privateIP             | String                                                          | Private IP address used by the node to communicate with other nodes in the same VPC as the current cluster.  |
   +-----------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   | deleteStatus          | :ref:`DeleteStatus <cce_02_0246__response_deletestatus>` object | Resource status during resource deletion.                                                                    |
   +-----------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+

.. _cce_02_0246__response_deletestatus:

.. table:: **Table 5** DeleteStatus

   +----------------+---------+------------------------------------------------------------------------------------------------------+
   | Parameter      | Type    | Description                                                                                          |
   +================+=========+======================================================================================================+
   | previous_total | Integer | Total number of existing cluster resource records when the cluster is deleted.                       |
   +----------------+---------+------------------------------------------------------------------------------------------------------+
   | current_total  | Integer | Latest number of resource records, which is generated based on the current cluster resource records. |
   +----------------+---------+------------------------------------------------------------------------------------------------------+
   | updated        | Integer | Total number of resource records updated when the cluster is deleted.                                |
   +----------------+---------+------------------------------------------------------------------------------------------------------+
   | added          | Integer | Total number of resource records updated when the cluster is deleted.                                |
   +----------------+---------+------------------------------------------------------------------------------------------------------+
   | deleted        | Integer | Total number of resource records deleted when the cluster is deleted.                                |
   +----------------+---------+------------------------------------------------------------------------------------------------------+

**Example response**:

.. code-block::

   {
       "kind": "Node",
       "apiVersion": "v3",
       "metadata": {
           "name": "new-hostname",
           "uid": "cc697ad9-9563-11e8-8ea7-0255ac106311",
           "creationTimestamp": "2020-02-01 08:20:49.944664515 +0000 UTC",
           "updateTimestamp": "2020-02-01 09:20:05.644032347 +0000 UTC",
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
           "jobID": "661f6f7d-956c-11e8-a916-0255ac10575d",
           "serverId": "5b504f8d-33f1-4ab7-a600-b62dac967d72",
           "privateIP": "192.168.0.69",
           "publicIP": "10.154.194.59"
       }
   }

Status Code
-----------

:ref:`Table 6 <cce_02_0246__en-us_topic_0079614900_table46761928>` describes the status code of this API.

.. _cce_02_0246__en-us_topic_0079614900_table46761928:

.. table:: **Table 6** Status code

   =========== ========================================================
   Status Code Description
   =========== ========================================================
   200         The job for deleting the node is successfully delivered.
   =========== ========================================================

For details about error status codes, see :ref:`Status Code <cce_02_0084>`.
