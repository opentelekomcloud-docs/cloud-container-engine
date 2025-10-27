:original_name: cce_10_0839.html

.. _cce_10_0839:

(Recommended) Creating an SFS Turbo Subdirectory Using a Dynamic PV
===================================================================

When an SFS Turbo volume is mounted to a workload container, the root directory is mounted to the container by default. However, the minimum capacity of an SFS Turbo volume is 500 GiB, which exceeds the capacity required by most workloads, leading to a waste of storage resources. CCE enables efficient utilization of storage capacity by creating SFS Turbo subdirectories dynamically when you create a PVC. If your Everest version is 2.4.73, you can configure the capacities of these subdirectories. This allows multiple workloads to share the SFS Turbo file system.

Prerequisites
-------------

-  You have created a cluster and installed the :ref:`CCE Container Storage (Everest) <cce_10_0066>` add-on of v2.3.23 or later in the cluster.
-  To create a cluster using commands, ensure kubectl is used. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.
-  You have created an available SFS Turbo file system, and the SFS Turbo file system and the cluster are in the same VPC.

Notes and Restrictions on Subdirectory Quotas
---------------------------------------------

-  If the subdirectory quota is less than 1 GiB, it is automatically set to 1 GiB. The maximum quota cannot exceed the capacity of the target SFS Turbo file system. The minimum scale-out step is 1 GiB, and capacity reduction is not allowed.
-  Once a subdirectory quota is configured, it cannot be canceled.
-  The number of files or subdirectories in a file system is determined by the quota capacity (in KB) divided by 16. The upper limit is set at 1 billion and cannot be changed by users.

Dynamically Creating an SFS Turbo Subdirectory Using the Console
----------------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** tab. Click **Create PVC** in the upper right corner. In the dialog box displayed, configure PVC parameters.

   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                                                                                                                                               |
   +===================================+===========================================================================================================================================================================================================================+
   | PVC Type                          | In this example, select **SFS Turbo**.                                                                                                                                                                                    |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | PVC Name                          | Enter the PVC name, which must be unique in a namespace.                                                                                                                                                                  |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Creation Method                   | Select **New subdirectory**.                                                                                                                                                                                              |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Storage Classes                   | Choose **csi-sfsturbo**. You can customize a StorageClass and configure its reclaim policy and binding mode. For details, see :ref:`Creating a StorageClass Through the Console <cce_10_0380__section1074117311660>`.     |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Access Mode                       | SFS Turbo volumes support only **ReadWriteMany**, indicating that a storage volume can be mounted to multiple nodes in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS Turbo                         | Click **Select SFS Turbo**. On the displayed page, select the SFS Turbo file system that meets your requirements and click **OK**.                                                                                        |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Subdirectory                      | Enter the absolute path of a subdirectory, for example, **/a/b**.                                                                                                                                                         |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Subdirectory Reclaim Policy       | Determine whether to retain subdirectories when a PVC is deleted.                                                                                                                                                         |
   |                                   |                                                                                                                                                                                                                           |
   |                                   | -  **Retain**: When a PVC is deleted, the PV is deleted, but **its associated subdirectories are retained**.                                                                                                              |
   |                                   | -  **Delete**: When a PVC is deleted, **the PV and its associated subdirectories are also deleted**.                                                                                                                      |
   |                                   |                                                                                                                                                                                                                           |
   |                                   |    .. note::                                                                                                                                                                                                              |
   |                                   |                                                                                                                                                                                                                           |
   |                                   |       When a subdirectory is deleted, only the absolute path of the subdirectory specified in the PVC is deleted. The parent directory is retained.                                                                       |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Subdirectory Capacity             | -  **Not limited**: The subdirectory capacity is not limited.                                                                                                                                                             |
   |                                   | -  **Limited**: The subdirectory capacity is limited.                                                                                                                                                                     |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Capacity                          | The maximum capacity of the subdirectory, in GiB. This parameter is available only when the subdirectory capacity limit is enabled.                                                                                       |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Create** to create a PVC and a PV.

   You can choose **Storage** in the navigation pane and view the created PVC and PV on the **PVCs** and **PVs** tab pages, respectively.

Dynamically Creating an SFS Turbo Subdirectory Using kubectl
------------------------------------------------------------

#. Use kubectl to access the cluster.

#. Create the **pvc-sfsturbo-subpath.yaml** file.

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: pvc-sfsturbo-subpath    # PVC name
        namespace: default
        annotations:
          everest.io/volume-as: absolute-path                # An SFS Turbo subdirectory is used.
          everest.io/sfsturbo-share-id: <sfsturbo_id>        # SFS Turbo ID
          everest.io/path: /a                                # Subdirectory that is automatically created, which must be an absolute path
          everest.io/reclaim-policy: retain-volume-only      # When a PVC is deleted, the PV is deleted, but its associated subdirectories are retained.
          everest.io/csi.enable-sfsturbo-dir-quota: "true"   # The status of quota limit
      spec:
        accessModes:
          - ReadWriteMany      # ReadWriteMany must be selected for SFS Turbo.
        resources:
          requests:

            storage: 10Gi      # For SFS Turbo subdirectory PVCs, this configuration specifies the capacity of a subdirectory when quota limit is enabled. In other scenarios, it is only used for verification and must not be empty or 0.
        storageClassName: csi-sfsturbo     # StorageClass name of the SFS Turbo file system

   .. table:: **Table 1** Key parameters

      +------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                | Mandatory             | Description                                                                                                                                                                                                                                                     |
      +==========================================+=======================+=================================================================================================================================================================================================================================================================+
      | everest.io/volume-as                     | Yes                   | The value is fixed at **absolute-path**, indicating that a dynamically created SFS Turbo subdirectory is used.                                                                                                                                                  |
      +------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/sfsturbo-share-id             | Yes                   | SFS Turbo ID                                                                                                                                                                                                                                                    |
      |                                          |                       |                                                                                                                                                                                                                                                                 |
      |                                          |                       | How to obtain: Log in to the CCE console, choose **Service List** > **Storage** > **Scalable File Service**, and select **SFS Turbo**. In the list, click the name of the target SFS Turbo file system. On the details page, copy the content following **ID**. |
      +------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/path                          | Yes                   | Subdirectory that is automatically created, which must be an absolute path.                                                                                                                                                                                     |
      +------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/reclaim-policy                | Yes                   | Whether to retain subdirectories when deleting a PVC. This parameter must be used with :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`. This parameter is available only when the PV reclaim policy is **Delete**. Options:                       |
      |                                          |                       |                                                                                                                                                                                                                                                                 |
      |                                          |                       | -  **retain-volume-only**: When a PVC is deleted, the PV is deleted, but **its associated subdirectories are retained**.                                                                                                                                        |
      |                                          |                       | -  **delete**: When a PVC is deleted, **the PV and its associated subdirectories are also deleted**.                                                                                                                                                            |
      |                                          |                       |                                                                                                                                                                                                                                                                 |
      |                                          |                       |    .. note::                                                                                                                                                                                                                                                    |
      |                                          |                       |                                                                                                                                                                                                                                                                 |
      |                                          |                       |       When a subdirectory is deleted, only the absolute path of the subdirectory specified in the PVC is deleted. The parent directory is retained.                                                                                                             |
      +------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/csi.enable-sfsturbo-dir-quota | No                    | Whether to enable quota limit for a subdirectory. If the value is set to **true**, the limit is enabled. If the value is empty or set to any other value, the limit is disabled.                                                                                |
      +------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storage                                  | Yes                   | Requested capacity in the PVC, in Gi.                                                                                                                                                                                                                           |
      |                                          |                       |                                                                                                                                                                                                                                                                 |
      |                                          |                       | -  When subdirectory capacity limit is enabled (**everest.io/csi.enable-sfsturbo-dir-quota** is set to **true**), **storage** indicates the capacity of a subdirectory, and its value must be an integer.                                                       |
      |                                          |                       |                                                                                                                                                                                                                                                                 |
      |                                          |                       |    If **storage** is set to a decimal, the value will be rounded up automatically. For example, if **storage** is set to **10.1Gi**, an 11-GiB subdirectory will be created.                                                                                    |
      |                                          |                       |                                                                                                                                                                                                                                                                 |
      |                                          |                       | -  If a subdirectory capacity is not limited, this parameter is meaningless and only used for verification. You can set this parameter to a fixed value **10Gi**.                                                                                               |
      +------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Run the following command to create a PVC:

   .. code-block::

      kubectl apply -f pvc-sfsturbo-subpath.yaml
