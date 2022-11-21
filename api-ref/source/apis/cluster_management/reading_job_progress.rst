:original_name: cce_02_0247.html

.. _cce_02_0247:

Reading Job Progress
====================

Function
--------

This API is used to query the progress of a job with a specified job ID returned after a job request is issued.

.. note::

   -  The URL for cluster management is in the format of https://Endpoint/uri. In the URL, uri indicates the resource path, that is, the path for API access.
   -  You can call this API when:

      -  Creating or deleting a cluster
      -  Creating or deleting a node

URI
---

GET /api/v3/projects/{project_id}/jobs/{job_id}

:ref:`Table 1 <cce_02_0247__table2027961241820>` describes the parameters of the API.

.. _cce_02_0247__table2027961241820:

.. table:: **Table 1** Parameter description

   +------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Mandatory | Description                                                                                                                   |
   +============+===========+===============================================================================================================================+
   | project_id | Yes       | Project ID. For details about how to obtain the project ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
   | job_id     | Yes       | Job ID. For details about how to obtain the job ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`.         |
   +------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+

Request
-------

**Request parameters**:

:ref:`Table 2 <cce_02_0247__table189325395020>` describes the request parameters.

.. _cce_02_0247__table189325395020:

.. table:: **Table 2** Parameters in the request header

   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Mandatory             | Description                                                                                                                                                                                                                                                                   |
   +=======================+=======================+===============================================================================================================================================================================================================================================================================+
   | Content-Type          | Yes                   | Message body type (format). Possible values:                                                                                                                                                                                                                                  |
   |                       |                       |                                                                                                                                                                                                                                                                               |
   |                       |                       | Default: **application/json**                                                                                                                                                                                                                                                 |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | X-Auth-Token          | Yes                   | Requests for calling an API can be authenticated using either a token or AK/SK. If token-based authentication is used, this parameter is mandatory and must be set to a user token. For details on how to obtain a user token, see :ref:`API Usage Guidelines <cce_02_0344>`. |
   |                       |                       |                                                                                                                                                                                                                                                                               |
   |                       |                       | Maximum: **16384**                                                                                                                                                                                                                                                            |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

**Example request**:

N/A

Response
--------

**Response parameters**:

:ref:`Table 3 <cce_02_0247__en-us_topic_0079616779_en-us_topic_0079614912_ref458774242>` describes the response parameters.

.. _cce_02_0247__en-us_topic_0079616779_en-us_topic_0079614912_ref458774242:

.. table:: **Table 3** Response parameters

   +-----------------------+--------------------------------------------------------+------------------------------------------------------------------+
   | Parameter             | Type                                                   | Description                                                      |
   +=======================+========================================================+==================================================================+
   | kind                  | String                                                 | API type. The value is fixed at **Job** and cannot be changed.   |
   |                       |                                                        |                                                                  |
   |                       |                                                        | Default: **Job**                                                 |
   +-----------------------+--------------------------------------------------------+------------------------------------------------------------------+
   | apiVersion            | String                                                 | API version. The value is fixed at **v3** and cannot be changed. |
   |                       |                                                        |                                                                  |
   |                       |                                                        | Default: **v3**                                                  |
   +-----------------------+--------------------------------------------------------+------------------------------------------------------------------+
   | metadata              | :ref:`metadata <cce_02_0247__table13456192212>` object | Node metadata.                                                   |
   +-----------------------+--------------------------------------------------------+------------------------------------------------------------------+
   | spec                  | :ref:`spec <cce_02_0247__table620623542313>` object    | Detailed node parameters.                                        |
   +-----------------------+--------------------------------------------------------+------------------------------------------------------------------+
   | status                | :ref:`status <cce_02_0247__table5445161610255>` object | Node status.                                                     |
   +-----------------------+--------------------------------------------------------+------------------------------------------------------------------+

.. _cce_02_0247__table13456192212:

.. table:: **Table 4** Data structure of the **metadata** field

   ================= ====== ==================
   Parameter         Type   Description
   ================= ====== ==================
   uid               String Job ID.
   creationTimestamp String Job creation time.
   updateTimestamp   String Job update time.
   ================= ====== ==================

.. _cce_02_0247__table620623542313:

.. table:: **Table 5** Data structure of the **spec** field

   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Type                  | Description                                                                                                                      |
   +=======================+=======================+==================================================================================================================================+
   | type                  | String                | Job type, for example, **CreateCluster**.                                                                                        |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------+
   | clusterUID            | String                | ID of the cluster where the job runs.                                                                                            |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------+
   | resourceID            | String                | ID of the resource on which a job is executed.                                                                                   |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------+
   | resourceName          | String                | Name of the resource on which a job is executed.                                                                                 |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------+
   | extendParam           | Map<String,String>    | Extended parameter. This field is not supported for the current version.                                                         |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------+
   | subJobs               | Object                | List of sub-jobs. For details, see :ref:`Table 3 <cce_02_0247__en-us_topic_0079616779_en-us_topic_0079614912_ref458774242>`.     |
   |                       |                       |                                                                                                                                  |
   |                       |                       | -  The sub-job list contains details about all sub-jobs.                                                                         |
   |                       |                       | -  Usually, a cluster/node creation job consists of multiple sub-jobs. The job is complete only after all sub-jobs are complete. |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0247__table5445161610255:

.. table:: **Table 6** Data structure of the **status** field

   +-----------------------+-----------------------+---------------------------------------------------+
   | Parameter             | Type                  | Description                                       |
   +=======================+=======================+===================================================+
   | phase                 | String                | Job status. The options are as follows:           |
   |                       |                       |                                                   |
   |                       |                       | -  JobPhaseInitializing JobPhase = "Initializing" |
   |                       |                       | -  JobPhaseRunning JobPhase = "Running"           |
   |                       |                       | -  JobPhaseFailed JobPhase = "Failed"             |
   |                       |                       | -  JobPhaseSuccess JobPhase = "Success"           |
   +-----------------------+-----------------------+---------------------------------------------------+
   | reason                | String                | Reason why the job is in the current state.       |
   +-----------------------+-----------------------+---------------------------------------------------+

**Example response**:

.. code-block::

   {
       "kind": "Job",
       "apiVersion": "v3",
       "metadata": {
           "uid": "354331b2c-229a-11e8-9c75-0255ac100ceb",
           "creationTimestamp": "2020-02-02 08:12:40.672772389 +0000 UTC",
           "updateTimestamp": "2020-02-02 08:21:50.478108569 +0000 UTC"
       },
       "spec": {
           "type": "CreateCluster",
           "clusterUID": "4d1ecb2c-229a-11e8-9c75-0255ac100ceb",
           "resourceID": "6f4dcb2c-229a-11e8-9c75-0255ac100ceb",
           "resourceName": "cluster-name",
           "extendParam": {
               "serverID": "bc467e3a-2338-11e8-825b-0255ac100c13"
           },
           "subJobs": [
               {
                   "kind": "Job",
                   "apiVersion": "v3",
                   "metadata": {
                       "uid": "fd474fab-9606-11e8-baa9-0255ac10215d",
                       "creationTimestamp": "2020-02-02 03:52:34.615819618 +0000 UTC",
                       "updateTimestamp": "2020-02-02 04:05:29.196243031 +0000 UTC"
                   },
                   "spec": {
                       "type": "InstallMaster",
                       "clusterUID": "fcc72de0-9606-11e8-baa8-0255ac10215d",
                       "resourceID": "fd3b4ac0-9606-11e8-baa8-0255ac10215d",
                       "extendParam": {
                           "serverID": "fd3b4ac0-9606-11e8-baa8-0255ac10215d"
                       }
                   },
                   "status": {
                       "phase": "Success"
                   }
               },
               {
                   "kind": "Job",
                   "apiVersion": "v3",
                   "metadata": {
                       "uid": "fd474f82-9606-11e8-baa8-0255ac10215d",
                       "creationTimestamp": "2020-02-02 03:52:33.859150791 +0000 UTC",
                       "updateTimestamp": "2020-02-02 03:52:34.615655429 +0000 UTC"
                   },
                   "spec": {
                       "type": "CreatePSMCert",
                       "clusterUID": "fcc72de0-9606-11e8-baa8-0255ac10215d"
                   },
                   "status": {
                       "phase": "Success"
                   }
               }
           ],
       },
       "status": {
           "phase": "Running",
           "reason": ""
       }
   }

Status Code
-----------

:ref:`Table 7 <cce_02_0247__en-us_topic_0079614900_table46761928>` describes the status code of this API.

.. _cce_02_0247__en-us_topic_0079614900_table46761928:

.. table:: **Table 7** Status code

   =========== ===========================================================
   Status Code Description
   =========== ===========================================================
   200         The progress of the specified job is successfully obtained.
   =========== ===========================================================

For details about error status codes, see :ref:`Status Code <cce_02_0084>`.
