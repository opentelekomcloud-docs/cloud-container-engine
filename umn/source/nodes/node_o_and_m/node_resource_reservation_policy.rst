:original_name: cce_10_0178.html

.. _cce_10_0178:

Node Resource Reservation Policy
================================

Some node resources are used to run mandatory Kubernetes system components and resources to make the node as part of your cluster. Therefore, the total number of node resources and the number of allocatable node resources for your cluster are different. The larger the node specifications, the more the containers deployed on the node. Therefore, more node resources need to be reserved to run Kubernetes components.

To ensure node stability, a certain number of CCE node resources will be reserved for Kubernetes components (such as kubelet, kube-proxy, and docker) based on the node specifications.

CCE calculates the resources that can be allocated to user nodes as follows:

**Allocatable resources = Total amount - Reserved amount - Eviction threshold**

The memory eviction threshold is fixed at 100 MB.

.. note::

   **Total amount** indicates the available memory of the ECS, excluding the memory used by system components. Therefore, the total amount is slightly less than the memory of the node flavor.

When the memory consumed by all pods on a node increases, the following behaviors may occur:

#. When the available memory of the node is lower than the eviction threshold, kubelet is triggered to evict the pod. For details about the eviction threshold in Kubernetes, see `Node-pressure Eviction <https://kubernetes.io/docs/concepts/scheduling-eviction/node-pressure-eviction>`__.
#. If a node triggers an OS memory insufficiency event (OOM) before kubelet reclaims memory, the system terminates the container. However, different from pod eviction, kubelet restarts the container based on the RestartPolicy of the pod.

Rules v1 for Reserving Node Memory
----------------------------------

.. note::

   For clusters of versions earlier than **v1.21.4-r0** and **v1.23.3-r0**, the v1 model is used for node memory reservation. For clusters of **v1.21.4-r0**, **v1.23.3-r0**, or later, the node memory reservation model is optimized to v2. For details, see :ref:`Rules for Reserving Node Memory v2 <cce_10_0178__section156741258145010>`.

You can use the following formula calculate how much memory you should reserve for running containers on a node:

Total reserved amount = :ref:`Reserved memory for system components <cce_10_0178__table19962121035915>` + :ref:`Reserved memory for kubelet to manage pods <cce_10_0178__table124614211528>`

.. _cce_10_0178__table19962121035915:

.. table:: **Table 1** Reservation rules for system components

   +----------------------+-------------------------------------------------------------------------+
   | Total Memory (TM)    | Reserved Memory for System Components                                   |
   +======================+=========================================================================+
   | TM <= 8 GB           | 0 MB                                                                    |
   +----------------------+-------------------------------------------------------------------------+
   | 8 GB < TM <= 16 GB   | [(TM - 8 GB) x 1024 x 10%] MB                                           |
   +----------------------+-------------------------------------------------------------------------+
   | 16 GB < TM <= 128 GB | [8 GB x 1024 x 10% + (TM - 16 GB) x 1024 x 6%] MB                       |
   +----------------------+-------------------------------------------------------------------------+
   | TM > 128 GB          | (8 GB x 1024 x 10% + 112 GB x 1024 x 6% + (TM - 128 GB) x 1024 x 2%) MB |
   +----------------------+-------------------------------------------------------------------------+

.. _cce_10_0178__table124614211528:

.. table:: **Table 2** Reservation rules for kubelet

   +-------------------+---------------------------------+-------------------------------------------------+
   | Total Memory (TM) | Number of Pods                  | Reserved Memory for kubelet                     |
   +===================+=================================+=================================================+
   | TM <= 2 GB        | None                            | TM x 25%                                        |
   +-------------------+---------------------------------+-------------------------------------------------+
   | TM > 2 GB         | 0 < Max. pods on a node <= 16   | 700 MB                                          |
   +-------------------+---------------------------------+-------------------------------------------------+
   |                   | 16 < Max. pods on a node <= 32  | [700 + (Max. pods on a node - 16) x 18.75] MB   |
   +-------------------+---------------------------------+-------------------------------------------------+
   |                   | 32 < Max. pods on a node <= 64  | [1024 + (Max. pods on a node - 32) x 6.25] MB   |
   +-------------------+---------------------------------+-------------------------------------------------+
   |                   | 64 < Max. pods on a node <= 128 | [1230 + (Max. pods on a node - 64) x 7.80] MB   |
   +-------------------+---------------------------------+-------------------------------------------------+
   |                   | Max. pods on a node > 128       | [1740 + (Max. pods on a node - 128) x 11.20] MB |
   +-------------------+---------------------------------+-------------------------------------------------+

.. important::

   For a small-capacity node, adjust the maximum number of instances based on the site requirements. Alternatively, when creating a node on the CCE console, you can adjust the maximum number of instances for the node based on the node specifications.

.. _cce_10_0178__section156741258145010:

Rules for Reserving Node Memory v2
----------------------------------

For clusters of **v1.21.4-r0**, **v1.23.3-r0**, or later, the node memory reservation model is optimized to v2 and can be dynamically adjusted using the node pool parameters **kube-reserved-mem** and **system-reserved-mem**. For details, see :ref:`Configuring a Node Pool <cce_10_0652>`.

The total reserved node memory of the v2 model is equal to the sum of that reserved for the OS and that reserved for CCE to manage pods.

Reserved memory includes basic and floating parts. For the OS, the floating memory depends on the node specifications. For CCE, the floating memory depends on the number of pods on a node.

.. table:: **Table 3** Rules for reserving node memory v2

   +-----------------+--------------------------------------------------------+----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Reserved for    | Basic/Floating                                         | Reservation          | Used by                                                                                                                                                                                             |
   +=================+========================================================+======================+=====================================================================================================================================================================================================+
   | OS              | Basic                                                  | 400 MB (fixed)       | OS service components such as sshd and systemd-journald.                                                                                                                                            |
   +-----------------+--------------------------------------------------------+----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | Floating (depending on the node memory)                | 25 MB/GB             | Kernel                                                                                                                                                                                              |
   +-----------------+--------------------------------------------------------+----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | CCE             | Basic                                                  | 500 MB (fixed)       | Container engine components, such as kubelet and kube-proxy, when the node is unloaded                                                                                                              |
   +-----------------+--------------------------------------------------------+----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | Floating (depending on the number of pods on the node) | Docker: 20 MB/pod    | Container engine components when the number of pods increases                                                                                                                                       |
   |                 |                                                        |                      |                                                                                                                                                                                                     |
   |                 |                                                        | containerd: 5 MB/pod | .. note::                                                                                                                                                                                           |
   |                 |                                                        |                      |                                                                                                                                                                                                     |
   |                 |                                                        |                      |    When the v2 model reserves memory for a node by default, the default maximum number of pods is estimated based on the memory. For details, see :ref:`Table 1 <cce_10_0348__table1710310156526>`. |
   +-----------------+--------------------------------------------------------+----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Rules for Reserving Node CPU
----------------------------

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

Rules for CCE to Reserve Data Disks on Nodes
--------------------------------------------

CCE uses Logical Volume Manager (LVM) to manage disks. LVM creates a metadata area on a disk to store logical and physical volumes, occupying 4 MiB space. Therefore, the actual available disk space of a node is equal to the disk size minus 4 MiB.
