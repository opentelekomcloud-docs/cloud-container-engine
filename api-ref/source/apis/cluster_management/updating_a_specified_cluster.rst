:original_name: cce_02_0240.html

.. _cce_02_0240:

Updating a Specified Cluster
============================

Function
--------

This API is used to update information about a specified cluster.

URI
---

PUT /api/v3/projects/{project_id}/clusters/{cluster_id}

:ref:`Table 1 <cce_02_0240__table2027961241820>` describes the parameters of this API.

.. _cce_02_0240__table2027961241820:

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

:ref:`Table 2 <cce_02_0240__table172831182919>` and :ref:`Table 3 <cce_02_0240__table34052983203655>` describe the request parameters.

.. _cce_02_0240__table172831182919:

.. table:: **Table 2** Parameters in the **request** header

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

.. _cce_02_0240__table34052983203655:

.. table:: **Table 3** Parameters in the request body

   +-----------+-----------+------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | Parameter | Mandatory | Type                                                 | Description                                                                                                                |
   +===========+===========+======================================================+============================================================================================================================+
   | spec      | Yes       | :ref:`spec <cce_02_0240__table1034041612134>` object | Detailed description of the cluster targeted by this API. CCE creates or updates objects by defining or updating its spec. |
   +-----------+-----------+------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0240__table1034041612134:

.. table:: **Table 4** Data structure of the **spec** field

   +------------------+-----------------+------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter        | Mandatory       | Type                                                                               | Description                                                                                                                          |
   +==================+=================+====================================================================================+======================================================================================================================================+
   | description      | No              | String                                                                             | Cluster description.                                                                                                                 |
   +------------------+-----------------+------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | customSan        | No              | Array of strings                                                                   | Custom SAN field in the server certificate of the cluster API server, which must comply with the SSL and X509 format specifications. |
   |                  |                 |                                                                                    |                                                                                                                                      |
   |                  |                 |                                                                                    | #. Duplicate names are not allowed.                                                                                                  |
   |                  |                 |                                                                                    | #. Must comply with the IP address and domain name formats.                                                                          |
   |                  |                 |                                                                                    |                                                                                                                                      |
   |                  |                 |                                                                                    | example: SAN 1: DNS Name=example.com SAN 2: DNS Name=www.example.com SAN 3: DNS Name=example.net SAN 4: IP Address=93.184.216.34     |
   +------------------+-----------------+------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | containerNetwork | No              | :ref:`ContainerNetworkUpdate <cce_02_0240__request_containernetworkupdate>` object | Container networking parameters, including information about the container CIDR block.                                               |
   +------------------+-----------------+------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0240__request_containernetworkupdate:

.. table:: **Table 5** ContainerNetworkUpdate

   +-----------------+-----------------+----------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type                                                                       | Description                                                                                                                                                  |
   +=================+=================+============================================================================+==============================================================================================================================================================+
   | cidrs           | No              | Array of :ref:`ContainerCIDR <cce_02_0240__request_containercidr>` objects | List of container CIDR blocks. For clusters of v1.21 and later, if the cluster uses the VPC network model, container CIDR blocks can be added incrementally. |
   |                 |                 |                                                                            |                                                                                                                                                              |
   |                 |                 |                                                                            | The configuration cannot be changed after the cluster is updated.                                                                                            |
   +-----------------+-----------------+----------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0240__request_containercidr:

.. table:: **Table 6** ContainerCIDR

   +-----------+-----------+--------+--------------------------------------------------------------------------------------------+
   | Parameter | Mandatory | Type   | Description                                                                                |
   +===========+===========+========+============================================================================================+
   | cidr      | Yes       | String | Container CIDR block. Recommended: 10.0.0.0/12-19, 172.16.0.0/16-19, and 192.168.0.0/16-19 |
   +-----------+-----------+--------+--------------------------------------------------------------------------------------------+

**Example request**:

-  Updating the description of a cluster

   .. code-block::

      {
        "spec" : {
          "description" : "new description"
        }
      }

-  Updating only the custom SAN of the cluster certificate

   .. code-block::

      {
        "spec" : {
          "customSan" : [ "192.168.1.0", "example.com" ]
        }
      }

-  Updating both the cluster description and custom certificate SAN

   .. code-block::

      {
        "spec" : {
          "description" : "new description",
          "customSan" : [ "192.168.1.0", "example.com" ]
        }
      }

-  Adding a container CIDR block for a cluster of v1.21 or later that uses the VPC network model

   .. code-block::

      {
        "spec" : {
          "containerNetwork" : {
            "cidrs" : [ {
              "cidr" : "10.10.0.0/16"
            }, {
              "cidr" : "10.11.0.0/16"
            } ]
          }
        }
      }

Response
--------

**Response parameters:**

For details about the response parameters, see :ref:`Table 7 <cce_02_0240__table111553952019>`.

.. _cce_02_0240__table111553952019:

.. table:: **Table 7** Parameters in the response body

   +------------+---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Type                                                    | Description                                                                                                         |
   +============+=========================================================+=====================================================================================================================+
   | kind       | String                                                  | API type. For a cluster management API, the parameter must be set to **Cluster** and cannot be changed.             |
   +------------+---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------+
   | apiVersion | String                                                  | API version. The value is fixed at **v3** and cannot be changed.                                                    |
   +------------+---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------+
   | metadata   | :ref:`metadata <cce_02_0236__table669019286188>` object | Basic information about a cluster. metadata is a collection of attributes.                                          |
   +------------+---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------+
   | spec       | :ref:`spec <cce_02_0236__table195921039143517>` object  | Detailed description of the cluster to be created. CCE creates or updates objects by defining or updating its spec. |
   +------------+---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------+
   | status     | :ref:`status <cce_02_0236__table6749834132215>` object  | Cluster status and jobID of the job that reads a specified cluster.                                                 |
   +------------+---------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------+

**Example response:**

.. code-block::

   {
       "kind": "Cluster",
       "apiVersion": "v3",
       "metadata": {
           "name": "mycluster",
           "uid": "4d1ecb2c-229a-11e8-9c75-0255ac100ceb",
           "creationTimestamp": "2020-02-02 03:48:58.968214406 +0000 UTC",
           "updateTimestamp": "2020-02-02 06:39:36.844676088 +0000 UTC"
       },
       "spec": {
           "type": "VirtualMachine",
           "flavor": "cce.s1.small",
           "version": "v1.17.9-r0",
           "description": "new description",
           "az": "eu-de-01",
           "ipv6enable": false,
           "supportIstio": true,
           "hostNetwork": {
               "vpc": "4d1ecb2c-229a-11e8-9c75-0255ac100ceb",
               "subnet": "4d1ecb2c-229a-11e8-9c75-0255ac100ceb",
               "SecurityGroup": "5da0b181-e0a2-4981-87ac-1681545cd666"
           },
           "containerNetwork": {
               "mode": "overlay_l2",
               "cidr": "172.17.0.0/16"
           },
           "eniNetwork": {},
           "authentication": {
               "mode": "rbac",
               "authenticatingProxy": {}
           },
           "billingMode": 0,
           "extendParam": {
               "alpha.cce/fixPoolMask": "",
               "kubernetes.io/cpuManagerPolicy": "",
               "patchVersion": "",
               "upgradefrom": ""
           },
           "kubernetesSvcIpRange": "10.247.0.0/16",
           "kubeProxyMode": "iptables"
       },
       "status": {
           "phase": "Available",
           "endpoints": [
               {
                   "Internal": "https://192.168.0.61:5443",
               },
               {
                   "External": "https://10.185.69.54:5443",
               },
              {
                   "external_otc": "https://a140174a-2f3e-11e9-9f91-0255ac101405.cce.eu-de.otc.t-systems.com",            },
           ]
       }
   }

Status Code
-----------

:ref:`Table 8 <cce_02_0240__en-us_topic_0079614900_table46761928>` describes the status code of this API.

.. _cce_02_0240__en-us_topic_0079614900_table46761928:

.. table:: **Table 8** Status code

   +-------------+------------------------------------------------------------------+
   | Status Code | Description                                                      |
   +=============+==================================================================+
   | 200         | Information about the specified cluster is successfully updated. |
   +-------------+------------------------------------------------------------------+

For details about error status codes, see :ref:`Status Code <cce_02_0084>`.
