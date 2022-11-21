:original_name: cce_02_0347.html

.. _cce_02_0347:

Querying the Access Information of a Specified Cluster
======================================================

Function
--------

This API is used to query the access information of a specified cluster.

.. note::

   The URL for cluster management is in the format of **https://Endpoint/uri**. In the URL, **uri** indicates the resource path, that is, the path for API access.

URI
---

GET /api/v3/projects/{project_id}/clusters/{cluster_id}/openapi

:ref:`Table 1 <cce_02_0347__tcb491e3c1e0f4805bc2a0f117c2e5291>` describes the parameters of the API.

.. _cce_02_0347__tcb491e3c1e0f4805bc2a0f117c2e5291:

.. table:: **Table 1** Parameter description

   ========== ========= ====== ===========
   Parameter  Mandatory Type   Description
   ========== ========= ====== ===========
   project_id Yes       String Project ID.
   cluster_id Yes       String Cluster ID.
   ========== ========= ====== ===========

Request
-------

N/A

Response
--------

**Response parameters:**

:ref:`Table 2 <cce_02_0347__tab67b852d83a4de9b60d1595df71492e>` describes the response parameters.

.. _cce_02_0347__tab67b852d83a4de9b60d1595df71492e:

.. table:: **Table 2** Response parameters

   +-----------+-----------------------------------------------------------------+-------------------+
   | Parameter | Type                                                            | Description       |
   +===========+=================================================================+===================+
   | metadata  | json                                                            | Metadata.         |
   +-----------+-----------------------------------------------------------------+-------------------+
   | spec      | :ref:`Table 3 <cce_02_0347__ta3c0ec2411934d01ad2031e6ea7a7106>` | ``-``             |
   +-----------+-----------------------------------------------------------------+-------------------+
   | status    | :ref:`Table 7 <cce_02_0347__t500749d17adf492d8e2535fe4933b586>` | Cluster endpoint. |
   +-----------+-----------------------------------------------------------------+-------------------+

.. _cce_02_0347__ta3c0ec2411934d01ad2031e6ea7a7106:

.. table:: **Table 3** Data structure of the spec field

   +-----------+--------------------------------------------------+-------------------------------------------+
   | Parameter | Type                                             | Description                               |
   +===========+==================================================+===========================================+
   | spec      | :ref:`Table 4 <cce_02_0347__table5860192685015>` | Information about the elastic IP address. |
   +-----------+--------------------------------------------------+-------------------------------------------+

.. _cce_02_0347__table5860192685015:

.. table:: **Table 4** Data structure of the spec-spec field

   +-----------+-------------------------------------------------+--------------------------------------------+
   | Parameter | Type                                            | Description                                |
   +===========+=================================================+============================================+
   | id        | String                                          | ID of the elastic IP address.              |
   +-----------+-------------------------------------------------+--------------------------------------------+
   | eip       | :ref:`Table 5 <cce_02_0347__table423743115136>` | Information about the elastic IP address.  |
   +-----------+-------------------------------------------------+--------------------------------------------+
   | IsDynamic | Boolean                                         | Whether the elastic IP address is dynamic. |
   +-----------+-------------------------------------------------+--------------------------------------------+

.. _cce_02_0347__table423743115136:

.. table:: **Table 5** Data structure of the eip field

   +-----------+-------------------------------------------------+----------------------------------------------------+
   | Parameter | Type                                            | Description                                        |
   +===========+=================================================+====================================================+
   | bandwidth | :ref:`Table 6 <cce_02_0347__table163165523719>` | Specifies the bandwidth of the elastic IP address. |
   +-----------+-------------------------------------------------+----------------------------------------------------+

.. _cce_02_0347__table163165523719:

.. table:: **Table 6** Data structure of the bandwidth field

   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------+
   | Parameter             | Type                  | Description                                                                                   |
   +=======================+=======================+===============================================================================================+
   | size                  | Integer               | Specifies the bandwidth (Mbit/s).                                                             |
   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------+
   | sharetype             | String                | Specifies the bandwidth sharing type.                                                         |
   |                       |                       |                                                                                               |
   |                       |                       | Enumerated values: **PER** (indicates exclusive bandwidth) and **WHOLE** (indicates sharing). |
   +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------+

.. _cce_02_0347__t500749d17adf492d8e2535fe4933b586:

.. table:: **Table 7** Data structure of the status field

   =============== ====== ===================================
   Parameter       Type   Description
   =============== ====== ===================================
   privateEndpoint String Address for access within the VPC.
   publicEndpoint  String Address for access outside the VPC.
   =============== ====== ===================================

**Example response:**

.. code-block::

   {
       "metadata": {},
       "spec": {
           "spec": {
               "id": "0ead681e-9f94-4599-8a21-e2a1950da121",
               "eip": {
                   "bandwidth": {
                       "size": 5,
                       "sharetype": "PER"
                   }
               },
               "IsDynamic": false
           }
       },
       "status": {
           "privateEndpoint": "https://192.168.0.189:5443",
           "publicEndpoint": "https://10.154.50.197:5443"
       }
   }

Status Code
-----------

:ref:`Table 8 <cce_02_0347__table5493720464>` describes the status code of the API.

.. _cce_02_0347__table5493720464:

.. table:: **Table 8** Status code

   =========== ===========================================================
   Status Code Description
   =========== ===========================================================
   200         The progress of the specified job is successfully obtained.
   =========== ===========================================================

For details about error status codes, see :ref:`Status Code <cce_02_0084>`.
