:original_name: cce_10_0462.html

.. _cce_10_0462:

Container Engine
================

Introduction to Container Engines
---------------------------------

Container engines, one of the most important components of Kubernetes, manage the lifecycle of images and containers. The kubelet interacts with a container runtime through the Container Runtime Interface (CRI).

.. _cce_10_0462__section159298451879:

Mapping between Node OSs and Container Engines
----------------------------------------------

.. table:: **Table 1** Node OSs and container engines in CCE clusters

   +--------------+----------------+-------------------------------------------------+-----------------------------------------------------+-------------------+
   | OS           | Kernel Version | Container Engine                                | Container Storage Rootfs                            | Container Runtime |
   +==============+================+=================================================+=====================================================+===================+
   | CentOS 7.x   | 3.x            | Docker                                          | Clusters of v1.19.16 and earlier use Device Mapper. | runC              |
   |              |                |                                                 |                                                     |                   |
   |              |                | Clusters of v1.23 and later support containerd. | Clusters of v1.19.16 and later use OverlayFS.       |                   |
   +--------------+----------------+-------------------------------------------------+-----------------------------------------------------+-------------------+
   | EulerOS 2.5  | 3.x            | Docker                                          | Device Mapper                                       | runC              |
   +--------------+----------------+-------------------------------------------------+-----------------------------------------------------+-------------------+
   | EulerOS 2.9  | 4.x            | Docker                                          | OverlayFS                                           | runC              |
   |              |                |                                                 |                                                     |                   |
   |              |                | Clusters of v1.23 and later support containerd. |                                                     |                   |
   +--------------+----------------+-------------------------------------------------+-----------------------------------------------------+-------------------+
   | Ubuntu 22.04 | 4.x            | Docker                                          | OverlayFS                                           | runC              |
   |              |                |                                                 |                                                     |                   |
   |              |                | containerd                                      |                                                     |                   |
   +--------------+----------------+-------------------------------------------------+-----------------------------------------------------+-------------------+

.. table:: **Table 2** Node OSs and container engines in CCE Turbo clusters

   +---------------------------+--------------+----------------+-------------------------------------------------+--------------------------+-------------------+
   | Node Type                 | OS           | Kernel Version | Container Engine                                | Container Storage Rootfs | Container Runtime |
   +===========================+==============+================+=================================================+==========================+===================+
   | Elastic Cloud Server (VM) | CentOS 7.x   | 3.x            | Docker                                          | OverlayFS                | runC              |
   +---------------------------+--------------+----------------+-------------------------------------------------+--------------------------+-------------------+
   |                           | EulerOS 2.5  | 3.x            | Docker                                          | OverlayFS                | runC              |
   +---------------------------+--------------+----------------+-------------------------------------------------+--------------------------+-------------------+
   |                           | EulerOS 2.9  | 4.x            | Docker                                          | OverlayFS                | runC              |
   |                           |              |                |                                                 |                          |                   |
   |                           |              |                | Clusters of v1.23 and later support containerd. |                          |                   |
   +---------------------------+--------------+----------------+-------------------------------------------------+--------------------------+-------------------+
   |                           | Ubuntu 22.04 | 4.x            | Docker                                          | OverlayFS                | runC              |
   |                           |              |                |                                                 |                          |                   |
   |                           |              |                | containerd                                      |                          |                   |
   +---------------------------+--------------+----------------+-------------------------------------------------+--------------------------+-------------------+

Differences in Tracing
----------------------

-  Docker (Kubernetes 1.23 and earlier versions):

   kubelet --> docker shim (in the kubelet process) --> docker --> containerd

-  Docker (community solution for Kubernetes v1.24 or later):

   kubelet --> cri-dockerd (kubelet uses CRI to connect to cri-dockerd) --> docker--> containerd

-  containerd:

   kubelet --> cri plugin (in the containerd process) --> containerd

Although Docker has added functions such as swarm cluster, docker build, and Docker APIs, it also introduces bugs. Compared with containerd, Docker has one more layer of calling. **Therefore, containerd is more resource-saving and secure.**

Container Engine Version Description
------------------------------------

-  Docker

   -  EulerOS/CentOS: docker 18.9.0, a Docker version customized for CCE. Security vulnerabilities will be fixed in a timely manner.
   -  Ubuntu 22.04: docker-ce 20.10.21 (community version).

   .. note::

      -  You are advised to use the containerd engine for Ubuntu nodes.
      -  The open source docker-ce of the Ubuntu 18.04 node may trigger bugs when concurrent exec operations are performed (for example, multiple exec probes are configured). You are advised to use HTTP/TCP probes.

-  containerd: 1.6.14
