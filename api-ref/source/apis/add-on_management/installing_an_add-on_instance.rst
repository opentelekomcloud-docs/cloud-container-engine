:original_name: cce_02_0322.html

.. _cce_02_0322:

Installing an Add-on Instance
=============================

Function
--------

This API is used to install an add-on instance by using the add-on template. One or more instances will be created for the installed add-on.

.. note::

   The URL for add-on management is in the format of **https://{clusterid}.Endpoint/uri.** In the URL, *{clusterid}* indicates the cluster ID, and *uri* indicates the resource path, that is, the path for API access.

URI
---

POST /api/v3/addons

Request
-------

**Request parameters:**

:ref:`Table 1 <cce_02_0322__headerparameter>` lists the request parameters.

.. _cce_02_0322__headerparameter:

.. table:: **Table 1** Parameters in the request header

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

.. table:: **Table 2** Parameters in the request body

   +------------+-----------+-----------------------------------------------------------+-----------------------------------------------------------------------------+
   | Parameter  | Mandatory | Type                                                      | Description                                                                 |
   +============+===========+===========================================================+=============================================================================+
   | kind       | Yes       | String                                                    | API type. The value is fixed at **Addon** and cannot be changed.            |
   +------------+-----------+-----------------------------------------------------------+-----------------------------------------------------------------------------+
   | apiVersion | Yes       | String                                                    | API version. The value is fixed at **v3** and cannot be changed.            |
   +------------+-----------+-----------------------------------------------------------+-----------------------------------------------------------------------------+
   | metadata   | Yes       | :ref:`metadata <cce_02_0322__table12181142512286>` object | Basic information about the add-on. Metadata is a collection of attributes. |
   +------------+-----------+-----------------------------------------------------------+-----------------------------------------------------------------------------+
   | spec       | Yes       | :ref:`spec <cce_02_0322__instancerequestspec>` object     | Detailed description of add-on installation or upgrade.                     |
   +------------+-----------+-----------------------------------------------------------+-----------------------------------------------------------------------------+

.. _cce_02_0322__table12181142512286:

.. table:: **Table 3** Data structure of the metadata field

   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                          |
   +=================+=================+=================+======================================================================================+
   | annotations     | Yes             | Object          | Add-on annotations in the format of key-value pairs.                                 |
   |                 |                 |                 |                                                                                      |
   |                 |                 |                 | For add-on installation, the value is fixed at **{"addon.install/type":"install"}**. |
   +-----------------+-----------------+-----------------+--------------------------------------------------------------------------------------+

.. _cce_02_0322__instancerequestspec:

.. table:: **Table 4** Data structure of the spec field

   +-------------------+-----------+--------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter         | Mandatory | Type   | Description                                                                                                                                                                                                                                                                                                                                                   |
   +===================+===========+========+===============================================================================================================================================================================================================================================================================================================================================================+
   | clusterID         | Yes       | String | Cluster ID.                                                                                                                                                                                                                                                                                                                                                   |
   +-------------------+-----------+--------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | version           | Yes       | String | Version number of the add-on to be installed or upgraded, for example, v1.0.0.                                                                                                                                                                                                                                                                                |
   +-------------------+-----------+--------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | addonTemplateName | Yes       | String | Name of the add-on template to be installed, for example, coredns.                                                                                                                                                                                                                                                                                            |
   +-------------------+-----------+--------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | values            | Yes       | Object | Add-on template installation parameters (varying depending on the add-on). During the add-on upgrade, you need to specify all the installation parameters. If the parameters are not specified, the default values in the add-on template are used. The current add-on installation parameters can be obtained through the API for querying add-on instances. |
   +-------------------+-----------+--------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0322__table634384511511:

.. table:: **Table 5** Data structure of the values field

   ========= ========= ====== ================================
   Parameter Mandatory Type   Description
   ========= ========= ====== ================================
   basic     Yes       Object Basic add-on information.
   custom    No        Object Custom parameters of the add-on.
   ========= ========= ====== ================================

**Example request:**

.. code-block::

   {
       "metadata": {
           "annotations": {
               "addon.install/type": "install"
           }
       },
       "spec": {
           "clusterID": "9dd81b6e-e8e5-11ea-8aeb-0255ac11161d",
           "version": "1.1.10",
           "addonTemplateName": "gpu-beta",
           "values": {
               "basic": {
                   "obs_url": "obs.eu-de.otc.t-systems.com",
                   "region": "eu-de",
                   "swr_addr": "10.125.7.25:20202",
                   "swr_user": "test",
                   "rbac_enabled": true
               },
               "custom": {
                   "is_driver_from_nvidia": true,
                   "nvidia_driver_download_url": "https://us.download.nvidia.com/tesla/396.37/NVIDIA-Linux-x86_64-396.37.run"
               }
           }
       }
   }

Response
--------

**Response parameters:**

For the description about response parameters, see :ref:`Table 6 <cce_02_0322__responseparameter>`.

.. _cce_02_0322__responseparameter:

.. table:: **Table 6** Parameters in the response body

   +------------+---------------------------------------------------------+-----------------------------------------------------------------------------+
   | Parameter  | Type                                                    | Description                                                                 |
   +============+=========================================================+=============================================================================+
   | kind       | String                                                  | API type. The value is fixed at **Addon** and cannot be changed.            |
   +------------+---------------------------------------------------------+-----------------------------------------------------------------------------+
   | apiVersion | String                                                  | API version. The value is fixed at **v3** and cannot be changed.            |
   +------------+---------------------------------------------------------+-----------------------------------------------------------------------------+
   | metadata   | :ref:`metadata <cce_02_0322__response_metadata>` object | Basic information about the add-on. Metadata is a collection of attributes. |
   +------------+---------------------------------------------------------+-----------------------------------------------------------------------------+
   | spec       | :ref:`spec <cce_02_0322__response_instancespec>` object | Detailed description of the add-on instance.                                |
   +------------+---------------------------------------------------------+-----------------------------------------------------------------------------+
   | status     | :ref:`status <cce_02_0322__response_status>` object     | Add-on instance status.                                                     |
   +------------+---------------------------------------------------------+-----------------------------------------------------------------------------+

.. _cce_02_0322__response_metadata:

.. table:: **Table 7** Data structure of the metadata field

   +-------------------+--------+------------------------------------------------------+
   | Parameter         | Type   | Description                                          |
   +===================+========+======================================================+
   | uid               | String | Unique ID of the add-on.                             |
   +-------------------+--------+------------------------------------------------------+
   | name              | String | Add-on name.                                         |
   +-------------------+--------+------------------------------------------------------+
   | labels            | Object | Add-on labels in the format of key-value pairs.      |
   +-------------------+--------+------------------------------------------------------+
   | annotations       | Object | Add-on annotations in the format of key-value pairs. |
   +-------------------+--------+------------------------------------------------------+
   | updateTimestamp   | String | Time when the add-on was updated.                    |
   +-------------------+--------+------------------------------------------------------+
   | creationTimestamp | String | Time when the add-on was created.                    |
   +-------------------+--------+------------------------------------------------------+

.. _cce_02_0322__response_instancespec:

.. table:: **Table 8** Data structure of the spec field

   +---------------------+------------------+-----------------------------------------------------------------------------------------+
   | Parameter           | Type             | Description                                                                             |
   +=====================+==================+=========================================================================================+
   | clusterID           | String           | Cluster ID.                                                                             |
   +---------------------+------------------+-----------------------------------------------------------------------------------------+
   | version             | String           | Add-on template version, for example, v1.0.0.                                           |
   +---------------------+------------------+-----------------------------------------------------------------------------------------+
   | addonTemplateName   | String           | Add-on template name, for example, coredns.                                             |
   +---------------------+------------------+-----------------------------------------------------------------------------------------+
   | addonTemplateType   | String           | Add-on template type.                                                                   |
   +---------------------+------------------+-----------------------------------------------------------------------------------------+
   | addonTemplateLabels | Array of strings | Group to which the add-on template belongs.                                             |
   +---------------------+------------------+-----------------------------------------------------------------------------------------+
   | addonTemplateLogo   | String           | OBS address of the current add-on logo.                                                 |
   +---------------------+------------------+-----------------------------------------------------------------------------------------+
   | description         | String           | Add-on template description.                                                            |
   +---------------------+------------------+-----------------------------------------------------------------------------------------+
   | values              | Object           | Add-on template installation parameters. These parameters vary depending on the add-on. |
   +---------------------+------------------+-----------------------------------------------------------------------------------------+

.. _cce_02_0322__response_status:

.. table:: **Table 9** Data structure of the status field

   +----------------+---------------------------------------------------------------+---------------------------------------------------------------+
   | Parameter      | Type                                                          | Description                                                   |
   +================+===============================================================+===============================================================+
   | status         | String                                                        | Add-on instance status.                                       |
   +----------------+---------------------------------------------------------------+---------------------------------------------------------------+
   | Reason         | String                                                        | Cause of unsuccessful add-on installation.                    |
   +----------------+---------------------------------------------------------------+---------------------------------------------------------------+
   | message        | String                                                        | Installation error details.                                   |
   +----------------+---------------------------------------------------------------+---------------------------------------------------------------+
   | targetVersions | Array of strings                                              | Versions to which the current add-on version can be upgraded. |
   +----------------+---------------------------------------------------------------+---------------------------------------------------------------+
   | currentVersion | :ref:`currentVersion <cce_02_0322__response_versions>` object | Current version of the add-on instance.                       |
   +----------------+---------------------------------------------------------------+---------------------------------------------------------------+

.. _cce_02_0322__response_versions:

.. table:: **Table 10** Data structure of the currentVersion field

   +-------------------+---------------------------------------------------------------------------------+-------------------------------------------------+
   | Parameter         | Type                                                                            | Description                                     |
   +===================+=================================================================================+=================================================+
   | version           | String                                                                          | Add-on version.                                 |
   +-------------------+---------------------------------------------------------------------------------+-------------------------------------------------+
   | input             | Object                                                                          | Add-on installation parameters.                 |
   +-------------------+---------------------------------------------------------------------------------+-------------------------------------------------+
   | stable            | Boolean                                                                         | Whether the add-on version is a stable release. |
   +-------------------+---------------------------------------------------------------------------------+-------------------------------------------------+
   | translate         | Object                                                                          | Translation information used by the GUI.        |
   +-------------------+---------------------------------------------------------------------------------+-------------------------------------------------+
   | supportVersions   | Array of :ref:`supportVersions <cce_02_0322__response_supportversions>` objects | Cluster versions that support the add-on.       |
   +-------------------+---------------------------------------------------------------------------------+-------------------------------------------------+
   | creationTimestamp | String                                                                          | Time when the add-on was created.               |
   +-------------------+---------------------------------------------------------------------------------+-------------------------------------------------+
   | updateTimestamp   | String                                                                          | Time when the add-on was updated.               |
   +-------------------+---------------------------------------------------------------------------------+-------------------------------------------------+

.. _cce_02_0322__response_supportversions:

.. table:: **Table 11** Data structure of the supportVersions field

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
       "kind": "Addon",
       "apiVersion": "v3",
       "metadata": {
           "uid": "b6ba182c-f7c5-11ea-a975-0255ac111605",
           "name": "gpu-beta",
           "creationTimestamp": "2020-09-16T02:38:33Z",
           "updateTimestamp": "2020-09-16T02:38:33Z"
       },
       "spec": {
           "clusterID": "9dd81b6e-e8e5-11ea-8aeb-0255ac11161d",
           "version": "1.1.10",
           "addonTemplateName": "gpu-beta",
           "addonTemplateType": "helm",
           "addonTemplateLogo": "https://obs.eu-de.otc.t-systems.com/cce-addon-eu-de-aw1hz2u/gpu-betalogo.svg",
           "addonTemplateLabels": [
               "Accelerator"
           ],
           "description": "A device plugin for nvidia.com/gpu resource on nvidia driver",
           "values": {
               "basic": {
                   "obs_url": "obs.eu-de.otc.t-systems.com",
                   "platform": "linux-amd64",
                   "rbac_enabled": true,
                   "region": "eu-de",
                   "swr_addr": "10.125.7.25:20202",
                   "swr_user": "test"
               },
               "custom": {
                   "is_driver_from_nvidia": true,
                   "nvidia_driver_download_url": "https://us.download.nvidia.com/tesla/396.37/NVIDIA-Linux-x86_64-396.37.run"
               }
           }
       },
       "status": {
           "status": "installing",
           "Reason": "",
           "message": "",
           "targetVersions": null,
           "currentVersion": {
               "version": "1.1.10",
               "input": {
                   "basic": {
                       "obs_url": "obs.eu-de.otc.t-systems.com",
                       "region": "eu-de",
                       "swr_addr": "10.125.7.25:20202",
                       "swr_user": "test"
                   },
                   "parameters": {
                       "custom": {
                           "is_driver_from_nvidia": true,
                           "nvidia_driver_download_url": ""
                       }
                   }
               },
               "stable": true,
               "translate": {
                   "en_US": {
                       "addon": {
                           "changeLog": "1.Supports both the default driver link address and the user-definable driver address download driver 2.Support kubernetes 1.15/1.17",
                           "description": "A device plugin for nvidia.com/gpu resource on nvidia driver"
                       },
                       "description": {
                           "Parameters.custom.drivers_info.cuda": "CUDA Toolkit",
                           "Parameters.custom.drivers_info.product": "Product",
                           "Parameters.custom.drivers_info.product_series": "Product Series",
                           "Parameters.custom.drivers_info.product_type": "Product Type",
                           "Parameters.custom.nvidia_driver_download_url": "Download the nvidia driver accroding to the input link"
                       },
                       "key": {
                           "Parameters.custom.nvidia_driver_download_url": "Nvidia Driver"
                       }
                   }
               },
               "supportVersions": null,
               "creationTimestamp": "2020-08-21T08:25:51Z",
               "updateTimestamp": "2020-08-28T12:21:11Z"
           }
       }
   }

Status Codes
------------

.. table:: **Table 12** Status codes

   =========== ===========
   Status Code Description
   =========== ===========
   201         OK
   =========== ===========

For the description about error status codes, see :ref:`Status Code <cce_02_0084>`.
