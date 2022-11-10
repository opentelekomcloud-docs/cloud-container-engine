:original_name: cce_01_0324.html

.. _cce_01_0324:

Using OBS Volumes
=================

Prerequisites
-------------

You have created a CCE cluster and installed the FlexVolume plug-in (:ref:`storage-driver <cce_01_0127>`) in the cluster.

Notes and Constraints
---------------------

-  CCE clusters of v1.7.3-r8 and earlier do not support OBS volumes. You need to upgrade these clusters or create clusters of a later version that supports OBS.
-  Volumes cannot be created in specified enterprise projects. Only the default enterprise project is supported.
-  The following operations apply to clusters of Kubernetes 1.13 or earlier.

.. _cce_01_0324__section14271608324:

Preparations
------------

To mount reliable and stable OBS buckets as volumes, you must create AK/SK before you create OBS buckets.

The procedure for configuring the AK/SK is as follows:

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Storage**.

#. On the **OBS** tab page, click **AK/SK** in the notice.


   .. figure:: /_static/images/en-us_image_0000001190538605.png
      :alt: **Figure 1** Configuring the AK/SK

      **Figure 1** Configuring the AK/SK

#. Click |image1|, select a key file, and click **Upload** to upload the key file.

#. Select the corresponding workload and click **Restart**.

.. important::

   When creating an OBS volume, you must use the AK/SK. If the key file is not uploaded, the pod will fail to be started or OBS data access will be abnormal due to the volume mounting failure.

.. _cce_01_0324__section172788131291:

Creating an OBS Volume
----------------------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Storage**.

#. Click the **OBS** tab and click **Create OBS Bucket**.

#. Configure basic information, as shown in :ref:`Table 1 <cce_01_0324__table20328123218464>`.

   .. _cce_01_0324__table20328123218464:

   .. table:: **Table 1** Parameters for creating an OBS volume

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================================================================+
      | \* PVC Name                       | Name of the new PVC, which is different from the volume name. The actual volume name is automatically generated when the PV is created by the PVC.                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                                                |
      |                                   | The name contains 3 to 55 characters (excluding the prefix). It must contain lowercase letters, digits, and hyphens (-), and cannot start or end with a hyphen (-).                                                                                                            |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Cluster Name                      | Cluster to which the OBS volume belongs.                                                                                                                                                                                                                                       |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Namespace                         | Namespace to which the volume belongs. The default value is **default**.                                                                                                                                                                                                       |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Instance Type                     | Type of the storage instance created on OBS.                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                |
      |                                   | -  **Parallel file system**: supported when the cluster version is 1.15 or later and the everest add-on version is 1.0.2 or later.                                                                                                                                             |
      |                                   | -  **Object bucket**: A bucket is a container for storing objects in OBS. OBS provides flat storage in the form of buckets and objects. Unlike the conventional multi-layer directory structure of file systems, all objects in a bucket are stored at the same logical layer. |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Storage Class                     | This parameter is displayed when you select **Object bucket** for **Instance Type**.                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                |
      |                                   | This parameter indicates the storage classes supported by OBS.                                                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                                                                                                                |
      |                                   | -  **Standard**\ : applicable to scenarios where a large number of hotspot files or small-sized files need to be accessed frequently (multiple times per month on average) and require fast access response.                                                                   |
      |                                   | -  **Infrequent access**: applicable to scenarios where data is not frequently accessed (less than 12 times per year on average) but requires fast access response.                                                                                                            |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Storage Policy                    | Object storage has the following policies:                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                |
      |                                   | **Private**: Only the bucket owner has full control over the bucket. Unauthorized users do not have permissions to access the bucket.                                                                                                                                          |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Mode                       | Access permissions of user applications on storage resources (PVs).                                                                                                                                                                                                            |
      |                                   |                                                                                                                                                                                                                                                                                |
      |                                   | -  **ReadWriteMany** (RWX): The volume is mounted as read-write by multiple nodes.                                                                                                                                                                                             |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Create**.

   After the OBS volume is successfully created, it is displayed in the OBS volume list. Click the PVC name to view detailed information about the OBS volume.

Adding an OBS Volume
--------------------

#. Create a workload or job by referring to :ref:`Creating a Deployment <cce_01_0047>`, :ref:`Creating a StatefulSet <cce_01_0048>`, :ref:`Creating a DaemonSet <cce_01_0216>`, or :ref:`Creating a Job <cce_01_0150>`. After you have added a container, choose **Data Storage** > **Cloud Volume**, and then click **Add Cloud Volume**.
#. Set **Type** to **OBS**.

   .. table:: **Table 2** OBS volume parameters

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                            |
      +===================================+========================================================================================================================================================================================================================================================================================================================================================================================================+
      | **Type**                          | Select **OBS**.                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | **OBS**: Standard and Infrequent Access OBS buckets are supported. OBS buckets are commonly used for big data analytics, cloud native applications, static website hosting, and backup/active archiving.                                                                                                                                                                                               |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | **Allocation Mode**               |                                                                                                                                                                                                                                                                                                                                                                                                        |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Manual                            | **Name**: Select a created OBS volume.                                                                                                                                                                                                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | **Sub-Type**: class of the selected volume. The value can be **Standard** or **Infrequent access**, and you do not need to set this parameter.                                                                                                                                                                                                                                                         |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Automatic                         | Type of the storage instance created on OBS.                                                                                                                                                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | -  **Parallel file system**: supported when the cluster version is 1.15 or later and the everest add-on version is 1.0.2 or later.                                                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | -  **Object bucket**: A bucket is a container for storing objects in OBS.                                                                                                                                                                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    **Sub-Type**: Select **Standard** or **Infrequent access**.                                                                                                                                                                                                                                                                                                                                         |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Add Container Path                | Configure the following parameters:                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | a. **Container Path**: Enter the mount path in the container, for example, **/tmp**.                                                                                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    The mount path must not be a system directory, such as **/** and **/var/run**. Otherwise, an exception occurs. You are advised to mount the volume to an empty directory. If the directory is not empty, ensure that there are no files affecting container startup in the directory. Otherwise, such files will be replaced, resulting in failures to start the container and create the workload. |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    .. important::                                                                                                                                                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |       NOTICE:                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   |       If the volume is mounted to a high-risk directory, you are advised to use an account with minimum permissions to start the container; otherwise, high-risk files on the host machine may be damaged.                                                                                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | b. Set permissions.                                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    -  **Read-only**: You can only read the data in the mounted volumes.                                                                                                                                                                                                                                                                                                                                |
      |                                   |    -  **Read/Write**: You can modify the data in the mounted volumes. Newly written data is not migrated if the container is migrated, which causes a data loss.                                                                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | Click **Add Container Path** to add multiple settings. Then, click **OK**.                                                                                                                                                                                                                                                                                                                             |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**.

Importing an OBS Volume
-----------------------

CCE allows you to import existing OBS volumes.

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Storage**. On the **OBS** tab page, click **Import**.
#. Select one or more OBS volumes that you want to import.
#. Select the target cluster and namespace.
#. Click **OK**.

Unbinding an OBS Volume
-----------------------

When an OBS volume is successfully created, the OBS volume is automatically bound to the current cluster. Other clusters can also use the OBS volume. When the volume is unbound from the cluster, other clusters can still use the volume.

If the volume has been mounted to a workload, the volume cannot be unbound from the cluster.

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Storage**. In the OBS volume list, click **Unbind** next to the target OBS volume.
#. In the dialog box displayed, click **Yes**.

Related Operations
------------------

After an OBS volume is created, you can perform the operation described in :ref:`Table 3 <cce_01_0324__table1619535674020>`.

.. _cce_01_0324__table1619535674020:

.. table:: **Table 3** Other operations

   +-----------------------------------+------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                              |
   +===================================+==========================================================================================+
   | Deleting an OBS volume            | #. Select the OBS volume to be deleted and click **Delete** in the **Operation** column. |
   |                                   | #. Follow the prompts to delete the volume.                                              |
   +-----------------------------------+------------------------------------------------------------------------------------------+

.. |image1| image:: /_static/images/en-us_image_0000001088110417.png
