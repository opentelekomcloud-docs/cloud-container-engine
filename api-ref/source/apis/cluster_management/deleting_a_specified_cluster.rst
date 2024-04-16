:original_name: cce_02_0241.html

.. _cce_02_0241:

Deleting a Specified Cluster
============================

Function
--------

This API is used to delete a specified cluster.

.. note::

   The URL for cluster management is in the format of **https://Endpoint/uri**. In the URL, **uri** indicates the resource path, that is, the path for API access.

URI
---

DELETE /api/v3/projects/{project_id}/clusters/{cluster_id}

.. table:: **Table 1** Path parameters

   +------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Mandatory | Type   | Description                                                                                                                   |
   +============+===========+========+===============================================================================================================================+
   | project_id | Yes       | String | Project ID. For details about how to obtain the project ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------------+
   | cluster_id | Yes       | String | Cluster ID. For details about how to obtain the cluster ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------------+

.. table:: **Table 2** Query Parameters

   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                                                                                                                                                  |
   +=================+=================+=================+==============================================================================================================================================================================================================+
   | errorStatus     | No              | String          | This field allows a cluster to be in the Error state if exceptions occur when the cluster is being deleted. If the value of **errorStatus** is null, the cluster stays in the Deleting state, but not Error. |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Minimum: **0**                                                                                                                                                                                               |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Maximum: **10**                                                                                                                                                                                              |
   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | delete_efs      | No              | String          | Whether to delete SFS Turbo resources. Value options:                                                                                                                                                        |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | -  **true** or **block** (The system starts to delete the object. If the deletion fails, subsequent processes are blocked.)                                                                                  |
   |                 |                 |                 | -  **try** (The system starts to delete the object. If the deletion fails, no deletion retry is performed, and subsequent processes are not blocked.)                                                        |
   |                 |                 |                 | -  **false** or **skip** (The object is not deleted. These are the default value options.)                                                                                                                   |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Default: **false**                                                                                                                                                                                           |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Enumeration values:                                                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | -  **true**                                                                                                                                                                                                  |
   |                 |                 |                 | -  **block**                                                                                                                                                                                                 |
   |                 |                 |                 | -  **try**                                                                                                                                                                                                   |
   |                 |                 |                 | -  **false**                                                                                                                                                                                                 |
   |                 |                 |                 | -  **skip**                                                                                                                                                                                                  |
   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | delete_eni      | No              | String          | Whether to delete ENI ports. Value options:                                                                                                                                                                  |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | -  **true** or **block** (The system starts to delete the object. If the deletion fails, subsequent processes are blocked.)                                                                                  |
   |                 |                 |                 | -  **try** (The system starts to delete the object. If the deletion fails, no deletion retry is performed, and subsequent processes are not blocked.)                                                        |
   |                 |                 |                 | -  **false** or **skip** (The object is not deleted. These are the default value options.)                                                                                                                   |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Default: **false**                                                                                                                                                                                           |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Enumeration values:                                                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | -  **true**                                                                                                                                                                                                  |
   |                 |                 |                 | -  **block**                                                                                                                                                                                                 |
   |                 |                 |                 | -  **try**                                                                                                                                                                                                   |
   |                 |                 |                 | -  **false**                                                                                                                                                                                                 |
   |                 |                 |                 | -  **skip**                                                                                                                                                                                                  |
   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | delete_evs      | No              | String          | Whether to delete EVS disks. Value options:                                                                                                                                                                  |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | -  **true** or **block** (The system starts to delete the object. If the deletion fails, subsequent processes are blocked.)                                                                                  |
   |                 |                 |                 | -  **try** (The system starts to delete the object. If the deletion fails, no deletion retry is performed, and subsequent processes are not blocked.)                                                        |
   |                 |                 |                 | -  **false** or **skip** (The object is not deleted. These are the default value options.)                                                                                                                   |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Default: **false**                                                                                                                                                                                           |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Enumeration values:                                                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | -  **true**                                                                                                                                                                                                  |
   |                 |                 |                 | -  **block**                                                                                                                                                                                                 |
   |                 |                 |                 | -  **try**                                                                                                                                                                                                   |
   |                 |                 |                 | -  **false**                                                                                                                                                                                                 |
   |                 |                 |                 | -  **skip**                                                                                                                                                                                                  |
   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | delete_net      | No              | String          | Whether to delete cluster Service/ingress-related resources, such as ELB. Value options:                                                                                                                     |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | -  **true** or **block** (The system starts to delete the object. If the deletion fails, subsequent processes are blocked.)                                                                                  |
   |                 |                 |                 | -  **try** (The system starts to delete the object. If the deletion fails, no deletion retry is performed, and subsequent processes are not blocked.)                                                        |
   |                 |                 |                 | -  **false** or **skip** (The object is not deleted. These are the default value options.)                                                                                                                   |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Default: **false**                                                                                                                                                                                           |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Enumeration values:                                                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | -  **true**                                                                                                                                                                                                  |
   |                 |                 |                 | -  **block**                                                                                                                                                                                                 |
   |                 |                 |                 | -  **try**                                                                                                                                                                                                   |
   |                 |                 |                 | -  **false**                                                                                                                                                                                                 |
   |                 |                 |                 | -  **skip**                                                                                                                                                                                                  |
   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | delete_obs      | No              | String          | Whether to delete the OBS resources. Value options:                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | -  **true** or **block** (The system starts to delete the object. If the deletion fails, subsequent processes are blocked.)                                                                                  |
   |                 |                 |                 | -  **try** (The system starts to delete the object. If the deletion fails, no deletion retry is performed, and subsequent processes are not blocked.)                                                        |
   |                 |                 |                 | -  **false** or **skip** (The object is not deleted. These are the default value options.)                                                                                                                   |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Default: **false**                                                                                                                                                                                           |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Enumeration values:                                                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | -  **true**                                                                                                                                                                                                  |
   |                 |                 |                 | -  **block**                                                                                                                                                                                                 |
   |                 |                 |                 | -  **try**                                                                                                                                                                                                   |
   |                 |                 |                 | -  **false**                                                                                                                                                                                                 |
   |                 |                 |                 | -  **skip**                                                                                                                                                                                                  |
   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | delete_sfs      | No              | String          | Whether to delete the SFS resources. Value options:                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | -  **true** or **block** (The system starts to delete the object. If the deletion fails, subsequent processes are blocked.)                                                                                  |
   |                 |                 |                 | -  **try** (The system starts to delete the object. If the deletion fails, no deletion retry is performed, and subsequent processes are not blocked.)                                                        |
   |                 |                 |                 | -  **false** or **skip** (The object is not deleted. These are the default value options.)                                                                                                                   |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Default: **false**                                                                                                                                                                                           |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | Enumeration values:                                                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                              |
   |                 |                 |                 | -  **true**                                                                                                                                                                                                  |
   |                 |                 |                 | -  **block**                                                                                                                                                                                                 |
   |                 |                 |                 | -  **try**                                                                                                                                                                                                   |
   |                 |                 |                 | -  **false**                                                                                                                                                                                                 |
   |                 |                 |                 | -  **skip**                                                                                                                                                                                                  |
   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Request
-------

**Request parameters**:

:ref:`Table 3 <cce_02_0241__table538113720514>` lists the request parameters.

.. _cce_02_0241__table538113720514:

.. table:: **Table 3** Parameters in the request header

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

:ref:`Table 4 <cce_02_0241__en-us_topic_0079616779_en-us_topic_0079614912_ref458774242>` describes response parameters.

.. _cce_02_0241__en-us_topic_0079616779_en-us_topic_0079614912_ref458774242:

.. table:: **Table 4** Response parameters

   +------------+-------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Type                                                              | Description                                                                                                                |
   +============+===================================================================+============================================================================================================================+
   | kind       | String                                                            | API type. The value is fixed at **Cluster** and cannot be changed.                                                         |
   +------------+-------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | apiVersion | String                                                            | API version. The value is fixed at **v3** and cannot be changed.                                                           |
   +------------+-------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | metadata   | :ref:`metadata <cce_02_0236__table669019286188>` object           | Cluster metadata, which is a collection of attributes.                                                                     |
   +------------+-------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | spec       | :ref:`spec <cce_02_0236__table195921039143517>` object            | Detailed description of the cluster targeted by this API. CCE creates or updates objects by defining or updating its spec. |
   +------------+-------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | status     | :ref:`ClusterStatus <cce_02_0241__response_clusterstatus>` object | Cluster status and jobID of the job that deletes a specified project.                                                      |
   +------------+-------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0241__response_clusterstatus:

.. table:: **Table 5** ClusterStatus

   +-----------------------+--------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Type                                                                     | Description                                                                                                                                        |
   +=======================+==========================================================================+====================================================================================================================================================+
   | phase                 | String                                                                   | Cluster status. Possible values:                                                                                                                   |
   |                       |                                                                          |                                                                                                                                                    |
   |                       |                                                                          | -  **Available**: The cluster is running properly.                                                                                                 |
   |                       |                                                                          | -  **Unavailable**: The cluster is exhibiting unexpected behavior. Manually delete the cluster or contact the administrator to delete the cluster. |
   |                       |                                                                          | -  **ScalingUp**: Nodes are being added to the cluster.                                                                                            |
   |                       |                                                                          | -  **ScalingDown**: The cluster is being downsized to fewer nodes.                                                                                 |
   |                       |                                                                          | -  **Creating**: The cluster is being created.                                                                                                     |
   |                       |                                                                          | -  **Deleting**: The cluster is being deleted.                                                                                                     |
   |                       |                                                                          | -  **Upgrading**: The cluster is being upgraded.                                                                                                   |
   |                       |                                                                          | -  **Resizing**: Cluster specifications are being changed.                                                                                         |
   |                       |                                                                          | -  **Empty**: The cluster has no resources.                                                                                                        |
   +-----------------------+--------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | jobID                 | String                                                                   | Job ID.                                                                                                                                            |
   +-----------------------+--------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | reason                | String                                                                   | Reason of cluster state change. This parameter is returned if the cluster is not in the Available state.                                           |
   +-----------------------+--------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | message               | String                                                                   | Detailed information about why the cluster changes to the current state. This parameter is returned if the cluster is not in the Available state.  |
   +-----------------------+--------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | endpoints             | :ref:`ClusterEndpoints <cce_02_0241__response_clusterendpoints>` objects | Access address of kube-apiserver in the cluster.                                                                                                   |
   +-----------------------+--------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | deleteOption          | :ref:`deleteOption <cce_02_0241__table1071610413396>` Object             | Whether to delete configurations. This parameter is contained only in the response to the deletion request.                                        |
   +-----------------------+--------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
   | deleteStatus          | :ref:`deleteStatus <cce_02_0241__table191928471414>` Object              | Whether to delete the status information. This parameter is contained only in the response to the deletion request.                                |
   +-----------------------+--------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0241__response_clusterendpoints:

.. table:: **Table 6** ClusterEndpoints

   +--------------+--------+-------------------------------------------------------------+
   | Parameter    | Type   | Description                                                 |
   +==============+========+=============================================================+
   | internal     | String | Internal network address.                                   |
   +--------------+--------+-------------------------------------------------------------+
   | external     | String | External network address.                                   |
   +--------------+--------+-------------------------------------------------------------+
   | external_otc | String | Endpoint of the cluster to be accessed through API Gateway. |
   +--------------+--------+-------------------------------------------------------------+

.. _cce_02_0241__table1071610413396:

.. table:: **Table 7** deleteOption

   +-----------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Type                  | Description                                                                                                                                                  |
   +=======================+=======================+==============================================================================================================================================================+
   | delete_sfs            | String                | Whether to delete SFS Turbo resources. Value options:                                                                                                        |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | -  **delete-true** or **delete-block** (The system starts to delete the object. If the deletion fails, subsequent processes are blocked.)                    |
   |                       |                       | -  **delete-try** (The system starts to delete the object. If the deletion fails, no deletion retry is performed, and subsequent processes are not blocked.) |
   |                       |                       | -  **delete-false** or **delete-skip** (The object is not deleted. These are the default value options.)                                                     |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | Default: **delete-false**                                                                                                                                    |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | Enumeration values:                                                                                                                                          |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | -  **delete-true**                                                                                                                                           |
   |                       |                       | -  **delete-block**                                                                                                                                          |
   |                       |                       | -  **delete-try**                                                                                                                                            |
   |                       |                       | -  **delete-false**                                                                                                                                          |
   |                       |                       | -  **delete-skip**                                                                                                                                           |
   +-----------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | delete_eni            | String                | Whether to delete ENI ports. Value options:                                                                                                                  |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | -  **delete-true** or **delete-block** (The system starts to delete the object. If the deletion fails, subsequent processes are blocked.)                    |
   |                       |                       | -  **delete-try** (The system starts to delete the object. If the deletion fails, no deletion retry is performed, and subsequent processes are not blocked.) |
   |                       |                       | -  **delete-false** or **delete-skip** (The object is not deleted. These are the default value options.)                                                     |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | Default: **delete-false**                                                                                                                                    |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | Enumeration values:                                                                                                                                          |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | -  **delete-true**                                                                                                                                           |
   |                       |                       | -  **delete-block**                                                                                                                                          |
   |                       |                       | -  **delete-try**                                                                                                                                            |
   |                       |                       | -  **delete-false**                                                                                                                                          |
   |                       |                       | -  **delete-skip**                                                                                                                                           |
   +-----------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | delete_evs            | String                | Whether to delete EVS disks. Value options:                                                                                                                  |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | -  **delete-true** or **delete-block** (The system starts to delete the object. If the deletion fails, subsequent processes are blocked.)                    |
   |                       |                       | -  **delete-try** (The system starts to delete the object. If the deletion fails, no deletion retry is performed, and subsequent processes are not blocked.) |
   |                       |                       | -  **delete-false** or **delete-skip** (The object is not deleted. These are the default value options.)                                                     |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | Default: **delete-false**                                                                                                                                    |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | Enumeration values:                                                                                                                                          |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | -  **delete-true**                                                                                                                                           |
   |                       |                       | -  **delete-block**                                                                                                                                          |
   |                       |                       | -  **delete-try**                                                                                                                                            |
   |                       |                       | -  **delete-false**                                                                                                                                          |
   |                       |                       | -  **delete-skip**                                                                                                                                           |
   +-----------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | delete_net            | String                | Whether to delete cluster Service/ingress-related resources, such as ELB. Value options:                                                                     |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | -  **delete-true** or **delete-block** (The system starts to delete the object. If the deletion fails, subsequent processes are blocked.)                    |
   |                       |                       | -  **delete-try** (The system starts to delete the object. If the deletion fails, no deletion retry is performed, and subsequent processes are not blocked.) |
   |                       |                       | -  **delete-false** or **delete-skip** (The object is not deleted. These are the default value options.)                                                     |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | Default: **delete-false**                                                                                                                                    |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | Enumeration values:                                                                                                                                          |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | -  **delete-true**                                                                                                                                           |
   |                       |                       | -  **delete-block**                                                                                                                                          |
   |                       |                       | -  **delete-try**                                                                                                                                            |
   |                       |                       | -  **delete-false**                                                                                                                                          |
   |                       |                       | -  **delete-skip**                                                                                                                                           |
   +-----------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | delete_obs            | String                | Whether to delete the OBS resources. Value options:                                                                                                          |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | -  **delete-true** or **delete-block** (The system starts to delete the object. If the deletion fails, subsequent processes are blocked.)                    |
   |                       |                       | -  **delete-try** (The system starts to delete the object. If the deletion fails, no deletion retry is performed, and subsequent processes are not blocked.) |
   |                       |                       | -  **delete-false** or **delete-skip** (The object is not deleted. These are the default value options.)                                                     |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | Default: **delete-false**                                                                                                                                    |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | Enumeration values:                                                                                                                                          |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | -  **delete-true**                                                                                                                                           |
   |                       |                       | -  **delete-block**                                                                                                                                          |
   |                       |                       | -  **delete-try**                                                                                                                                            |
   |                       |                       | -  **delete-false**                                                                                                                                          |
   |                       |                       | -  **delete-skip**                                                                                                                                           |
   +-----------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | delete_sfs            | String                | Whether to delete the SFS resources. Value options:                                                                                                          |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | -  **delete-true** or **delete-block** (The system starts to delete the object. If the deletion fails, subsequent processes are blocked.)                    |
   |                       |                       | -  **delete-try** (The system starts to delete the object. If the deletion fails, no deletion retry is performed, and subsequent processes are not blocked.) |
   |                       |                       | -  **delete-false** or **delete-skip** (The object is not deleted. These are the default value options.)                                                     |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | Default: **delete-false**                                                                                                                                    |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | Enumeration values:                                                                                                                                          |
   |                       |                       |                                                                                                                                                              |
   |                       |                       | -  **delete-true**                                                                                                                                           |
   |                       |                       | -  **delete-block**                                                                                                                                          |
   |                       |                       | -  **delete-try**                                                                                                                                            |
   |                       |                       | -  **delete-false**                                                                                                                                          |
   |                       |                       | -  **delete-skip**                                                                                                                                           |
   +-----------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0241__table191928471414:

.. table:: **Table 8** deleteStatus

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

**Response example**:

.. code-block::

   {
       "kind": "Cluster",
       "apiVersion": "v3",
       "metadata": {
           "name": "mycluster",
           "uid": "7a661f82-90d9-11ea-9f85-0255ac10110d",
           "creationTimestamp": "2020-05-08 03:10:32.403757 +0000 UTC",
           "updateTimestamp": "2020-05-08 03:18:29.238331 +0000 UTC"
       },
       "spec": {
           "type": "VirtualMachine",
           "flavor": "cce.s1.small",
           "version": "v1.17.9-r0",
           "az": "eu-de-01",
           "ipv6enable": false,
           "supportIstio": true,
           "hostNetwork": {
               "vpc": "23d3725f-6ffe-400e-8fb6-b4f9a7b3e8c1",
               "subnet": "c90b3ce5-e1f1-4c87-a006-644d78846438",
               "SecurityGroup": "11fb88d1-0fc9-422d-963d-374ababa5f57"
           },
           "containerNetwork": {
               "mode": "overlay_l2",
               "cidr": "172.16.0.0/16"
           },

           "authentication": {
               "mode": "rbac",
               "authenticatingProxy": {}
           },
           "billingMode": 0,
           "extendParam": {
               "alpha.cce/fixPoolMask": "",
               "kubernetes.io/cpuManagerPolicy": "",
               "upgradefrom": ""
           },
           "kubernetesSvcIpRange": "10.247.0.0/16",
           "kubeProxyMode": "iptables"
       },
       "status": {
           "phase": "Available",
           "jobID": "f5fd8cbf-90db-11ea-9f85-0255ac10110d",
           "endpoints": {
              "Internal": "https://192.168.0.61:5443",
              "External": "https://10.185.69.54:5443",
              "external_otc": "https://a140174a-2f3e-11e9-9f91-0255ac101405.cce.eu-de.otc.t-systems.com"
           },
           "deleteOption": {
              "delete_eni": "delete-skip",
              "delete_net": "delete-block",
              "delete_evs": "delete-skip",
              "delete_sfs": "delete-skip",
              "delete_obs": "delete-skip",
              "delete_efs": "delete-skip"
           },
           "deleteStatus": {
              "previous_total": 0,
              "current_total": 15,
              "updated": 0,
              "added": 15,
              "deleted": 0
           }
       }
   }

Status Code
-----------

:ref:`Table 9 <cce_02_0241__en-us_topic_0079614900_table46761928>` describes the status code of this API.

.. _cce_02_0241__en-us_topic_0079614900_table46761928:

.. table:: **Table 9** Status code

   =========== =========================================================
   Status Code Description
   =========== =========================================================
   200         The job for deleting a cluster is successfully delivered.
   =========== =========================================================

For details about error status codes, see :ref:`Status Code <cce_02_0084>`.
