:original_name: cce_10_0341.html

.. _cce_10_0341:

Space Allocation of a Data Disk
===============================

This section describes how to allocate data disk space to nodes so that you can configure the data disk space accordingly.

Allocating Default Data Disk Space
----------------------------------

.. note::

   -  In clusters of a version earlier than v1.23.18-r0, v1.25.13-r0, v1.27.10-r0, v1.29.4-r0, or v1.28.8-r0, a default data disk will be added to the node for the container runtime and kubelet components. You can customize the space allocation of the default data disk.
   -  In clusters of v1.23.18-r0, v1.25.13-r0, v1.27.10-r0, v1.28.8-r0, v1.29.4-r0, or later, a default data disk will be added to the node for the container runtime and kubelet components only if **System Component Storage** is set to **Data Disk**. You can customize the space allocation of the default data disk.

When creating a node, you can customize Data Disk Space Allocation in the expanded area of **Data Disk**.

-  :ref:`Space Allocation for Container Engines <cce_10_0341__section10653143445411>`

   -  Specified disk space: CCE divides the data disk space for two parts by default. One part is used to store the Docker/containerd working directories, container image data, and image metadata. The other is reserved for kubelet and emptyDir volumes. The available container engine space affects image pulls and container startup and running.

      -  Container engine and container image space (90% by default): stores the container runtime working directories, container image data, and image metadata.
      -  kubelet and emptyDir space (10% by default): stores pod configuration files, secrets, and mounted storage such as emptyDir volumes.

      .. note::

         If the sum of the container engine and container image space and the kubelet and emptyDir space is less than 100%, the remaining space will be allocated for user data. You can mount the storage volume to a service path. Do not leave the path empty or set it to a key OS path such as the root directory.

-  :ref:`Space Allocation for Pods <cce_10_0341__section12119191161518>`: indicates the basesize of a pod. You can set an upper limit for the disk space occupied by each workload pod (including the space occupied by container images). This setting prevents the pods from taking all the disk space available, which may cause service exceptions. It is recommended that the value is less than or equal to 80% of the container engine space. This parameter is related to the node OS and container storage Rootfs and is not supported in some scenarios. For details, see :ref:`Mapping Between OS and Container Storage Rootfs <cce_10_0341__section1473612279214>`.
-  Write Mode

   -  **Linear**: A linear logical volume integrates one or more physical volumes. Data is written to the next physical volume when the previous one is used up.
   -  **Striped**: available only if there are at least two data disks. A striped logical volume stripes data into blocks of the same size and stores them in multiple physical volumes in sequence. This allows data to be concurrently read and written. A storage pool consisting of striped volumes cannot be scaled-out.

.. _cce_10_0341__section10653143445411:

Space Allocation for Container Engines
--------------------------------------

For a node using a non-shared data disk (100 GiB for example), the division of the disk space varies depending on the container storage Rootfs type **Device Mapper** or **OverlayFS**. For details about the container storage Rootfs corresponding to different OSs, see :ref:`Mapping Between OS and Container Storage Rootfs <cce_10_0341__section1473612279214>`.

-  **Rootfs (Device Mapper)**

   By default, the container engine and image space, occupying 90% of the data disk, can be divided into the following two parts:

   -  The **/var/lib/docker** directory is used as the Docker working directory and occupies 20% of the container engine and container image space by default. (Space size of the **/var/lib/docker** directory = **Data disk space x 90% x 20%**)

   -  The thin pool is used to store container image data, image metadata, and container data, and occupies 80% of the container engine and container image space by default. (Thin pool space = **Data disk space x 90% x 80%**)

      The thin pool is dynamically mounted. You can view it by running the **lsblk** command on a node, but not the **df -h** command.


   .. figure:: /_static/images/en-us_image_0000002218819650.png
      :alt: **Figure 1** Space allocation for container engines of Device Mapper

      **Figure 1** Space allocation for container engines of Device Mapper

-  **Rootfs (OverlayFS)**

   No separate thin pool. The entire container engine and container image space (90% of the data disk by default) are in the **/var/lib/docker** directory.


   .. figure:: /_static/images/en-us_image_0000002218659822.png
      :alt: **Figure 2** Space allocation for container engines of OverlayFS

      **Figure 2** Space allocation for container engines of OverlayFS

.. _cce_10_0341__section12119191161518:

Space Allocation for Pods
-------------------------

The customized pod container space (basesize) is related to the node OS and container storage Rootfs. For details about the container storage Rootfs, see :ref:`Mapping Between OS and Container Storage Rootfs <cce_10_0341__section1473612279214>`.

-  Device Mapper supports custom pod basesize. The default value is 10 GiB.
-  In OverlayFS mode, the pod container space is not limited by default.

When configuring **basesize**, consider the maximum number of pods allowed on one node. The container engine space should be greater than the total disk space used by containers. Formula: **Container engine space and container image space (90% by default)** > **Number of containers** x **basesize**. Otherwise, the container engine space allocated to the node may be insufficient and the container cannot be started.

For nodes that support **basesize**, when Device Mapper is used, although you can limit the size of the **/home** directory of a single container (to 10 GiB by default), all containers on the node still share the thin pool of the node for storage. They are not completely isolated. When the sum of the thin pool space used by certain containers reaches the upper limit, other containers cannot run properly.

In addition, after a file is deleted in the **/home** directory of the container, the thin pool space occupied by the file is not released immediately. Therefore, even if **basesize** is set to 10 GiB, the thin pool space occupied by files keeps increasing until 10 GiB when files are created in the container. The space released after file deletion will be reused but after a while. If **the number of containers on the node multiplied by basesize** is greater than the thin pool space size of the node, there is a possibility that the thin pool space has been used up.

.. _cce_10_0341__section1473612279214:

Mapping Between OS and Container Storage Rootfs
-----------------------------------------------

.. table:: **Table 1** Node OSs and container engines in CCE clusters

   +-----------------------+--------------------------+----------------------------------------------------------------------------------------------------------------+
   | OS                    | Container Storage Rootfs | Custom Basesize                                                                                                |
   +=======================+==========================+================================================================================================================+
   | EulerOS 2.9           | OverlayFS                | Supported by Docker clusters of v1.19.16-r0, v1.21.3-r0, v1.23.3-r0, or later. There are no limits by default. |
   |                       |                          |                                                                                                                |
   |                       |                          | Not supported by clusters of a version earlier than v1.19.16-r0, v1.21.3-r0, or v1.23.3-r0.                    |
   +-----------------------+--------------------------+----------------------------------------------------------------------------------------------------------------+
   | Ubuntu 22.04          | OverlayFS                | Not supported                                                                                                  |
   +-----------------------+--------------------------+----------------------------------------------------------------------------------------------------------------+
   | HCE OS 2.0            | OverlayFS                | Supported only by Docker clusters. There are no limits by default.                                             |
   +-----------------------+--------------------------+----------------------------------------------------------------------------------------------------------------+

.. table:: **Table 2** Node OSs and container engines in CCE Turbo clusters

   +--------------+--------------------------+------------------------------------------------------------------------------------------------------+
   | OS           | Container Storage Rootfs | Custom Basesize                                                                                      |
   +==============+==========================+======================================================================================================+
   | Ubuntu 22.04 | OverlayFS                | Not supported                                                                                        |
   +--------------+--------------------------+------------------------------------------------------------------------------------------------------+
   | EulerOS 2.9  | OverlayFS                | Supported when Rootfs is set to OverlayFS and the runtime is Docker. There are no limits by default. |
   +--------------+--------------------------+------------------------------------------------------------------------------------------------------+
   | HCE OS 2.0   | OverlayFS                | Supported only by Docker clusters. There are no limits by default.                                   |
   +--------------+--------------------------+------------------------------------------------------------------------------------------------------+

Garbage Collection Policies for Container Images
------------------------------------------------

When the container engine space is insufficient, image garbage collection is triggered.

The policy for garbage collecting images takes two factors into consideration: **HighThresholdPercent** and **LowThresholdPercent**. Disk usage exceeding the high threshold (default: 80%) will trigger garbage collection. The garbage collection will delete least recently used images until the low threshold (default: 70%) is met.

Recommended Configuration for the Container Engine Space
--------------------------------------------------------

-  The container engine space should be greater than the total disk space used by containers. Formula: **Container engine space** > **Number of containers** x **basesize**
-  You are advised to create and delete files of containerized services in local storage volumes (such as emptyDir and hostPath volumes) or cloud storage directories mounted to the containers. In this way, the thin pool space is not occupied. emptyDir volumes occupy the kubelet space. Therefore, properly plan the size of the kubelet space.
-  You can deploy services on nodes that use the OverlayFS (for details, see :ref:`Mapping Between OS and Container Storage Rootfs <cce_10_0341__section1473612279214>`) so that the disk space occupied by files created or deleted in containers can be released immediately.
