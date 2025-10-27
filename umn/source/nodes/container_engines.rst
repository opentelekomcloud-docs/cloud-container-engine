:original_name: cce_10_0462.html

.. _cce_10_0462:

Container Engines
=================

Introduction to Container Engines
---------------------------------

Container engines, one of the most important components of Kubernetes, manage the lifecycle of images and containers. The kubelet interacts with a container runtime through the Container Runtime Interface (CRI).

CCE supports containerd and Docker. **containerd is recommended for its shorter traces, fewer components, higher stability, and less consumption of node resources**.

.. table:: **Table 1** Comparison between container engines

   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+
   | Item                       | containerd                                                        | Docker                                                                                           |
   +============================+===================================================================+==================================================================================================+
   | Tracing                    | kubelet --> CRI plugin (in the containerd process) --> containerd | -  Docker (Kubernetes v1.23 and earlier versions):                                               |
   |                            |                                                                   |                                                                                                  |
   |                            |                                                                   |    kubelet --> dockershim (in the kubelet process) --> docker --> containerd                     |
   |                            |                                                                   |                                                                                                  |
   |                            |                                                                   | -  Docker (community solution for Kubernetes v1.24 or later):                                    |
   |                            |                                                                   |                                                                                                  |
   |                            |                                                                   |    kubelet --> cri-dockerd (kubelet uses CRI to connect to cri-dockerd) --> docker--> containerd |
   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+
   | Command                    | crictl/ctr                                                        | docker                                                                                           |
   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+
   | Kubernetes CRI             | Native support                                                    | Support through dockershim or cri-dockerd                                                        |
   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+
   | Pod delayed startup        | Minor                                                             | High                                                                                             |
   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+
   | kubelet CPU/memory usage   | Minor                                                             | High                                                                                             |
   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+
   | Runtime's CPU/memory usage | Minor                                                             | High                                                                                             |
   +----------------------------+-------------------------------------------------------------------+--------------------------------------------------------------------------------------------------+

.. _cce_10_0462__section159298451879:

Mapping Between Node OSs and Container Engines
----------------------------------------------

.. note::

   VPC network clusters of v1.23 or later versions support containerd. Tunnel network clusters of v1.23.2-r0 or later versions support containerd.

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
   | Ubuntu 22.04 | 5.x            | Docker                                          | OverlayFS                | runC              |
   |              |                |                                                 |                          |                   |
   |              |                | Clusters of v1.23 and later support containerd. |                          |                   |
   +--------------+----------------+-------------------------------------------------+--------------------------+-------------------+
   | HCE OS 2.0   | 5.x            | Docker                                          | OverlayFS                | runC              |
   |              |                |                                                 |                          |                   |
   |              |                | containerd                                      |                          |                   |
   +--------------+----------------+-------------------------------------------------+--------------------------+-------------------+

.. table:: **Table 3** Node OSs and container engines in CCE Turbo clusters

   +-----------+--------------+----------------+------------------+--------------------------+-------------------+
   | Node Type | OS           | Kernel Version | Container Engine | Container Storage Rootfs | Container Runtime |
   +===========+==============+================+==================+==========================+===================+
   | ECS (VM)  | EulerOS 2.9  | 4.x            | Docker           | OverlayFS                | runC              |
   |           |              |                |                  |                          |                   |
   |           |              |                | containerd       |                          |                   |
   +-----------+--------------+----------------+------------------+--------------------------+-------------------+
   | ECS (VM)  | Ubuntu 22.04 | 5.x            | Docker           | OverlayFS                | runC              |
   |           |              |                |                  |                          |                   |
   |           |              |                | containerd       |                          |                   |
   +-----------+--------------+----------------+------------------+--------------------------+-------------------+
   | ECS (VM)  | HCE OS 2.0   | 5.x            | Docker           | OverlayFS                | runC              |
   |           |              |                |                  |                          |                   |
   |           |              |                | containerd       |                          |                   |
   +-----------+--------------+----------------+------------------+--------------------------+-------------------+

Common Commands of containerd and Docker
----------------------------------------

containerd does not support Docker APIs and Docker CLI, but you can run crictl commands to implement similar functions.

.. table:: **Table 4** Image-related commands

   +-----------------------+----------------+--------------------+----------------------+
   | Operation             | Docker Command | containerd Command |                      |
   +=======================+================+====================+======================+
   |                       | docker         | crictl             | ctr                  |
   +-----------------------+----------------+--------------------+----------------------+
   | List local images.    | docker images  | crictl images      | ctr -n k8s.io i ls   |
   +-----------------------+----------------+--------------------+----------------------+
   | Pull images.          | docker pull    | crictl pull        | ctr -n k8s.io i pull |
   +-----------------------+----------------+--------------------+----------------------+
   | Push images.          | docker push    | None               | ctr -n k8s.io i push |
   +-----------------------+----------------+--------------------+----------------------+
   | Delete a local image. | docker rmi     | crictl rmi         | ctr -n k8s.io i rm   |
   +-----------------------+----------------+--------------------+----------------------+
   | Check images.         | docker inspect | crictl inspect     | None                 |
   +-----------------------+----------------+--------------------+----------------------+

.. table:: **Table 5** Container-related commands

   +--------------------------------------------+----------------+--------------------+------------------------+
   | Operation                                  | Docker Command | containerd Command |                        |
   +============================================+================+====================+========================+
   |                                            | docker         | crictl             | ctr                    |
   +--------------------------------------------+----------------+--------------------+------------------------+
   | List containers.                           | docker ps      | crictl ps          | ctr -n k8s.io c ls     |
   +--------------------------------------------+----------------+--------------------+------------------------+
   | Create a container.                        | docker create  | crictl create      | ctr -n k8s.io c create |
   +--------------------------------------------+----------------+--------------------+------------------------+
   | Start a container.                         | docker start   | crictl start       | ctr -n k8s.io run      |
   +--------------------------------------------+----------------+--------------------+------------------------+
   | Stop a container.                          | docker stop    | crictl stop        | None                   |
   +--------------------------------------------+----------------+--------------------+------------------------+
   | Delete a container.                        | docker rm      | crictl rm          | ctr -n k8s.io c del    |
   +--------------------------------------------+----------------+--------------------+------------------------+
   | Connect to a container.                    | docker attach  | crictl attach      | None                   |
   +--------------------------------------------+----------------+--------------------+------------------------+
   | Access the container.                      | docker exec    | crictl exec        | None                   |
   +--------------------------------------------+----------------+--------------------+------------------------+
   | Query container details.                   | docker inspect | crictl inspect     | ctr -n k8s.io c info   |
   +--------------------------------------------+----------------+--------------------+------------------------+
   | View container logs.                       | docker logs    | crictl logs        | None                   |
   +--------------------------------------------+----------------+--------------------+------------------------+
   | Check the resource usage of the container. | docker stats   | crictl stats       | None                   |
   +--------------------------------------------+----------------+--------------------+------------------------+
   | Update container resource limits.          | docker update  | crictl update      | None                   |
   +--------------------------------------------+----------------+--------------------+------------------------+

.. table:: **Table 6** Pod-related commands

   ================= ============== ================== ====
   Operation         Docker Command containerd Command
   ================= ============== ================== ====
   \                 docker         crictl             ctr
   List pods.        None           crictl pods        None
   View pod details. None           crictl inspectp    None
   Start a pod.      None           crictl start       None
   Run a pod.        None           crictl runp        None
   Stop a pod.       None           crictl stopp       None
   Delete a pod.     None           crictl rmp         None
   ================= ============== ================== ====

.. note::

   Containers created and started by containerd are immediately deleted by kubelet. containerd does not support suspending, resuming, restarting, renaming, and waiting for containers, nor Docker image build, import, export, comparison, push, search, and labeling. containerd does not support file copy. You can log in to the image repository by modifying the configuration file of containerd.

Differences in Tracing
----------------------

-  Docker (Kubernetes v1.23 and earlier versions):

   kubelet --> dockershim (in the kubelet process) --> docker --> containerd

-  Docker (community solution for Kubernetes v1.24 or later):

   kubelet --> cri-dockerd (kubelet uses CRI to connect to cri-dockerd) --> docker--> containerd

-  containerd:

   kubelet --> CRI plugin (in the containerd process) --> containerd

Although Docker has added functions such as swarm cluster, docker build, and Docker APIs, it also introduces bugs. Compared with containerd, Docker has one more layer of calling. **Therefore, containerd is more resource-saving and secure.**

Container Engine Versions
-------------------------

-  Docker

   -  EulerOS/CentOS: docker-engine 18.9.0, a Docker version customized for CCE. Security vulnerabilities will be fixed promptly.

-  containerd: 1.6.14

   .. note::

      If the cluster version is v1.28.8-r0, v1.29.4-r0, v1.30.1-r0, or later, the containerd version of new nodes is 1.7.16.
