:original_name: cce_10_0839.html

.. _cce_10_0839:

(Recommended) Creating an SFS Turbo Subdirectory Using a Dynamic PV
===================================================================

When an SFS Turbo volume is mounted to a workload container, the root directory is mounted to the container by default. However, the minimum capacity of an SFS Turbo volume is 500 GiB, which exceeds the capacity required by most workloads, leading to a waste of storage resources. To properly use the storage capacity, CCE allows you to dynamically create an SFS Turbo subdirectory when creating a PVC so that different workloads can share one SFS Turbo volume.

Prerequisites
-------------

-  You have created a cluster and installed the :ref:`CCE Container Storage (Everest) <cce_10_0066>` add-on of v2.3.23 or later in the cluster.
-  To create a cluster using commands, ensure kubectl is used. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.
-  You have created an available SFS Turbo file system, and the SFS Turbo file system and the cluster are in the same VPC.

Dynamically Creating an SFS Turbo Subdirectory on the Console
-------------------------------------------------------------

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
   | Storage Classes                   | Choose **csi-sfsturbo**.                                                                                                                                                                                                  |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Access Mode                       | SFS Turbo volumes support only **ReadWriteMany**, indicating that a storage volume can be mounted to multiple nodes in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS Turbo                         | Click **Select SFS Turbo**. On the displayed page, select the SFS Turbo file system that meets your requirements and click **OK**.                                                                                        |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Subdirectory                      | Enter the absolute path of a subdirectory, for example, **/a/b**.                                                                                                                                                         |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Subdirectory Reclaim Policy       | Whether to retain subdirectories when a PVC is deleted.                                                                                                                                                                   |
   |                                   |                                                                                                                                                                                                                           |
   |                                   | -  **Retain**: If a PVC is deleted, the PV will be deleted, but **the subdirectories associated with the PV will be retained**.                                                                                           |
   |                                   | -  **Delete**: After a PVC is deleted, **the PV and its associated subdirectories will also be deleted**.                                                                                                                 |
   |                                   |                                                                                                                                                                                                                           |
   |                                   |    .. note::                                                                                                                                                                                                              |
   |                                   |                                                                                                                                                                                                                           |
   |                                   |       When a subdirectory is deleted, only the absolute path of the subdirectory configured in the PVC will be deleted. The upper-layer directory will not be deleted.                                                    |
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
          everest.io/reclaim-policy: retain-volume-only      # When a PVC is deleted, the PV will be deleted, but the subdirectories associated with the PV will be retained.
      spec:
        accessModes:
          - ReadWriteMany      # ReadWriteMany must be selected for SFS Turbo.
        resources:
          requests:
            storage: 10Gi      # This parameter is only used for verification for the PVCs of the SFS Turbo subdirectory type. The value cannot be empty or 0.
        storageClassName: csi-sfsturbo     # Storage class name of the SFS Turbo file system

   .. table:: **Table 1** Key parameters

      +------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                    | Mandatory             | Description                                                                                                                                                                                                                                                     |
      +==============================+=======================+=================================================================================================================================================================================================================================================================+
      | everest.io/volume-as         | No                    | The value is fixed at **absolute-path**, indicating that a dynamically created SFS Turbo subdirectory is used.                                                                                                                                                  |
      +------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/sfsturbo-share-id | No                    | SFS Turbo ID                                                                                                                                                                                                                                                    |
      |                              |                       |                                                                                                                                                                                                                                                                 |
      |                              |                       | How to obtain: Log in to the CCE console, choose **Service List** > **Storage** > **Scalable File Service**, and select **SFS Turbo**. In the list, click the name of the target SFS Turbo file system. On the details page, copy the content following **ID**. |
      +------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/path              | No                    | Subdirectory that is automatically created, which must be an absolute path.                                                                                                                                                                                     |
      +------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/reclaim-policy    | No                    | Whether to retain subdirectories when deleting a PVC. This parameter must be used with :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`. This parameter is available only when the PV reclaim policy is **Delete**. Options:                       |
      |                              |                       |                                                                                                                                                                                                                                                                 |
      |                              |                       | -  **retain-volume-only**: If a PVC is deleted, the PV will be deleted, but **the subdirectories associated with the PV will be retained**.                                                                                                                     |
      |                              |                       | -  **delete**: After a PVC is deleted, **the PV and its associated subdirectories will also be deleted**.                                                                                                                                                       |
      |                              |                       |                                                                                                                                                                                                                                                                 |
      |                              |                       |    .. note::                                                                                                                                                                                                                                                    |
      |                              |                       |                                                                                                                                                                                                                                                                 |
      |                              |                       |       When a subdirectory is deleted, only the absolute path of the subdirectory configured in the PVC will be deleted. The upper-layer directory will not be deleted.                                                                                          |
      +------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storage                      | Yes                   | Requested capacity in the PVC, in Gi.                                                                                                                                                                                                                           |
      |                              |                       |                                                                                                                                                                                                                                                                 |
      |                              |                       | This parameter is only used for verification for the PVCs of the SFS Turbo subdirectory type. The value cannot be empty or **0**, and can be fixed at **10** because any value you set does not take effect.                                                    |
      +------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Run the following command to create a PVC:

   .. code-block::

      kubectl apply -f pvc-sfsturbo-subpath.yaml
