:original_name: cce_10_0462.html

.. _cce_10_0462:

Container Engine
================

Introduction to Container Engines
---------------------------------

Container engines, one of the most important components of Kubernetes, manage the lifecycle of images and containers. The kubelet interacts with a container runtime through the Container Runtime Interface (CRI).

CCE supports containerd and Docker. **containerd is recommended for its shorter traces, fewer components, higher stability, and less consumption of node resources**.

.. table:: **Table 1** Comparison between container engines

   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+
   | Item                       | containerd                                                        | Docker                                                                                           |
   +============================+===================================================================+==================================================================================================+
   | Tracing                    | kubelet --> CRI plugin (in the containerd process) --> containerd | -  Docker (Kubernetes 1.23 and earlier versions):                                                |
   |                            |                                                                   |                                                                                                  |
   |                            |                                                                   |    kubelet --> dockershim (in the kubelet process) --> docker --> containerd                     |
   |                            |                                                                   |                                                                                                  |
   |                            |                                                                   | -  Docker (community solution for Kubernetes 1.24 and later versions):                           |
   |                            |                                                                   |                                                                                                  |
   |                            |                                                                   |    kubelet --> cri-dockerd (kubelet uses CRI to connect to cri-dockerd) --> docker--> containerd |
   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+
   | Command                    | crictl                                                            | docker                                                                                           |
   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+
   | Kubernetes CRI             | Native support                                                    | Support through dockershim or cri-dockerd                                                        |
   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+
   | Pod startup delay          | Low                                                               | High                                                                                             |
   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+
   | kubelet CPU/memory usage   | Low                                                               | High                                                                                             |
   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+
   | Runtime's CPU/memory usage | Low                                                               | High                                                                                             |
   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+

.. _cce_10_0462__section159298451879:

Mapping between Node OSs and Container Engines
----------------------------------------------

.. table:: **Table 2** Node OSs and container engines in CCE clusters

   +--------------+----------------+-------------------------------------------------+--------------------------+-------------------+
   | OS           | Kernel Version | Container Engine                                | Container Storage Rootfs | Container Runtime |
   +==============+================+=================================================+==========================+===================+
   | EulerOS 2.5  | 3.x            | Docker                                          | Device Mapper            | runC              |
   +--------------+----------------+-------------------------------------------------+--------------------------+-------------------+
   | EulerOS 2.9  | 4.x            | Docker                                          | OverlayFS                | runC              |
   |              |                |                                                 |                          |                   |
   |              |                | Clusters of v1.23 and later support containerd. |                          |                   |
   +--------------+----------------+-------------------------------------------------+--------------------------+-------------------+
   | Ubuntu 22.04 | 4.x            | Docker                                          | OverlayFS                | runC              |
   |              |                |                                                 |                          |                   |
   |              |                | Clusters of v1.23 and later support containerd. |                          |                   |
   +--------------+----------------+-------------------------------------------------+--------------------------+-------------------+

.. table:: **Table 3** Node OSs and container engines in CCE Turbo clusters

   +-----------------------------------------+-------------+----------------+------------------+--------------------------+-------------------+
   | Node Type                               | OS          | Kernel Version | Container Engine | Container Storage Rootfs | Container Runtime |
   +=========================================+=============+================+==================+==========================+===================+
   | Elastic Cloud Server (VM)               | EulerOS 2.9 | 3.x            | Docker           | OverlayFS                | runC              |
   +-----------------------------------------+-------------+----------------+------------------+--------------------------+-------------------+
   | Elastic Cloud Server (physical machine) | EulerOS 2.9 | 4.x            | containerd       | Device Mapper            | Kata              |
   +-----------------------------------------+-------------+----------------+------------------+--------------------------+-------------------+

Common Commands of containerd and Docker
----------------------------------------

containerd does not support Docker APIs and Docker CLI, but you can run crictl commands to implement similar functions.

.. table:: **Table 4** Image-related commands

   +-----+---------------------------------------------------+---------------------------------------------------+-----------------------+
   | No. | Docker Command                                    | containerd Command                                | Remarks               |
   +=====+===================================================+===================================================+=======================+
   | 1   | docker images [Option] [Image name[:Tag]]         | crictl images [Option] [Image name[:Tag]]         | List local images.    |
   +-----+---------------------------------------------------+---------------------------------------------------+-----------------------+
   | 2   | docker pull [Option] *Image name*\ [:Tag|@DIGEST] | crictl pull [Option] *Image name*\ [:Tag|@DIGEST] | Pull images.          |
   +-----+---------------------------------------------------+---------------------------------------------------+-----------------------+
   | 3   | docker push                                       | None                                              | Pushing images.       |
   +-----+---------------------------------------------------+---------------------------------------------------+-----------------------+
   | 4   | docker rmi [Option] *Image*...                    | crictl rmi [Option] *Image ID*...                 | Delete a local image. |
   +-----+---------------------------------------------------+---------------------------------------------------+-----------------------+
   | 5   | docker inspect *Image ID*                         | crictl inspecti *Image ID*                        | Check images.         |
   +-----+---------------------------------------------------+---------------------------------------------------+-----------------------+

.. table:: **Table 5** Container-related commands

   +-----+------------------------------------------------------------------------+------------------------------------------------------------------------+--------------------------------------------+
   | No. | Docker Command                                                         | containerd Command                                                     | Remarks                                    |
   +=====+========================================================================+========================================================================+============================================+
   | 1   | docker ps [Option]                                                     | crictl ps [Option]                                                     | List containers.                           |
   +-----+------------------------------------------------------------------------+------------------------------------------------------------------------+--------------------------------------------+
   | 2   | docker create [Option]                                                 | crictl create [Option]                                                 | Create a container.                        |
   +-----+------------------------------------------------------------------------+------------------------------------------------------------------------+--------------------------------------------+
   | 3   | docker start [Option] *Container ID*...                                | crictl start [Option] *Container ID*...                                | Start a container.                         |
   +-----+------------------------------------------------------------------------+------------------------------------------------------------------------+--------------------------------------------+
   | 4   | docker stop [Option] *Container ID*...                                 | crictl stop [Option] *Container ID*...                                 | Stop a container.                          |
   +-----+------------------------------------------------------------------------+------------------------------------------------------------------------+--------------------------------------------+
   | 5   | docker rm [Option] *Container ID*...                                   | crictl rm [Option] *Container ID*...                                   | Delete a container.                        |
   +-----+------------------------------------------------------------------------+------------------------------------------------------------------------+--------------------------------------------+
   | 6   | docker attach [Option] *Container ID*                                  | crictl attach [Option] *Container ID*                                  | Connect to a container.                    |
   +-----+------------------------------------------------------------------------+------------------------------------------------------------------------+--------------------------------------------+
   | 7   | docker exec [Option] *Container ID* *Startup command* [*Parameter*...] | crictl exec [Option] *Container ID* *Startup command* [*Parameter*...] | Access the container.                      |
   +-----+------------------------------------------------------------------------+------------------------------------------------------------------------+--------------------------------------------+
   | 8   | docker inspect [Option] *Container name*\ \|\ *ID*...                  | crictl inspect [Option] *Container ID*...                              | Query container details.                   |
   +-----+------------------------------------------------------------------------+------------------------------------------------------------------------+--------------------------------------------+
   | 9   | docker logs [Option] *Container ID*                                    | crictl logs [Option] *Container ID*                                    | View container logs.                       |
   +-----+------------------------------------------------------------------------+------------------------------------------------------------------------+--------------------------------------------+
   | 10  | docker stats [Option] *Container ID*...                                | crictl stats [Option] *Container ID*                                   | Check the resource usage of the container. |
   +-----+------------------------------------------------------------------------+------------------------------------------------------------------------+--------------------------------------------+
   | 11  | docker update [Option] *Container ID*...                               | crictl update [Option] *Container ID*...                               | Update container resource limits.          |
   +-----+------------------------------------------------------------------------+------------------------------------------------------------------------+--------------------------------------------+

.. table:: **Table 6** Pod-related commands

   +-----+----------------+--------------------------------------+-------------------+
   | No. | Docker Command | containerd Command                   | Remarks           |
   +=====+================+======================================+===================+
   | 1   | None           | crictl pods [Option]                 | List pods.        |
   +-----+----------------+--------------------------------------+-------------------+
   | 2   | None           | crictl inspectp [Option] *Pod ID*... | View pod details. |
   +-----+----------------+--------------------------------------+-------------------+
   | 3   | None           | crictl start [Option] *Pod ID*...    | Start a pod.      |
   +-----+----------------+--------------------------------------+-------------------+
   | 4   | None           | crictl runp [Option] *Pod ID*...     | Run a pod.        |
   +-----+----------------+--------------------------------------+-------------------+
   | 5   | None           | crictl stopp [Option] *Pod ID*...    | Stop a pod.       |
   +-----+----------------+--------------------------------------+-------------------+
   | 6   | None           | crictl rmp [Option] *Pod ID*...      | Delete a pod.     |
   +-----+----------------+--------------------------------------+-------------------+

.. note::

   Containers created and started by containerd are immediately deleted by kubelet. containerd does not support suspending, resuming, restarting, renaming, and waiting for containers, nor Docker image build, import, export, comparison, push, search, and labeling. containerd does not support file copy. You can log in to the image repository by modifying the configuration file of containerd.

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

   -  EulerOS/CentOS: docker-engine 18.9.0, a Docker version customized for CCE. Security vulnerabilities will be fixed in a timely manner.

-  containerd: 1.6.14
