:original_name: cce_02_0326.html

.. _cce_02_0326:

Listing Add-on Instances
========================

Function
--------

This API is used to list all add-on instances in the cluster.

.. note::

   The URL for add-on management is in the format of **https://{clusterid}.Endpoint/uri.** In the URL, *{clusterid}* indicates the cluster ID, and *uri* indicates the resource path, that is, the path for API access.

URI
---

GET /api/v3/addons?cluster_id={cluster_id}

:ref:`Table 1 <cce_02_0326__table489613501374>` describes the parameters of this API.

.. _cce_02_0326__table489613501374:

.. table:: **Table 1** Parameter description

   +------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Mandatory | Type   | Description                                                                                                                   |
   +============+===========+========+===============================================================================================================================+
   | cluster_id | Yes       | String | Cluster ID. For details about how to obtain the cluster ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------------+

Request Parameters
------------------

**Request parameters:**

:ref:`Table 2 <cce_02_0326__table52418212211>` lists the request parameters.

.. _cce_02_0326__table52418212211:

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

**Example request:**

N/A

Response
--------

**Response parameters:**

For details about the response parameters, see :ref:`Table 3 <cce_02_0326__responseparameter>`.

.. _cce_02_0326__responseparameter:

.. table:: **Table 3** Parameters in the response body

   +------------+---------------------------------------------------------------------+-----------------------------------------------------------------------------+
   | Parameter  | Type                                                                | Description                                                                 |
   +============+=====================================================================+=============================================================================+
   | kind       | String                                                              | API type. The value is fixed at **Addon** and cannot be changed.            |
   +------------+---------------------------------------------------------------------+-----------------------------------------------------------------------------+
   | apiVersion | String                                                              | API version. The value is fixed at **v3** and cannot be changed.            |
   +------------+---------------------------------------------------------------------+-----------------------------------------------------------------------------+
   | metadata   | String                                                              | Basic information about the add-on. Metadata is a collection of attributes. |
   +------------+---------------------------------------------------------------------+-----------------------------------------------------------------------------+
   | items      | Array of :ref:`items <cce_02_0326__response_addoninstance>` objects | Add-on instance list.                                                       |
   +------------+---------------------------------------------------------------------+-----------------------------------------------------------------------------+

.. _cce_02_0326__response_addoninstance:

.. table:: **Table 4** Data structure of the items field

   +------------+---------------------------------------------------------+-----------------------------------------------------------------------------+
   | Parameter  | Type                                                    | Description                                                                 |
   +============+=========================================================+=============================================================================+
   | kind       | String                                                  | API type. The value is fixed at **Addon** and cannot be changed.            |
   +------------+---------------------------------------------------------+-----------------------------------------------------------------------------+
   | apiVersion | String                                                  | API version. The value is fixed at **v3** and cannot be changed.            |
   +------------+---------------------------------------------------------+-----------------------------------------------------------------------------+
   | metadata   | :ref:`metadata <cce_02_0326__response_metadata>` object | Basic information about the add-on. Metadata is a collection of attributes. |
   +------------+---------------------------------------------------------+-----------------------------------------------------------------------------+
   | spec       | :ref:`spec <cce_02_0326__response_instancespec>` object | Detailed description of the add-on instance.                                |
   +------------+---------------------------------------------------------+-----------------------------------------------------------------------------+
   | status     | :ref:`status <cce_02_0326__response_status>` object     | Add-on instance status.                                                     |
   +------------+---------------------------------------------------------+-----------------------------------------------------------------------------+

.. _cce_02_0326__response_metadata:

.. table:: **Table 5** Data structure of the metadata field

   +-------------------+--------+------------------------------------------------------+
   | Parameter         | Type   | Description                                          |
   +===================+========+======================================================+
   | uid               | String | Unique ID of the add-on instance.                    |
   +-------------------+--------+------------------------------------------------------+
   | name              | String | Add-on name.                                         |
   +-------------------+--------+------------------------------------------------------+
   | labels            | Object | Add-on labels in the format of key-value pairs.      |
   +-------------------+--------+------------------------------------------------------+
   | annotations       | Object | Add-on annotations in the format of key-value pairs. |
   +-------------------+--------+------------------------------------------------------+
   | updateTimestamp   | String | Time when the add-on instance was updated.           |
   +-------------------+--------+------------------------------------------------------+
   | creationTimestamp | String | Time when the add-on instance was created.           |
   +-------------------+--------+------------------------------------------------------+

.. _cce_02_0326__response_instancespec:

.. table:: **Table 6** Data structure of the spec field

   +---------------------+------------------+-----------------------------------------------------------------------------+
   | Parameter           | Type             | Description                                                                 |
   +=====================+==================+=============================================================================+
   | clusterID           | String           | Cluster ID.                                                                 |
   +---------------------+------------------+-----------------------------------------------------------------------------+
   | version             | String           | Add-on template version, for example, v1.0.0.                               |
   +---------------------+------------------+-----------------------------------------------------------------------------+
   | addonTemplateName   | String           | Add-on template name, for example, coredns.                                 |
   +---------------------+------------------+-----------------------------------------------------------------------------+
   | addonTemplateType   | String           | Add-on template type.                                                       |
   +---------------------+------------------+-----------------------------------------------------------------------------+
   | addonTemplateLabels | Array of strings | Group to which the add-on template belongs.                                 |
   +---------------------+------------------+-----------------------------------------------------------------------------+
   | description         | String           | Add-on template description.                                                |
   +---------------------+------------------+-----------------------------------------------------------------------------+
   | values              | Object           | Add-on template installation parameters. These parameters vary with add-on. |
   +---------------------+------------------+-----------------------------------------------------------------------------+

.. _cce_02_0326__response_status:

.. table:: **Table 7** Data structure of the status field

   +----------------+---------------------------------------------------------+----------------------------------------------------------------------+
   | Parameter      | Type                                                    | Description                                                          |
   +================+=========================================================+======================================================================+
   | status         | String                                                  | Add-on instance status.                                              |
   +----------------+---------------------------------------------------------+----------------------------------------------------------------------+
   | Reason         | String                                                  | Cause why the system failed to list add-on instances.                |
   +----------------+---------------------------------------------------------+----------------------------------------------------------------------+
   | message        | String                                                  | Details about the error that occurred when listing add-on instances. |
   +----------------+---------------------------------------------------------+----------------------------------------------------------------------+
   | targetVersions | Array of strings                                        | Versions to which the current add-on version can be upgraded.        |
   +----------------+---------------------------------------------------------+----------------------------------------------------------------------+
   | currentVersion | :ref:`versions <cce_02_0326__response_versions>` object | Current add-on version.                                              |
   +----------------+---------------------------------------------------------+----------------------------------------------------------------------+

.. _cce_02_0326__response_versions:

.. table:: **Table 8** Data structure of the versions field

   +-----------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------+
   | Parameter             | Type                                                                            | Description                                               |
   +=======================+=================================================================================+===========================================================+
   | version               | String                                                                          | Add-on version.                                           |
   +-----------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------+
   | input                 | Object                                                                          | Add-on installation parameters.                           |
   +-----------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------+
   | stable                | Boolean                                                                         | Indicates whether the add-on version is a stable release. |
   +-----------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------+
   | translate             | Object                                                                          | Translation information used by the GUI.                  |
   +-----------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------+
   | supportVersions       | Array of :ref:`supportVersions <cce_02_0326__response_supportversions>` objects | Cluster versions that support the add-on.                 |
   |                       |                                                                                 |                                                           |
   |                       |                                                                                 | .. note::                                                 |
   |                       |                                                                                 |                                                           |
   |                       |                                                                                 |    This field is not supported for the current version.   |
   +-----------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------+
   | creationTimestamp     | String                                                                          | Time when the add-on instance was created.                |
   +-----------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------+
   | updateTimestamp       | String                                                                          | Time when the add-on instance was updated.                |
   +-----------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------+

.. _cce_02_0326__response_supportversions:

.. table:: **Table 9** Data structure of the supportVersions field

   +----------------+------------------+----------------------------------------------------------------------------------------+
   | Parameter      | Type             | Description                                                                            |
   +================+==================+========================================================================================+
   | clusterType    | String           | Cluster type that supports the add-on.                                                 |
   +----------------+------------------+----------------------------------------------------------------------------------------+
   | clusterVersion | Array of strings | Cluster versions that support the add-on. The parameter value is a regular expression. |
   +----------------+------------------+----------------------------------------------------------------------------------------+

**Example response:**

.. code-block::

   {
       "apiVersion": "v3",
       "kind": "Addon",
       "items": [
           {
               "metadata": {
                   "uid": "8ca259cc-553b-11e9-926f-0255ac101a31",
                   "name": "storage-driver",
                   "creationTimestamp": "2019-04-02T11:36:26Z",
                   "updateTimestamp": "2019-04-02T11:36:26Z"
               },
               "apiVersion": "v3",
               "kind": "Addon",
               "spec": {
                   "addonTemplateName": "storage-driver",
                   "addonTemplateLogo": "https://192.168.48.66/cce-addon-aw1hz2u/storage-driverlogo.svg",
                   "addonTemplateType": "helm",
                   "values": {
                       "flavor": {
                           "replicas": 1
                       },
                       "basic": {
                           "obs_url": "",
                           "swr_user": "swr_test",
                           "euleros_version": "2.2.5",
                           "addon_version": "1.0.10",
                           "platform": "linux-amd64",
                           "swr_addr": "10.125.6.246:20202"
                       },
                       "parameters": {}
                   },
                   "description": "A kubernetes FlexVolume Driver used to support cloud storage",
                   "addonTemplateLabels": [
                       "Storage"
                   ],
                   "clusterID": "0c0e4a63-5539-11e9-95f7-0255ac10177e",
                   "version": "1.0.10"
               },
               "status": {
                   "message": "",
                   "Reason": "Install complete",
                   "currentVersion": {
                       "input": {
                           "basic": {
                               "obs_url": "",
                               "swr_user": "swr_test",
                               "euleros_version": "2.2.5",
                               "swr_addr": "10.125.6.246:20202"
                           },
                           "parameters": {}
                       },
                       "stable": true,
                       "creationTimestamp": "2019-03-29T13:45:37Z",
                       "version": "1.0.10",
                       "translate": {
                           "en_US": {
                               "addon": {
                                   "changeLog": "The plug-in is upgraded to enhance the storage plug-in function.",
                                   "description": "A kubernetes FlexVolume Driver used to support cloud storage"
                               }
                           },
                           "zh_CN": {
                               "addon": {
                                   "changeLog": "",
                                   "description": ""
                               }
                           }
                       },
                       "updateTimestamp": "2019-03-29T13:45:37Z"
                   },
                   "status": "running"
               }
           }
       ]
   }

Status Codes
------------

.. table:: **Table 10** Status codes

   =========== ===========
   Status Code Description
   =========== ===========
   200         OK
   =========== ===========

For the description about error status codes, see :ref:`Status Code <cce_02_0084>`.
