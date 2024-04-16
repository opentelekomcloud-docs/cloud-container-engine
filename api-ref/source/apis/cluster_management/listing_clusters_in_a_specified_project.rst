:original_name: cce_02_0239.html

.. _cce_02_0239:

Listing Clusters in a Specified Project
=======================================

Function
--------

This API is used to obtain details about all clusters in a specified project.

URI
---

GET /api/v3/projects/{project_id}/clusters

:ref:`Table 1 <cce_02_0239__table2027961241820>` describes the parameters of this API.

.. _cce_02_0239__table2027961241820:

.. table:: **Table 1** Parameter description

   +------------+-----------+----------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Mandatory | Description                                                                                                                |
   +============+===========+============================================================================================================================+
   | project_id | Yes       | Project ID. For details on how to obtain the project ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`. |
   +------------+-----------+----------------------------------------------------------------------------------------------------------------------------+

Request
-------

**Request parameters**:

:ref:`Table 2 <cce_02_0239__table538113720514>` lists the request parameters.

.. _cce_02_0239__table538113720514:

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

:ref:`Table 3 <cce_02_0239__en-us_topic_0079616779_en-us_topic_0079614912_ref458774242>` describes the response parameters.

.. _cce_02_0239__en-us_topic_0079616779_en-us_topic_0079614912_ref458774242:

.. table:: **Table 3** Response parameters

   +------------+--------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | Parameter  | Type                                                   | Description                                                                                                    |
   +============+========================================================+================================================================================================================+
   | kind       | String                                                 | API type. The value is fixed at **Cluster** and cannot be changed.                                             |
   +------------+--------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | apiVersion | String                                                 | API version. The value is fixed at **v3** and cannot be changed.                                               |
   +------------+--------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | items      | :ref:`items <cce_02_0239__table34052983203655>` object | A list of details for all clusters in the current project. You can filter clusters by **items.metadata.name**. |
   +------------+--------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+

.. _cce_02_0239__table34052983203655:

.. table:: **Table 4** Data structure of the **items** field

   +------------+---------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | Parameter  | Type                                                    | Description                                                                                                                |
   +============+=========================================================+============================================================================================================================+
   | kind       | String                                                  | API type. The value is fixed at **Cluster** and cannot be changed.                                                         |
   +------------+---------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | apiVersion | String                                                  | API version. The value is fixed at **v3** and cannot be changed.                                                           |
   +------------+---------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | metadata   | :ref:`metadata <cce_02_0236__table669019286188>` object | Cluster metadata, which is a collection of attributes.                                                                     |
   +------------+---------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | spec       | :ref:`spec <cce_02_0236__table195921039143517>` object  | Detailed description of the cluster targeted by this API. CCE creates or updates objects by defining or updating its spec. |
   +------------+---------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
   | status     | :ref:`status <cce_02_0238__table6749834132215>` object  | Cluster status and jobID of the job that lists clusters in a specified project.                                            |
   +------------+---------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+

**Response example**:

.. code-block::

   {
       "kind": "Cluster",
       "apiVersion": "v3",
       "items": [
           {
               "kind": "Cluster",
               "apiVersion": "v3",
               "metadata": {
                   "name": "mycluster",
                   "uid": "4d1ecb2c-229a-11e8-9c75-0255ac100ceb",
                   "creationTimestamp": "2020-02-02 03:48:58.968214406 +0000 UTC",
                   "updateTimestamp": "2020-02-02 04:05:29.386391813 +0000 UTC"
               },
               "spec": {
                   "type": "VirtualMachine",
                   "flavor": "cce.s1.small",
                   "version": "v1.17.9-r0",
                   "description": "awesome cluster",
                   "ipv6enable": false,
                   "supportIstio": true,
                   "hostNetwork": {
                       "vpc": "f0c12911-4fdb-4284-9230-7ffb0860826a",
                       "subnet": "ac274229-fd2e-4695-9f01-a0c1372b8006",
                       "SecurityGroup": "5da0b181-e0a2-4981-87ac-1681545cd666"
                   },
                   "containerNetwork": {
                       "mode": "overlay_l2",
                       "cidr": "172.16.0.0/16"
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
                   "external_otc": "https://a140174a-2f3e-11e9-9f91-0255ac101405.cce.eu-de.otc.t-systems.com",            }
           ]
       }
   }

Status Code
-----------

:ref:`Table 5 <cce_02_0239__en-us_topic_0079614900_table46761928>` describes the status code of this API.

.. _cce_02_0239__en-us_topic_0079614900_table46761928:

.. table:: **Table 5** Status code

   +-------------+-------------------------------------------------------------------------------+
   | Status Code | Description                                                                   |
   +=============+===============================================================================+
   | 200         | Information about clusters in the specified project is successfully obtained. |
   +-------------+-------------------------------------------------------------------------------+

For details about error status codes, see :ref:`Status Code <cce_02_0084>`.
