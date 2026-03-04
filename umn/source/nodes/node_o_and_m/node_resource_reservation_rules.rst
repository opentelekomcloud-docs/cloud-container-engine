:original_name: cce_10_0178.html

.. _cce_10_0178:

Node Resource Reservation Rules
===============================

Some node resources are used to run mandatory Kubernetes system components and resources to make the node as part of your cluster. Therefore, the total number of node resources and the number of allocatable node resources for your cluster are different. The larger the node specifications, the more the containers deployed on the node. Therefore, more node resources need to be reserved to run Kubernetes components.

To maintain node stability, a specific number of CCE node resources will be reserved for Kubernetes components like kubelet, kube-proxy, and docker, based on the node specifications.

CCE calculates the resources that can be allocated to user nodes as follows:

**Allocatable = Capacity - Reserved - Eviction threshold**

The memory eviction threshold is fixed at 100 MiB.

.. note::

   **Total amount** indicates the available memory of the ECS, excluding the memory used by system components. Therefore, it is slightly less than the total memory of the node flavor.

When the memory consumed by all pods on a node increases, the following behaviors may occur:

#. When the available memory of the node is lower than the eviction threshold, kubelet is triggered to evict pods. For details about the eviction threshold in Kubernetes, see `Node-pressure Eviction <https://kubernetes.io/docs/concepts/scheduling-eviction/node-pressure-eviction>`__.
#. If a node triggers an OS memory insufficiency event (OOM) before kubelet reclaims memory, the system terminates the container. Unlike pod eviction, kubelet restarts the container based on the pod's RestartPolicy.

Relationship Between Node Resource Reservation and Cluster Versions
-------------------------------------------------------------------

+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
| Cluster Version | Specific Version                                          | Node Memory Reservation                                                                             | Node CPU Reservation                                                | Node Data Disk Reservation                                                   |
+=================+===========================================================+=====================================================================================================+=====================================================================+==============================================================================+
| v1.29 or later  | None                                                      | :ref:`Node Memory Reservation Rule v2 <cce_10_0178__section156741258145010>`                        | :ref:`Node CPU Reservation Rules <cce_10_0178__section65041914716>` | :ref:`Node Data Disk Reservation Rules <cce_10_0178__section10719193504911>` |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
| v1.28           | v1.28.2-r0 or later                                       | :ref:`Node Memory Reservation Rule v2 <cce_10_0178__section156741258145010>`                        |                                                                     |                                                                              |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
|                 | Earlier than v1.28.2-r0                                   | -  Custom node pools: :ref:`Node Memory Reservation Rule v1 <cce_10_0178__section16856143934620>`   |                                                                     |                                                                              |
|                 |                                                           | -  Default node pools: :ref:`Node Memory Reservation Rule v2 <cce_10_0178__section156741258145010>` |                                                                     |                                                                              |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
| v1.27           | v1.27.4-r0 or later                                       | :ref:`Node Memory Reservation Rule v2 <cce_10_0178__section156741258145010>`                        |                                                                     |                                                                              |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
|                 | Earlier than v1.27.4-r0                                   | -  Custom node pools: :ref:`Node Memory Reservation Rule v1 <cce_10_0178__section16856143934620>`   |                                                                     |                                                                              |
|                 |                                                           | -  Default node pools: :ref:`Node Memory Reservation Rule v2 <cce_10_0178__section156741258145010>` |                                                                     |                                                                              |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
| v1.25           | v1.25.7-r0 or later                                       | :ref:`Node Memory Reservation Rule v2 <cce_10_0178__section156741258145010>`                        |                                                                     |                                                                              |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
|                 | Earlier than v1.25.7-r0                                   | -  Custom node pools: :ref:`Node Memory Reservation Rule v1 <cce_10_0178__section16856143934620>`   |                                                                     |                                                                              |
|                 |                                                           | -  Default node pools: :ref:`Node Memory Reservation Rule v2 <cce_10_0178__section156741258145010>` |                                                                     |                                                                              |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
| v1.23           | v1.23.12-r0 or later                                      | :ref:`Node Memory Reservation Rule v2 <cce_10_0178__section156741258145010>`                        |                                                                     |                                                                              |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
|                 | v1.23.3-r0 or later, or versions earlier than v1.23.12-r0 | -  Custom node pools: :ref:`Node Memory Reservation Rule v1 <cce_10_0178__section16856143934620>`   |                                                                     |                                                                              |
|                 |                                                           | -  Default node pools: :ref:`Node Memory Reservation Rule v2 <cce_10_0178__section156741258145010>` |                                                                     |                                                                              |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
|                 | Earlier than v1.23.3-r0                                   | :ref:`Node Memory Reservation Rule v1 <cce_10_0178__section16856143934620>`                         |                                                                     |                                                                              |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
| v1.21           | v1.21.13-r0 or later                                      | :ref:`Node Memory Reservation Rule v2 <cce_10_0178__section156741258145010>`                        |                                                                     |                                                                              |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
|                 | v1.21.4-r0 or later, or versions earlier than v1.21.13-r0 | -  Custom node pools: :ref:`Node Memory Reservation Rule v1 <cce_10_0178__section16856143934620>`   |                                                                     |                                                                              |
|                 |                                                           | -  Default node pools: :ref:`Node Memory Reservation Rule v2 <cce_10_0178__section156741258145010>` |                                                                     |                                                                              |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+
|                 | Earlier than v1.21.4-r0                                   | :ref:`Node Memory Reservation Rule v1 <cce_10_0178__section16856143934620>`                         |                                                                     |                                                                              |
+-----------------+-----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------+------------------------------------------------------------------------------+

.. _cce_10_0178__section16856143934620:

Node Memory Reservation Rule v1
-------------------------------

You can use the following formula to calculate how much memory you should reserve for running containers on a node:

Total reserved amount = :ref:`Reserved memory for system components <cce_10_0178__table19962121035915>` + :ref:`Reserved memory for kubelet to manage pods <cce_10_0178__table124614211528>`

.. _cce_10_0178__table19962121035915:

.. table:: **Table 1** Reservation rules for system components

   +------------------------+-----------------------------------------------------------------------------+
   | Total Memory (TM)      | Reserved Memory for System Components                                       |
   +========================+=============================================================================+
   | TM <= 8 GiB            | 0 MiB                                                                       |
   +------------------------+-----------------------------------------------------------------------------+
   | 8 GiB < TM <= 16 GiB   | [(TM - 8 GiB) x 1024 x 10%] MiB                                             |
   +------------------------+-----------------------------------------------------------------------------+
   | 16 GiB < TM <= 128 GiB | [8 GiB x 1024 x 10% + (TM - 16 GiB) x 1024 x 6%] MiB                        |
   +------------------------+-----------------------------------------------------------------------------+
   | TM > 128 GiB           | [8 GiB x 1024 x 10% + 112 GiB x 1024 x 6% + (TM - 128 GiB) x 1024 x 2%] MiB |
   +------------------------+-----------------------------------------------------------------------------+

.. _cce_10_0178__table124614211528:

.. table:: **Table 2** Reservation rules for kubelet

   +-------------------+---------------------------------+---------------------------------------------------+
   | Total Memory (TM) | Number of Pods                  | Reserved Memory for kubelet                       |
   +===================+=================================+===================================================+
   | TM <= 2 GiB       | None                            | TM x 25%                                          |
   +-------------------+---------------------------------+---------------------------------------------------+
   | TM > 2 GiB        | 0 < Max. pods on a node <= 16   | 700 MiB                                           |
   +-------------------+---------------------------------+---------------------------------------------------+
   |                   | 16 < Max. pods on a node <= 32  | [700 + (Max. pods on a node - 16) x 18.75] MiB    |
   +-------------------+---------------------------------+---------------------------------------------------+
   |                   | 32 < Max. pods on a node <= 64  | [1,024 + (Max. pods on a node - 32) x 6.25] MiB   |
   +-------------------+---------------------------------+---------------------------------------------------+
   |                   | 64 < Max. pods on a node <= 128 | [1,230 + (Max. pods on a node - 64) x 7.80] MiB   |
   +-------------------+---------------------------------+---------------------------------------------------+
   |                   | Max. pods on a node > 128       | [1,740 + (Max. pods on a node - 128) x 11.20] MiB |
   +-------------------+---------------------------------+---------------------------------------------------+

.. important::

   For a small-capacity node, adjust the maximum number of pods based on site requirements. Alternatively, when creating a node on the CCE console, you can adjust the maximum number of pods on the node based on the node specifications.

.. _cce_10_0178__section156741258145010:

Node Memory Reservation Rule v2
-------------------------------

The total reserved memory of a CCE node using memory reservation rule v2 includes memory allocated for **system components** and **Kubernetes**.

Reserved memory includes basic and floating parts. For system components, the floating memory depends on the node specifications. For Kubernetes, the floating memory depends on the number of pods on a node.

By default, CCE automatically calculates the reserved memory based on the rules listed in :ref:`Table 3 <cce_10_0178__table1061012595214>`. You can specify the reserved memory by configuring parameters **system-reserved-mem** and **kube-reserved-mem** in the node pool settings. For details, see :ref:`Managing Node Pool Configurations <cce_10_0652>`.

.. _cce_10_0178__table1061012595214:

.. table:: **Table 3** Node memory reservation rule v2

   +---------------------+--------------------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Memory Reserved For | Basic/Floating                                         | Reservation           | Used By                                                                                                                                                                                             |
   +=====================+========================================================+=======================+=====================================================================================================================================================================================================+
   | System components   | Basic                                                  | Fixed at 400 MiB      | OS service components such as sshd and systemd-journald.                                                                                                                                            |
   +---------------------+--------------------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                     | Floating (depending on the node memory)                | 25 MiB/GiB            | Kernel                                                                                                                                                                                              |
   +---------------------+--------------------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Kubernetes          | Basic                                                  | Fixed at 500 MiB      | Container engine components, such as kubelet and kube-proxy, when the node is unloaded                                                                                                              |
   +---------------------+--------------------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                     | Floating (depending on the number of pods on the node) | Docker: 20 MiB/Pod    | Container engine components when the number of pods increases                                                                                                                                       |
   |                     |                                                        |                       |                                                                                                                                                                                                     |
   |                     |                                                        | containerd: 5 MiB/pod | .. note::                                                                                                                                                                                           |
   |                     |                                                        |                       |                                                                                                                                                                                                     |
   |                     |                                                        |                       |    When the v2 model reserves memory for a node by default, the default maximum number of pods is estimated based on the memory. For details, see :ref:`Table 1 <cce_10_0348__table1710310156526>`. |
   +---------------------+--------------------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0178__section65041914716:

Node CPU Reservation Rules
--------------------------

.. table:: **Table 4** Node CPU reservation rules

   +----------------------------+------------------------------------------------------------------------+
   | Total CPU Cores (Total)    | Reserved CPU Cores                                                     |
   +============================+========================================================================+
   | Total <= 1 core            | Total x 6%                                                             |
   +----------------------------+------------------------------------------------------------------------+
   | 1 core < Total <= 2 cores  | 1 core x 6% + (Total - 1 core) x 1%                                    |
   +----------------------------+------------------------------------------------------------------------+
   | 2 cores < Total <= 4 cores | 1 core x 6% + 1 core x 1% + (Total - 2 cores) x 0.5%                   |
   +----------------------------+------------------------------------------------------------------------+
   | Total > 4 cores            | 1 core x 6% + 1 core x 1% + 2 cores x 0.5% + (Total - 4 cores) x 0.25% |
   +----------------------------+------------------------------------------------------------------------+

.. _cce_10_0178__section10719193504911:

Node Data Disk Reservation Rules
--------------------------------

CCE uses Logical Volume Manager (LVM) for disk management. LVM creates a metadata area on a disk to store logical and physical volume information, occupying 4 MiB of space. Therefore, the actual available disk space of a node is the disk size minus 4 MiB.
