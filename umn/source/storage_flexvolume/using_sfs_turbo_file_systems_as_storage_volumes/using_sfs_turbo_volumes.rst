:original_name: cce_01_0331.html

.. _cce_01_0331:

Using SFS Turbo Volumes
=======================

Prerequisites
-------------

You have created a CCE cluster and installed the FlexVolume plug-in (:ref:`storage-driver <cce_01_0127>`) in the cluster.

Notes and Constraints
---------------------

-  SFS Turbo volumes are available only in certain regions.
-  Currently, SFS Turbo file systems cannot be directly created on CCE.
-  The following operations apply to clusters of Kubernetes 1.13 or earlier.

.. _cce_01_0331__section57261325121712:

Importing an SFS Turbo Volume
-----------------------------

CCE allows you to import existing SFS Turbo volumes.

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Storage**. On the **SFS Turbo** tab page, click **Import**.
#. Select one or more SFS Turbo volumes that you want to import.
#. Select the cluster and namespace to which you want to import the volumes.
#. Click **OK**. The volumes are displayed in the list. When **PVS Status** becomes **Bound**, the volumes are imported successfully.

Adding an SFS Turbo Volume
--------------------------

#. Create a workload or job by referring to :ref:`Creating a Deployment <cce_01_0047>`, :ref:`Creating a StatefulSet <cce_01_0048>`, :ref:`Creating a DaemonSet <cce_01_0216>`, or :ref:`Creating a Job <cce_01_0150>`. After you have added a container, choose **Data Storage** > **Cloud Volume**, and then click **Add Cloud Volume**.
#. Set the storage volume type to **SFS Turbo**.

   .. table:: **Table 1** Parameters for configuring an SFS Turbo volume

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                            |
      +===================================+========================================================================================================================================================================================================================================================================================================================================================================================================+
      | **Type**                          | **SFS Turbo**: applicable to DevOps, containerized microservices, and enterprise OA applications.                                                                                                                                                                                                                                                                                                      |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | **Allocation Mode**               |                                                                                                                                                                                                                                                                                                                                                                                                        |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Manual                            | Select an existing SFS Turbo volume. You need to import SFS Turbo volumes in advance. For details, see :ref:`Importing an SFS Turbo Volume <cce_01_0331__section57261325121712>`.                                                                                                                                                                                                                      |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Add Container Path                | Configure the following parameters:                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | a. **subPath**: Enter the subpath of the file storage, for example, **/tmp**.                                                                                                                                                                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    This parameter specifies a subpath inside the referenced volume instead of its root. If this parameter is not specified, the root path is used. Currently, only file storage is supported. The value must be a relative path and cannot start with a slash (/) or ../.                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | b. **Container Path**: Enter the mount path in the container, for example, **/tmp**.                                                                                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    The mount path must not be a system directory, such as **/** and **/var/run**. Otherwise, an exception occurs. You are advised to mount the volume to an empty directory. If the directory is not empty, ensure that there are no files affecting container startup in the directory. Otherwise, such files will be replaced, resulting in failures to start the container and create the workload. |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    .. important::                                                                                                                                                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |       NOTICE:                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   |       If the volume is mounted to a high-risk directory, you are advised to use an account with minimum permissions to start the container; otherwise, high-risk files on the host machine may be damaged.                                                                                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | c. Set permissions.                                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    -  **Read-only**: You can only read the data in the mounted volumes.                                                                                                                                                                                                                                                                                                                                |
      |                                   |    -  **Read/Write**: You can modify the data in the mounted volumes. Newly written data is not migrated if the container is migrated, which causes a data loss.                                                                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | Click **Add Container Path** to add multiple settings. Then, click **OK**.                                                                                                                                                                                                                                                                                                                             |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**.

Unbinding an SFS Turbo Volume
-----------------------------

When an SFS Turbo volume is successfully imported to a cluster, the volume is bound to the cluster. The volume can also be imported to other clusters. When the volume is unbound from the cluster, other clusters can still import and use the volume.

If the SFS Turbo volume has been mounted to a workload, the volume cannot be unbound from the cluster.

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Storage**. In the SFS Turbo volume list, click **Unbind** next to the target volume.
#. In the dialog box displayed, click **OK**.
