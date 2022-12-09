:original_name: cce_01_0341.html

.. _cce_01_0341:

Data Disk Space Allocation
==========================

When creating a node, you need to configure data disks for the node.

The data disk is divided into Kubernetes space and user space. The user space defines the space that is not allocated to Kubernetes in the local disk. The Kubernetes space consists of the following two parts:

-  Docker space (90% by default): stores Docker working directories, Docker image data, and image metadata.
-  kubelet space (10% by default): stores pod configuration files, secrets, and mounted storage such as emptyDir volumes.

The Docker space size affects image download and container startup and running. This section describes how the Docker space is used so that you can configure the Docker space accordingly.

Docker Space Description
------------------------

By default, a data disk, 100 GB for example, is divided as follows (depending on the container storage Rootfs):

-  Rootfs (Device Mapper)

   -  The **/var/lib/docker** directory is used as the Docker working directory and occupies 20% of the Docker space by default. (Space size of the **/var/lib/docker** directory = Data disk space x 90% x 20%)

   -  The thin pool is used to store Docker image data, image metadata, and container data, and occupies 80% of the Docker space by default. (Thin pool space = Data disk space x 90% x 80%)

      The thin pool is dynamically mounted. You can view it by running the **lsblk** command on a node, but not the **df -h** command.

   |image1|

-  Rootfs (OverlayFS): No separate thinpool. The entire Docker space is in the **/var/lib/docker** directory.

   |image2|

Using rootfs for container storage in CCE

-  CCE cluster: EulerOS 2.9 nodes use OverlayFS, and EulerOS 2.5 nodes use Device Mapper. CentOS 7.6 nodes in clusters earlier than v1.21 use Device Mapper, and use OverlayFS in clusters of v.1.21 and later.

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

Docker Space and Containers
---------------------------

The number of pods and the space configured for each container determine whether the Docker space of a node is sufficient.

The Docker space should be greater than the total disk space used by containers. Formula: **Docker space** > **Number of containers** x **Available data space for a single container (basesize)**

**When device mapper is used**, although you can limit the size of the **/home** directory of a single container (to 10 GB by default), all containers on the node still share the thin pool of the node for storage. They are not completely isolated. When the sum of the thin pool space used by certain containers reaches the upper limit, other containers cannot run properly.

In addition, after a file is deleted in the **/home** directory of the container, the thin pool space occupied by the file is not released immediately. Therefore, even if **basesize** is set to 10 GB, the thin pool space occupied by files keeps increasing until 10 GB when files are created in the container. The space released after file deletion will be reused but after a while. If **the number of containers on the node multiplied by basesize** is greater than the thin pool space size of the node, there is a possibility that the thin pool space has been used up.

Garbage Collection Policies for Container Images
------------------------------------------------

When the Docker space is insufficient, image garbage collection is triggered.

The policy for garbage collecting images takes two factors into consideration: **HighThresholdPercent** and **LowThresholdPercent**. Disk usage above the high threshold (default: 85%) will trigger garbage collection. The garbage collection will delete least recently used images until the low threshold (default: 80%) has been met.

Docker Space Configuration Suggestions
--------------------------------------

-  The Docker space should be greater than the total disk space used by containers. Formula: **Docker space** > **Number of containers** x **Available data space for a single container (basesize)**
-  You are advised to create and delete files of containerized services in local storage volumes (such as emptyDir and hostPath volumes) or cloud storage directories mounted to the containers. In this way, the thin pool space is not occupied. emptyDir volumes occupy the kubelet space. Therefore, properly plan the size of the kubelet space.
-  Docker uses the OverlayFS storage mode. This mode is used in Ubuntu 18.04 nodes in CCE clusters by default. You can deploy services on these nodes to prevent that the disk space occupied by files created or deleted in containers is not released immediately.

.. |image1| image:: /_static/images/en-us_image_0000001180446397.png
.. |image2| image:: /_static/images/en-us_image_0000001134406294.png
