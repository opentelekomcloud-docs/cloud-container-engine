:original_name: cce_02_0356.html

.. _cce_02_0356:

Updating a Specified Node Pool
==============================

Function
--------

This API is used to update a specified node pool.

.. note::

   -  The URL for cluster management is in the format of **https://Endpoint/uri**. In the URL, **uri** indicates the resource path, that is, the path for API access.
   -  Currently, only the node pool name and scaling parameters in **spec** can be updated, such as **initialNodeCount, autoscaling, name, k8sTags, taints, login**, and **userTags**.

URI
---

PUT /api/v3/projects/{project_id}/clusters/{cluster_id}/nodepools/{nodepool_id}

:ref:`Table 1 <cce_02_0356__table127923532918>` describes the parameters of this API.

.. _cce_02_0356__table127923532918:

.. table:: **Table 1** Parameter description

   +-------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter   | Mandatory | Description                                                                                                                             |
   +=============+===========+=========================================================================================================================================+
   | project_id  | Yes       | Project ID. For details about how to obtain the project ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`.           |
   +-------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------+
   | cluster_id  | Yes       | Cluster ID. For details about how to obtain the cluster ID, see :ref:`How to Obtain Parameters in the API URI <cce_02_0271>`.           |
   +-------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------+
   | nodepool_id | Yes       | Node pool ID. For details about how to obtain the node pool ID, see :ref:`Listing All Node Pools in a Specified Cluster <cce_02_0269>`. |
   +-------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------+

Request
-------

**Request parameters**:

:ref:`Table 2 <cce_02_0356__table794104818135>` and :ref:`Table 3 <cce_02_0356__table1644211019>` lists the request parameters.

.. _cce_02_0356__table794104818135:

.. table:: **Table 2** Parameters in the request header

   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | String          | Description                                                                                                                                                                                                                                                                   |
   +=================+=================+=================+===============================================================================================================================================================================================================================================================================+
   | Content-Type    | Yes             | String          | Message body type (format).                                                                                                                                                                                                                                                   |
   |                 |                 |                 |                                                                                                                                                                                                                                                                               |
   |                 |                 |                 | Default: **application/json**                                                                                                                                                                                                                                                 |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | X-Auth-Token    | Yes             | String          | Requests for calling an API can be authenticated using either a token or AK/SK. If token-based authentication is used, this parameter is mandatory and must be set to a user token. For details on how to obtain a user token, see :ref:`API Usage Guidelines <cce_02_0004>`. |
   +-----------------+-----------------+-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0356__table1644211019:

.. table:: **Table 3** NodePool structure

   +-----------+-----------+--------------------------------------------------------+------------------------------+
   | Parameter | Mandatory | Type                                                   | Description                  |
   +===========+===========+========================================================+==============================+
   | metadata  | Yes       | :ref:`metadata <cce_02_0356__table13456192212>` object | Metadata of the node pool.   |
   +-----------+-----------+--------------------------------------------------------+------------------------------+
   | spec      | Yes       | :ref:`spec <cce_02_0356__table620623542313>` object    | Parameters of the node pool. |
   +-----------+-----------+--------------------------------------------------------+------------------------------+

.. _cce_02_0356__table13456192212:

.. table:: **Table 4** Data structure of the **metadata** field

   +-----------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                                                                                                                                              |
   +=================+=================+=================+==========================================================================================================================================================================================================+
   | name            | Yes             | String          | Node pool name.                                                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                          |
   |                 |                 |                 | Naming rule: Enter 1 to 50 characters that starts with a lowercase letter and cannot end with a hyphen (-). Lowercase letters, digits, and hyphens (-) are allowed. The value cannot be **DefaultPool**. |
   +-----------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0356__table620623542313:

.. table:: **Table 5** Data structure of the **spec** field

   +------------------+-----------------+-------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter        | Mandatory       | Type                                                                                            | Description                                                                                                                                                                                                                                                                                                                         |
   +==================+=================+=================================================================================================+=====================================================================================================================================================================================================================================================================================================================================+
   | initialNodeCount | Yes             | Integer                                                                                         | Expected number of nodes in this node pool. The value cannot be greater than the maximum number of nodes allowed for the cluster.                                                                                                                                                                                                   |
   +------------------+-----------------+-------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | autoscaling      | No              | autoscaling object                                                                              | Auto scaling parameters.                                                                                                                                                                                                                                                                                                            |
   +------------------+-----------------+-------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | nodeTemplate     | Yes             | :ref:`Table6 Data structure of the nodeTemplate field <cce_02_0354__table3150105216225>` object | Parameters of the node pool template.                                                                                                                                                                                                                                                                                               |
   |                  |                 |                                                                                                 |                                                                                                                                                                                                                                                                                                                                     |
   |                  |                 |                                                                                                 | If **az** is set to **random**, when you create a node pool or update the number of nodes in a node pool, a scaling task is triggered. The system selects an AZ from all AZs where scaling is allowed to add nodes based on priorities.                                                                                             |
   |                  |                 |                                                                                                 |                                                                                                                                                                                                                                                                                                                                     |
   |                  |                 |                                                                                                 | AZs with a smaller number of existing nodes have a higher priority. If AZs have the same number of nodes, the system selects the AZ based on the AZ sequence. Example:                                                                                                                                                              |
   |                  |                 |                                                                                                 |                                                                                                                                                                                                                                                                                                                                     |
   |                  |                 |                                                                                                 | -  Creating nodes in a node pool for the first time: Assume that AZ 1, AZ 2, and AZ 3 have available resources. These AZs have the same priority because they have no existing nodes. In this case, AZ 1 is selected by sequence to create all nodes to be added.                                                                   |
   |                  |                 |                                                                                                 | -  Adding nodes to a node pool that already has nodes:                                                                                                                                                                                                                                                                              |
   |                  |                 |                                                                                                 |                                                                                                                                                                                                                                                                                                                                     |
   |                  |                 |                                                                                                 |    -  Assume that the node pool has 1 node in AZ 1, 3 in AZ 2, and 2 in AZ 3, and that resources in AZ 1 have been sold out. Only AZ 2 and AZ 3 are available for scaling. In this case, AZ 3 has a higher priority than AZ 2 because AZ 3 has less nodes. Therefore, all nodes to be added will be created in AZ 3.                |
   |                  |                 |                                                                                                 |    -  Assume that the node pool has 1 node in AZ 1, 2 in AZ 2, and 2 in AZ 3, and that resources in AZ 1 have been sold out. Only AZ 2 and AZ 3 are available for scaling. In this case, AZ 2 and AZ 3 have the same priority because they have the same number of nodes. Therefore, all nodes to be added will be created in AZ 2. |
   +------------------+-----------------+-------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. table:: **Table 6** Data structure of the **autoscaling** field

   +-----------------------+-----------+---------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Mandatory | Type    | Description                                                                                                                                                                                                                 |
   +=======================+===========+=========+=============================================================================================================================================================================================================================+
   | enable                | No        | Boolean | Whether to enable auto scaling.                                                                                                                                                                                             |
   +-----------------------+-----------+---------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | minNodeCount          | No        | Integer | Minimum number of nodes after a scale-down if auto scaling is enabled.                                                                                                                                                      |
   +-----------------------+-----------+---------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | maxNodeCount          | No        | Integer | Maximum number of nodes after a scale-up if auto scaling is enabled. The value of this parameter must be greater than or equal to that of **minNodeCount** and does not exceed the maximum number of nodes for the cluster. |
   +-----------------------+-----------+---------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | scaleDownCooldownTime | No        | Integer | Interval during which nodes added after a scale-up will not be deleted, in minutes.                                                                                                                                         |
   +-----------------------+-----------+---------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | priority              | No        | Integer | Node pool weight. A higher weight indicates a higher priority in scale-up.                                                                                                                                                  |
   +-----------------------+-----------+---------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. table:: **Table 7** Data structure of the **nodeTemplate** field

   +-----------------+-----------------+----------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type                                                           | Description                                                                                                                                                                                                                                                                         |
   +=================+=================+================================================================+=====================================================================================================================================================================================================================================================================================+
   | k8sTags         | No              | Map<String,String>                                             | Kubernetes label, in the format of key-value pair. A maximum of 20 key-value pairs are allowed.                                                                                                                                                                                     |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                | -  **Key**: Enter 1 to 63 characters starting with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed. A DNS subdomain can be prefixed to a key and contain a maximum of 253 characters. Example DNS subdomain: example.com/my-key. |
   |                 |                 |                                                                | -  **Value**: The value can be left blank or a string of 1 to 63 characters starting with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed in the character string.                                                               |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                | Example:                                                                                                                                                                                                                                                                            |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                | .. code-block::                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                |    "k8sTags": {                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                |        "key": "value"                                                                                                                                                                                                                                                               |
   |                 |                 |                                                                |    }                                                                                                                                                                                                                                                                                |
   +-----------------+-----------------+----------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | userTags        | No              | Array of :ref:`Table 9 <cce_02_0356__request_usertag>` objects | **Definition**                                                                                                                                                                                                                                                                      |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                | Cloud server resource tags                                                                                                                                                                                                                                                          |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                | **Constraints**                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                | -  The key of a tag must be unique. The maximum number of custom tags supported by CCE varies depending on regions and cannot exceed 8.                                                                                                                                             |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                | -  If this parameter is not specified, the custom cloud server resource tags of the node pool will not be updated.                                                                                                                                                                  |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                | -  If the parameter is left empty, the custom cloud server resource tags of the node pool will be deleted.                                                                                                                                                                          |
   +-----------------+-----------------+----------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | taints          | No              | Array of :ref:`Table 10 <cce_02_0356__request_taint>` objects  | Taints added to the created node to set anti-affinity. The following three parameters are contained in every taint:                                                                                                                                                                 |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                | -  **Key**: Enter 1 to 63 characters starting with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed. A DNS subdomain can be prefixed to a key.                                                                                    |
   |                 |                 |                                                                | -  **Value**: The value can be a string of 1 to 63 characters starting with a letter or digit. Letters, digits, hyphens (-), underscores (_), and periods (.) are allowed.                                                                                                          |
   |                 |                 |                                                                | -  **Effect**: Set it to **NoSchedule**, **PreferNoSchedule**, or **NoExecute**.                                                                                                                                                                                                    |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                | Example:                                                                                                                                                                                                                                                                            |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                | .. code-block::                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                |                                                                                                                                                                                                                                                                                     |
   |                 |                 |                                                                |    "taints": [                                                                                                                                                                                                                                                                      |
   |                 |                 |                                                                |    {                                                                                                                                                                                                                                                                                |
   |                 |                 |                                                                |        "key": "status",                                                                                                                                                                                                                                                             |
   |                 |                 |                                                                |        "value": "unavailable",                                                                                                                                                                                                                                                      |
   |                 |                 |                                                                |        "effect": "NoSchedule"                                                                                                                                                                                                                                                       |
   |                 |                 |                                                                |    },                                                                                                                                                                                                                                                                               |
   |                 |                 |                                                                |    {                                                                                                                                                                                                                                                                                |
   |                 |                 |                                                                |        "key": "looks",                                                                                                                                                                                                                                                              |
   |                 |                 |                                                                |        "value": "bad",                                                                                                                                                                                                                                                              |
   |                 |                 |                                                                |        "effect": "NoSchedule"                                                                                                                                                                                                                                                       |
   |                 |                 |                                                                |    }]                                                                                                                                                                                                                                                                               |
   +-----------------+-----------------+----------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. table:: **Table 8** Data structure of the spec/extendParam field

   +-------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter               | Mandatory       | Type            | Description                                                                                                                                                                                                                |
   +=========================+=================+=================+============================================================================================================================================================================================================================+
   | maxPods                 | No              | Integer         | Maximum number of pods on the node.                                                                                                                                                                                        |
   +-------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | agency_name             | No              | String          | Specifies the IAM agency name.                                                                                                                                                                                             |
   +-------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | alpha.cce/preInstall    | No              | String          | Script required before the installation.                                                                                                                                                                                   |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | .. note::                                                                                                                                                                                                                  |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 |    The input value must be encoded using Base64. (Command: **echo -n "Content to be encoded" \| base64**)                                                                                                                  |
   +-------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | alpha.cce/postInstall   | No              | String          | **Details**:                                                                                                                                                                                                               |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | Post-installation script.                                                                                                                                                                                                  |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | The input value must be encoded using Base64. The method is as follows:                                                                                                                                                    |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | .. code-block::                                                                                                                                                                                                            |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 |    echo -n "*Content to be encoded*" | base64                                                                                                                                                                              |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | **Constraints**:                                                                                                                                                                                                           |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | The characters of both the pre-installation and post-installation scripts are centrally calculated, and the total number of characters after transcoding cannot exceed 10,240.                                             |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | **Options**:                                                                                                                                                                                                               |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | N/A                                                                                                                                                                                                                        |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | **Default value**:                                                                                                                                                                                                         |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | N/A                                                                                                                                                                                                                        |
   +-------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | alpha.cce/NodeImageID   | No              | String          | Mandatory if a custom image is used in creating a bare metal node.                                                                                                                                                         |
   +-------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | DockerLVMConfigOverride | No              | String          | Docker data disk configuration item. (This parameter has been discarded. Use the **storage** field instead.)The following is an example configuration:                                                                     |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | .. code-block::                                                                                                                                                                                                            |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 |    "DockerLVMConfigOverride":"dockerThinpool=vgpaas/90%VG;kubernetesLV=vgpaas/10%VG;diskType=evs;lvType=linear"                                                                                                            |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | In this example:                                                                                                                                                                                                           |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | -  **userLV**: size of the user space, for example, **vgpaas/20%VG**.                                                                                                                                                      |
   |                         |                 |                 | -  **userPath**: mount path of the user space, for example, **/home/wqt-test**.                                                                                                                                            |
   |                         |                 |                 | -  **diskType**: disk type. Currently, only the **evs**, **hdd**, and **ssd** are supported.                                                                                                                               |
   |                         |                 |                 | -  **lvType**: type of a logic volume. Currently, the value can be **linear** or **striped**.                                                                                                                              |
   |                         |                 |                 | -  **dockerThinpool**: Docker space size, for example, **vgpaas/60%VG**.                                                                                                                                                   |
   |                         |                 |                 | -  **kubernetesLV**: kubelet space size, for example, **vgpaas/20%VG**.                                                                                                                                                    |
   +-------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | publicKey               | No              | String          | Public key of the node. Used when creating a key pair.                                                                                                                                                                     |
   +-------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | nicMultiqueue           | No              | String          | ENI queue settings, the default setting is:                                                                                                                                                                                |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | .. code-block::                                                                                                                                                                                                            |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 |    "[{\"queue\":4}]"                                                                                                                                                                                                       |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | -  **queue** indicates the number of ENI queues.                                                                                                                                                                           |
   |                         |                 |                 | -  Supported proportions are {"1":128, "2":92, "4":92, "8":32, "16":16, "28":9}. That is, there is one queue, a maximum of 128 ENIs can be bound. If there are two queues, a maximum of 92 ENIs can be bound.              |
   |                         |                 |                 | -  A larger number of ENI queues indicates higher performance but fewer ENIs can be bound. The queue settings cannot be changed after the node pool is created.                                                            |
   +-------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | nicThreshold            | No              | String          | ENI pre-binding thresholds, the default setting is:                                                                                                                                                                        |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | .. code-block::                                                                                                                                                                                                            |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 |    "0.3:0.6"                                                                                                                                                                                                               |
   |                         |                 |                 |                                                                                                                                                                                                                            |
   |                         |                 |                 | -  The first decimal place indicates the low threshold. Formula: Minimum number of pre-bound ENIs = Min (128 x Low threshold, Maximum number of ENIs that can be bound to the queue)                                       |
   |                         |                 |                 | -  The second decimal place indicates the high threshold. Formula: Maximum number of pre-bound ENIs = Min (128 x High threshold, Maximum number of ENIs that can be bound to the queue)                                    |
   |                         |                 |                 | -  The high and low thresholds of the number of pre-bound ENIs are restricted by the maximum number of ENIs that can be bound to a queue.                                                                                  |
   |                         |                 |                 | -  ENIs bound to a BMS node = Number of ENIs currently used by pods + Number of pre-bound ENIs                                                                                                                             |
   |                         |                 |                 | -  CCE keeps binding ENIs to a BMS node to ensure that the minimum number of ENIs is always reached. (Min. pre-bound ENIs <= Number of pre-bound ENIs + Number of ENIs currently used by pods)                             |
   |                         |                 |                 | -  At every 2 minutes, CCE releases ENIs pre-bound to a BMS node to ensure that the maximum number of ENIs will not be exceeded. (Max. pre-bound ENIs >= Number of pre-bound ENIs + Number of ENIs currently used by pods) |
   |                         |                 |                 | -  Both the thresholds are one-decimal-place values ranging from 0.0 to 1.0. The low threshold must be smaller than or equal to the high one.                                                                              |
   |                         |                 |                 | -  This field can be configured only for BMS nodes in a CCE Turbo cluster.                                                                                                                                                 |
   |                         |                 |                 | -  Pre-binding ENIs can speed up workload creation but occupies IP addresses.                                                                                                                                              |
   +-------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0356__request_usertag:

.. table:: **Table 9** UserTag

   +-----------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                                                                                                                                                           |
   +=================+=================+=================+=======================================================================================================================================================================================================================+
   | key             | No              | String          | **Details**:                                                                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | Key of the cloud server tag                                                                                                                                                                                           |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | **Constraints**:                                                                                                                                                                                                      |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | The value cannot start with **CCE-**, **\__type_baremetal**, or **sys**.                                                                                                                                              |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | **Options**:                                                                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | The value contains a maximum of 128 characters. The tag key must be unique and cannot start or end with a space. It cannot contain non-printable ASCII characters (0-31) or the following special characters: =*<>,|/ |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | **Default value**:                                                                                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | N/A                                                                                                                                                                                                                   |
   +-----------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | value           | No              | String          | **Details**:                                                                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | Value of the cloud server tag                                                                                                                                                                                         |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | **Constraints**:                                                                                                                                                                                                      |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | None                                                                                                                                                                                                                  |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | **Options**:                                                                                                                                                                                                          |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | The value contains a maximum of 255 characters. The tag value cannot start or end with a space. It cannot contain non-printable ASCII characters (0-31) or the following special characters: =*<>,\|                  |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | **Default value**:                                                                                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                                                                                       |
   |                 |                 |                 | N/A                                                                                                                                                                                                                   |
   +-----------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0356__request_taint:

.. table:: **Table 10** Taint

   +-----------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory       | Type            | Description                                                                                                                                                            |
   +=================+=================+=================+========================================================================================================================================================================+
   | key             | Yes             | String          | **Definition**                                                                                                                                                         |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | Key of a Kubernetes taint                                                                                                                                              |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | **Constraints**                                                                                                                                                        |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | The value must contain 1 to 63 characters starting and ending with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed. |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | **Range**                                                                                                                                                              |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | N/A                                                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | **Default Value**                                                                                                                                                      |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | N/A                                                                                                                                                                    |
   +-----------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | value           | No              | String          | **Definition**                                                                                                                                                         |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | Value of a Kubernetes taint                                                                                                                                            |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | **Constraints**                                                                                                                                                        |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | The value must contain 1 to 63 characters starting and ending with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed. |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | **Range**                                                                                                                                                              |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | N/A                                                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | **Default Value**                                                                                                                                                      |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | N/A                                                                                                                                                                    |
   +-----------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | effect          | Yes             | String          | **Definition**                                                                                                                                                         |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | Effect of a Kubernetes taint                                                                                                                                           |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | **Constraints**                                                                                                                                                        |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | N/A                                                                                                                                                                    |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | **Range**                                                                                                                                                              |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | **NoSchedule**, **PreferNoSchedule**, and **NoExecute**                                                                                                                |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | **Default Value**                                                                                                                                                      |
   |                 |                 |                 |                                                                                                                                                                        |
   |                 |                 |                 | N/A                                                                                                                                                                    |
   +-----------------+-----------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

**Example request**:

.. code-block::

   {
     "metadata" : {
       "name" : "nodepool-name-change"
     },
     "spec" : {
       "nodeTemplate" : {
         "k8sTags" : {
           "cce.cloud.com/cce-nodepool": "nodepool-name-change",
            "change-tag": "value2"
       },
         "taints" : [ {
           "key" : "status",
           "value" : "unavailable",
           "effect" : "NoSchedule"
         } ],
       },
       "autoscaling" : {
         "enable" : true,
         "minNodeCount" : 2,
         "maxNodeCount" : 4,
         "scaleDownCooldownTime" : 10,
         "priority" : 2
       },
       "initialNodeCount" : 1
     }
   }

Response
--------

**Response parameters**:

:ref:`Table 26 <cce_02_0354__table835415466262>` describes the response parameters.

**Response example**:

.. code-block::

   {
       "kind": "NodePool",
       "apiVersion": "v3",
       "metadata": {
           "name": "nodepool-name-change",
           "uid": "feec6013-cd7e-11ea-8c7a-0255ac100be7"
       },
       "spec": {
           "initialNodeCount": 0,
           "type": "vm",
           "nodeTemplate": {
               "flavor": "s6.large.2",
               "az": "eu-de-01",
               "os": "EulerOS 2.5",
               "login": {
                   "sshKey": "KeyPair-nodepool",
                   "userPassword": {}
               },
               "rootVolume": {
                   "volumetype": "SATA",
                   "size": 40
               },
               "dataVolumes": [
                   {
                       "volumetype": "SATA",
                       "size": 100,
                       "extendParam": {
                           "useType": "docker"
                       }
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
               "runtime": {
                   "name":"docker"
               },
               "publicIP": {
                   "eip": {
                       "bandwidth": {}
                   }
               },
               "nodeNicSpec": {
                   "primaryNic": {
                       "subnetId": "31be174a-0c7f-4b71-bb0d-d325fecb90ef"
                   }
               },
               "billingMode": 0,
               "taints": [
                   {
                       "key": "change-taints",
                       "value": "value1",
                       "effect": "NoExecute"
                   }
               ],
               "k8sTags": {
                   "cce.cloud.com/cce-nodepool": "nodepool-name-change",
                   "change-tag": "value2"
               },
               "userTags": [
                   {
                       "key": "change-resource-tag",
                       "value": "value3"
                   }
               ],
               "extendParam": {
                   "DockerLVMConfigOverride": "dockerThinpool=vgpaas/90%VG;kubernetesLV=vgpaas/10%VG;diskType=evs;lvType=linear",
                   "alpha.cce/postInstall": "bHMgLWwK",
                   "alpha.cce/preInstall": "bHMgLWw=",
                   "maxPods": 110
               }
           },
           "autoscaling": {
               "enable": true,
               "minNodeCount": 2,
               "maxNodeCount": 4,
               "scaleDownCooldownTime": 10,
               "priority": 2
           },
           "nodeManagement": {
               "serverGroupReference": "2129f95a-f233-4cd8-a1b2-9c0acdf918d3"
           }
       },
       "status": {
           "currentNode": 0,
           "phase": ""
       }
   }

Status Code
-----------

:ref:`Table 11 <cce_02_0356__zh-cn_topic_0079614900_table46761928>` describes the status code of this API.

.. _cce_02_0356__zh-cn_topic_0079614900_table46761928:

.. table:: **Table 11** Status code

   =========== ================================================
   Status Code Description
   =========== ================================================
   200         The specified node pool is updated successfully.
   =========== ================================================

For details about error status codes, see :ref:`Status Code <cce_02_0084>`.
