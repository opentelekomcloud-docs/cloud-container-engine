:original_name: cce_02_0346.html

.. _cce_02_0346:

Modifying the Access Information of a Specified Cluster
=======================================================

Function
--------

This API is used to modify the access information of a specified cluster.

URI
---

PUT /api/v3/projects/{project_id}/clusters/{cluster_id}/mastereip

:ref:`Table 1 <cce_02_0346__tcb491e3c1e0f4805bc2a0f117c2e5291>` describes the parameters of the API.

.. _cce_02_0346__tcb491e3c1e0f4805bc2a0f117c2e5291:

.. table:: **Table 1** Parameter description

   ========== ========= ===========
   Parameter  Mandatory Description
   ========== ========= ===========
   project_id Yes       Project ID.
   cluster_id Yes       Cluster ID.
   ========== ========= ===========

Request
-------

**Request parameters:**

:ref:`Table 2 <cce_02_0346__t97650ea1b00a431b9ba9075aafc7e494>` describes the request parameters.

.. _cce_02_0346__t97650ea1b00a431b9ba9075aafc7e494:

.. table:: **Table 2** Parameter description

   +-----------+-----------+-----------------------------------------------------------------+-------------+
   | Parameter | Mandatory | Type                                                            | Description |
   +===========+===========+=================================================================+=============+
   | spec      | Yes       | :ref:`Table 3 <cce_02_0346__t18f5d0505d9048cab59a8ecd4fa7138d>` | ``-``       |
   +-----------+-----------+-----------------------------------------------------------------+-------------+

.. _cce_02_0346__t18f5d0505d9048cab59a8ecd4fa7138d:

.. table:: **Table 3** Data structure of the spec field

   +-----------------+-----------------+-------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type                                            | Description                                                                                                                                                    |
   +=================+=================+=================================================+================================================================================================================================================================+
   | action          | Yes             | String                                          | Whether to bind an elastic IP address to the cluster or unbind an elastic IP address from the cluster. The value is **bind** or **unbind** (case insensitive). |
   |                 |                 |                                                 |                                                                                                                                                                |
   |                 |                 |                                                 | .. note::                                                                                                                                                      |
   |                 |                 |                                                 |                                                                                                                                                                |
   |                 |                 |                                                 |    **id** needs to be configured only when **action** is set to **bind**.                                                                                      |
   +-----------------+-----------------+-------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | spec            | No              | :ref:`Table 4 <cce_02_0346__table985812714268>` | Information about the elastic IP address.                                                                                                                      |
   +-----------------+-----------------+-------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0346__table985812714268:

.. table:: **Table 4** Data structure of the spec-spec field

   ========= ========= ====== =============================
   Parameter Mandatory Type   Description
   ========= ========= ====== =============================
   id        Yes       String ID of the elastic IP address.
   ========= ========= ====== =============================

**Example request:**

-  Binding an elastic IP address to the cluster

   .. code-block::

      {
          "spec": {
              "action": "bind",
              "spec": {
                  "id": "0ef26920-3527-405d-a7b4-27106618c2d7"
              }
          }
      }

-  Unbinding an elastic IP address from the cluster

   .. code-block::

      {
          "spec": {
              "action": "unbind"
          }
      }

Response
--------

**Response parameters:**

:ref:`Table 5 <cce_02_0346__tab67b852d83a4de9b60d1595df71492e>` describes the response parameters.

.. _cce_02_0346__tab67b852d83a4de9b60d1595df71492e:

.. table:: **Table 5** Response parameters

   +-----------+-----------------------------------------------------------------+-------------------+
   | Parameter | Type                                                            | Description       |
   +===========+=================================================================+===================+
   | metadata  | json                                                            | Metadata.         |
   +-----------+-----------------------------------------------------------------+-------------------+
   | spec      | :ref:`Table 6 <cce_02_0346__tce0b898226db4163888e3bf7c290b876>` | ``-``             |
   +-----------+-----------------------------------------------------------------+-------------------+
   | status    | :ref:`Table 7 <cce_02_0346__t500749d17adf492d8e2535fe4933b586>` | Cluster endpoint. |
   +-----------+-----------------------------------------------------------------+-------------------+

.. _cce_02_0346__tce0b898226db4163888e3bf7c290b876:

.. table:: **Table 6** Data structure of the spec field

   +-----------+-------------------------------------------------+-----------------------------------------------------------------------------------------------------------------+
   | Parameter | Type                                            | Description                                                                                                     |
   +===========+=================================================+=================================================================================================================+
   | action    | String                                          | Whether an EIP is bound to or unbound from the cluster. The value is **bind** or **unbind** (case insensitive). |
   +-----------+-------------------------------------------------+-----------------------------------------------------------------------------------------------------------------+
   | spec      | :ref:`Table 4 <cce_02_0346__table985812714268>` | Information about the elastic IP address.                                                                       |
   +-----------+-------------------------------------------------+-----------------------------------------------------------------------------------------------------------------+
   | elasticIp | String                                          | Elastic IP address.                                                                                             |
   +-----------+-------------------------------------------------+-----------------------------------------------------------------------------------------------------------------+

.. _cce_02_0346__t500749d17adf492d8e2535fe4933b586:

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
           "action": "bind",
           "spec": {
               "id": "0ef26920-3527-405d-a7b4-27106618c2d7",
               "eip": {
                   "bandwidth": {
                       "size": 5,
                       "sharetype": "PER"
                   }
               },
               "IsDynamic": false
           },
           "elasticIp": "10.154.50.11"
       },
       "status": {
           "privateEndpoint": "https://172.16.0.86:5443",
           "publicEndpoint": "https://10.154.50.11:5443"
       }
   }

Status Code
-----------

:ref:`Table 8 <cce_02_0346__tcb712722097d4597ae95bec996421736>` describes the status code of the API.

.. _cce_02_0346__tcb712722097d4597ae95bec996421736:

.. table:: **Table 8** Status code

   =========== ===========================================================
   Status Code Description
   =========== ===========================================================
   200         The progress of the specified job is successfully obtained.
   =========== ===========================================================

For details about error status codes, see :ref:`Status Code <cce_02_0084>`.
