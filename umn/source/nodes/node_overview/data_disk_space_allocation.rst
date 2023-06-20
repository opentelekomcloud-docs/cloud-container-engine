:original_name: cce_10_0341.html

.. _cce_10_0341:

Data Disk Space Allocation
==========================

This section describes how to allocate data disk space.

When creating a node, you need to configure a data disk whose capacity is greater than or equal to 100GB for the node. You can click **Expand** to customize the data disk space allocation.

-  :ref:`Allocate Disk Space <cce_10_0341__section10653143445411>`: CCE divides the data disk space for container engines and pods. The container engine space stores the Docker/containerd working directories, container images, and image metadata. The pod space stores kubelet components and emptyDir volumes. The available container engine space affects image download and container startup and running.

   -  Container engine and container image space (90% by default): functions as the container runtime working directory and stores container image data and image metadata.
   -  kubelet component and emptyDir volume space (10% by default): stores pod configuration files, secrets, and mounted storage such as emptyDir volumes.

-  :ref:`Allocate Pod Basesize <cce_10_0341__section12119191161518>`: indicates the base size of a container, that is, the upper limit of the disk space occupied by each workload pod (including the space occupied by container images). This setting prevents the pods from taking all the disk space available, which may cause service exceptions. It is recommended that the value be smaller than or equal to 80% of the container engine space. This parameter is related to the node OS and container storage rootfs and is not supported in some scenarios.

.. _cce_10_0341__section10653143445411:

Setting Container Engine Space
------------------------------

A data disk, 100 GB for example, is divided as follows (depending on the container storage rootfs):

You can log in to the node and run the **docker info** command to view the storage engine type.

.. code-block::

   # docker info
   Containers: 20
    Running: 17
    Paused: 0
    Stopped: 3
   Images: 16
   Server Version: 18.09.0
   Storage Driver: devicemapper

-  **Rootfs (Device Mapper)**

   By default, 90% of the data disk is the container engine and container image space, which can be divided into the following two parts:

   -  The **/var/lib/docker** directory is the Docker working directory and occupies 20% of the container runtime space by default. (Space size of the **/var/lib/docker** directory = **Data disk space x 90% x 20%**)

   -  The thin pool stores container image data, image metadata, and container data, and occupies 80% of the container runtime space by default. (Thin pool space = **Data disk space x 90% x 80%**)

      The thin pool is dynamically mounted. You can view it by running the **lsblk** command on a node, but not the **df -h** command.

   |image1|

-  **Rootfs (OverlayFS)**

   No separate thin pool. The entire container engine and container image space (90% of the data disk by default) are in the **/var/lib/docker** directory.

   |image2|

Using rootfs for container storage in CCE

-  CCE cluster: EulerOS 2.5 nodes use Device Mapper and EulerOS 2.9 nodes use OverlayFS. CentOS 7.x nodes in clusters earlier than v1.19.16 use Device Mapper, and use OverlayFS in clusters of v1.19.16 and later.
-  CCE Turbo cluster: BMSs use Device Mapper. ECSs use OverlayFS.

.. _cce_10_0341__section12119191161518:

Allocating Basesize for Pods
----------------------------

The capability of customizing pod basesize is related to the node OS and container storage rootfs. You can log in to the node and run the **docker info** command to view the container storage rootfs.

-  Device Mapper supports custom pod basesize. The default value is 10 GB.
-  When OverlayFS is used, **basesize** is not limited by default. In clusters of latest versions (1.19.16, 1.21.3, 1.23.3, and later), EulerOS 2.9 supports **basesize** if the Docker engine is used. Other OSs do not support **basesize**.

   .. note::

      In the case of using Docker on EulerOS 2.9 nodes, **basesize** will not take effect if **CAP_SYS_RESOURCE** or **privileged** is configured for a container.

When configuring **basesize**, consider the maximum number of pods on a node. The container engine space should be greater than the total disk space used by containers. Formula: **the container engine space and container image space (90% by default)** > **Number of containers** x **basesize**. Otherwise, the container engine space allocated to the node may be insufficient and the container cannot be started.

For nodes that support **basesize**, when Device Mapper is used, although you can limit the size of the **/home** directory of a single container (to 10 GB by default), all containers on the node still share the thin pool of the node for storage. They are not completely isolated. When the sum of the thin pool space used by certain containers reaches the upper limit, other containers cannot run properly.

In addition, after a file is deleted in the **/home** directory of the container, the thin pool space occupied by the file is not released immediately. Therefore, even if **basesize** is set to 10 GB, the thin pool space occupied by files keeps increasing until 10 GB when files are created in the container. The space released after file deletion will be reused but after a while. If **the number of containers on the node multiplied by basesize** is greater than the thin pool space size of the node, there is a possibility that the thin pool space has been used up.

Garbage Collection Policies for Container Images
------------------------------------------------

When the container engine space is insufficient, image garbage collection is triggered.

The policy for garbage collecting images takes two factors into consideration: **HighThresholdPercent** and **LowThresholdPercent**. Disk usage above the high threshold (default: 85%) will trigger garbage collection. The garbage collection will delete least recently used images until the low threshold (default: 80%) has been met.

Recommended Configuration for the Container Engine Space
--------------------------------------------------------

-  The container engine space should be greater than the total disk space used by containers. Formula: **Container engine space** > **Number of containers** x **basesize**
-  You are advised to create and delete files of containerized services in local storage volumes (such as emptyDir and hostPath volumes) or cloud storage directories mounted to the containers. In this way, the thin pool space is not occupied. emptyDir volumes occupy the kubelet space. Therefore, properly plan the size of the kubelet space.
-  If OverlayFS is used by in CCE clusters, you can deploy services on these nodes so that the disk space occupied by files created or deleted in containers can be released immediately.

.. |image1| image:: /_static/images/en-us_image_0000001517902940.png
.. |image2| image:: /_static/images/en-us_image_0000001517743364.png
