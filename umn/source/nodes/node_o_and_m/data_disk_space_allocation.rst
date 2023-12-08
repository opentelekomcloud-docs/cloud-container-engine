:original_name: cce_10_0341.html

.. _cce_10_0341:

Data Disk Space Allocation
==========================

This section describes how to allocate data disk space to nodes so that you can configure the data disk space accordingly.

Allocating Data Disk Space
--------------------------

When creating a node, configure data disks for the node. You can also click **Expand** and customize the data disk space allocation for the node.

-  :ref:`Allocate Disk Space <cce_10_0341__section10653143445411>`:

   CCE divides the data disk space for two parts by default. One part is used to store the Docker/containerd working directories, container images, and image metadata. The other is reserved for kubelet and emptyDir volumes. The available container engine space affects image pulls and container startup and running.

   -  Container engine and container image space (90% by default): stores the container runtime working directories, container image data, and image metadata.
   -  kubelet and emptyDir space (10% by default): stores pod configuration files, secrets, and mounted storage such as emptyDir volumes.

-  :ref:`Allocate Pod Basesize <cce_10_0341__section12119191161518>`: indicates the basesize of a pod. You can set an upper limit for the disk space occupied by each workload pod (including the space occupied by container images). This setting prevents the pods from taking all the disk space available, which may cause service exceptions. It is recommended that the value is smaller than or equal to 80% of the container engine space. This parameter is related to the node OS and container storage rootfs and is not supported in some scenarios.

.. _cce_10_0341__section10653143445411:

Allocating Disk Space
---------------------

For a node using a non-shared data disk (100 GB for example), the division of the disk space varies depending on the container storage Rootfs type **Device Mapper** or **OverlayFS**. For details about the container storage Rootfs corresponding to different OSs, see :ref:`Mapping Between OS and Container Storage Rootfs <cce_10_0341__section1473612279214>`.

-  **Rootfs (Device Mapper)**

   By default, the container engine and image space, occupying 90% of the data disk, can be divided into the following two parts:

   -  The **/var/lib/docker** directory is used as the Docker working directory and occupies 20% of the container engine and container image space by default. (Space size of the **/var/lib/docker** directory = **Data disk space x 90% x 20%**)

   -  The thin pool is used to store container image data, image metadata, and container data, and occupies 80% of the container engine and container image space by default. (Thin pool space = **Data disk space x 90% x 80%**)

      The thin pool is dynamically mounted. You can view it by running the **lsblk** command on a node, but not the **df -h** command.


   .. figure:: /_static/images/en-us_image_0000001647576500.png
      :alt: **Figure 1** Space allocation for container engines of Device Mapper

      **Figure 1** Space allocation for container engines of Device Mapper

-  **Rootfs (OverlayFS)**

   No separate thin pool. The entire container engine and container image space (90% of the data disk by default) are in the **/var/lib/docker** directory.


   .. figure:: /_static/images/en-us_image_0000001647417268.png
      :alt: **Figure 2** Space allocation for container engines of OverlayFS

      **Figure 2** Space allocation for container engines of OverlayFS

.. _cce_10_0341__section12119191161518:

Allocating Basesize for Pods
----------------------------

The customized pod container space (basesize) is related to the node OS and container storage Rootfs. For details about the container storage Rootfs, see :ref:`Mapping Between OS and Container Storage Rootfs <cce_10_0341__section1473612279214>`.

-  Device Mapper supports custom pod basesize. The default value is 10 GB.
-  In OverlayFS mode, the pod container space is not limited by default.

   .. note::

      In the case of using Docker on EulerOS 2.9 nodes, **basesize** will not take effect if **CAP_SYS_RESOURCE** or **privileged** is configured for a container.

When configuring **basesize**, consider the maximum number of pods on a node. The container engine space should be greater than the total disk space used by containers. Formula: **the container engine space and container image space (90% by default)** > **Number of containers** x **basesize**. Otherwise, the container engine space allocated to the node may be insufficient and the container cannot be started.

For nodes that support **basesize**, when Device Mapper is used, although you can limit the size of the **/home** directory of a single container (to 10 GB by default), all containers on the node still share the thin pool of the node for storage. They are not completely isolated. When the sum of the thin pool space used by certain containers reaches the upper limit, other containers cannot run properly.

In addition, after a file is deleted in the **/home** directory of the container, the thin pool space occupied by the file is not released immediately. Therefore, even if **basesize** is set to 10 GB, the thin pool space occupied by files keeps increasing until 10 GB when files are created in the container. The space released after file deletion will be reused but after a while. If **the number of containers on the node multiplied by basesize** is greater than the thin pool space size of the node, there is a possibility that the thin pool space has been used up.

.. _cce_10_0341__section1473612279214:

Mapping Between OS and Container Storage Rootfs
-----------------------------------------------

.. table:: **Table 1** Node OSs and container engines in CCE clusters

   +-----------------------+--------------------------+------------------------------------------------------------------------------------------------------------------------+
   | OS                    | Container Storage Rootfs | Customized Basesize                                                                                                    |
   +=======================+==========================+========================================================================================================================+
   | EulerOS 2.5           | Device Mapper            | Supported only when the container engine is Docker. The default value is 10 GB.                                        |
   +-----------------------+--------------------------+------------------------------------------------------------------------------------------------------------------------+
   | EulerOS 2.9           | OverlayFS                | Supported only by clusters of v1.19.16, v1.21.3, v1.23.3, and later. The container basesize is not limited by default. |
   |                       |                          |                                                                                                                        |
   |                       |                          | Not supported when th cluster versions are earlier than v1.19.16, v1.21.3, and v1.23.3.                                |
   +-----------------------+--------------------------+------------------------------------------------------------------------------------------------------------------------+
   | Ubuntu 22.04          | OverlayFS                | Not supported.                                                                                                         |
   +-----------------------+--------------------------+------------------------------------------------------------------------------------------------------------------------+

.. table:: **Table 2** Node OSs and container engines in CCE Turbo clusters

   +-----------------------+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | OS                    | Container Storage Rootfs   | Customized Basesize                                                                                                                  |
   +=======================+============================+======================================================================================================================================+
   | Ubuntu 22.04          | OverlayFS                  | Not supported.                                                                                                                       |
   +-----------------------+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | EulerOS 2.9           | ECS VMs use OverlayFS.     | Supported only when Rootfs is set to OverlayFS and the container engine is Docker. The container basesize is not limited by default. |
   |                       |                            |                                                                                                                                      |
   |                       | ECS PMs use Device Mapper. | Supported when Rootfs is set to Device Mapper and the container engine is Docker. The default value is 10 GB.                        |
   +-----------------------+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+

Garbage Collection Policies for Container Images
------------------------------------------------

When the container engine space is insufficient, image garbage collection is triggered.

The policy for garbage collecting images takes two factors into consideration: **HighThresholdPercent** and **LowThresholdPercent**. Disk usage above the high threshold (default: 85%) will trigger garbage collection. The garbage collection will delete least recently used images until the low threshold (default: 80%) has been met.

Recommended Configuration for the Container Engine Space
--------------------------------------------------------

-  The container engine space should be greater than the total disk space used by containers. Formula: **Container engine space** > **Number of containers** x **basesize**
-  You are advised to create and delete files of containerized services in local storage volumes (such as emptyDir and hostPath volumes) or cloud storage directories mounted to the containers. In this way, the thin pool space is not occupied. emptyDir volumes occupy the kubelet space. Therefore, properly plan the size of the kubelet space.
-  You can deploy services on nodes that use the OverlayFS (for details, see :ref:`Mapping Between OS and Container Storage Rootfs <cce_10_0341__section1473612279214>`) so that the disk space occupied by files created or deleted in containers can be released immediately.
