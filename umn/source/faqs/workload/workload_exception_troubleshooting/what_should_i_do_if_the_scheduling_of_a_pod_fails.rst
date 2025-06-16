:original_name: cce_faq_00098.html

.. _cce_faq_00098:

What Should I Do If the Scheduling of a Pod Fails?
==================================================

Fault Locating
--------------

If a pod is in the **Pending** state and the events contain the information that indicates a pod scheduling failure, you can locate the cause based on the events. For details about how to view events, see :ref:`How Can I Locate the Root Cause If a Workload Is Abnormal? <cce_faq_00134>`

Troubleshooting
---------------

Determine the cause based on the events, as listed in :ref:`Table 1 <cce_faq_00098__table230510269532>`.

.. _cce_faq_00098__table230510269532:

.. table:: **Table 1** Events related to a pod scheduling failure

   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | Event                                                                                                                                                        | Cause and Solution                                                                                                          |
   +==============================================================================================================================================================+=============================================================================================================================+
   | no nodes available to schedule pods.                                                                                                                         | There are not any available nodes in the cluster.                                                                           |
   |                                                                                                                                                              |                                                                                                                             |
   |                                                                                                                                                              | :ref:`Check Item 1: Whether a Node Is Available in the Cluster <cce_faq_00098__section133416392418>`                        |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | 0/2 nodes are available: 2 Insufficient cpu.                                                                                                                 | The resources (CPU and memory) on the node are insufficient.                                                                |
   |                                                                                                                                                              |                                                                                                                             |
   | 0/2 nodes are available: 2 Insufficient memory.                                                                                                              | :ref:`Check Item 2: Whether Node Resources (CPU and Memory) Are Sufficient <cce_faq_00098__section29231833141817>`          |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | 0/2 nodes are available: 1 node(s) didn't match node selector, 1 node(s) didn't match pod affinity rules, 1 node(s) didn't match pod affinity/anti-affinity. | The node and pod affinity configurations are mutually exclusive. No node meets the pod requirements.                        |
   |                                                                                                                                                              |                                                                                                                             |
   |                                                                                                                                                              | :ref:`Check Item 3: Affinity and Anti-Affinity Configuration of the Workload <cce_faq_00098__section794092214205>`          |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | 0/2 nodes are available: 2 node(s) had volume node affinity conflict.                                                                                        | The EVS volume mounted to the pod and the node are not in the same AZ.                                                      |
   |                                                                                                                                                              |                                                                                                                             |
   |                                                                                                                                                              | :ref:`Check Item 4: Whether the Workload's Volume and the Node Are in the Same AZ <cce_faq_00098__section197421559143010>`  |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | 0/1 nodes are available: 1 node(s) had taints that the pod didn't tolerate.                                                                                  | There are some taints on the node, and the pod cannot tolerate these taints.                                                |
   |                                                                                                                                                              |                                                                                                                             |
   |                                                                                                                                                              | :ref:`Check Item 5: Tolerations of the Pod <cce_faq_00098__section188241489126>`                                            |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | 0/7 nodes are available: 7 Insufficient ephemeral-storage.                                                                                                   | The ephemeral storage space on the node is insufficient.                                                                    |
   |                                                                                                                                                              |                                                                                                                             |
   |                                                                                                                                                              | :ref:`Check Item 6: Ephemeral Volume Usage <cce_faq_00098__section096718509019>`                                            |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | 0/1 nodes are available: 1 everest driver not found at node                                                                                                  | The everest-csi-driver on the node is not in the running state.                                                             |
   |                                                                                                                                                              |                                                                                                                             |
   |                                                                                                                                                              | :ref:`Check Item 7: Whether the CCE Container Storage (Everest) Add-on Works Properly <cce_faq_00098__section136595495137>` |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | Failed to create pod sandbox: ...                                                                                                                            | The node thin pool space is insufficient.                                                                                   |
   |                                                                                                                                                              |                                                                                                                             |
   | Create more free space in thin pool or use dm.min_free_space option to change behavior                                                                       | :ref:`Check Item 8: Whether the Thin Pool Space Is Sufficient <cce_faq_00098__section1739734419111>`                        |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | 0/1 nodes are available: 1 Too many pods.                                                                                                                    | The number of pods scheduled to the node exceeded the maximum number allowed by the node.                                   |
   |                                                                                                                                                              |                                                                                                                             |
   |                                                                                                                                                              | :ref:`Check Item 9: Whether the Node Has Too Many Pods Scheduled onto It <cce_faq_00098__section24491119103316>`            |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
   | UnexpectedAdmissionError Allocate failed due to not enough cpus available to satisfy request, which is unexpected.                                           | The kubelet static CPU pinning is abnormal due to a known community issue.                                                  |
   |                                                                                                                                                              |                                                                                                                             |
   |                                                                                                                                                              | :ref:`Check Item 10: Whether the Static CPU Pinning of kubelet Is Abnormal <cce_faq_00098__section27458132316>`             |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------+

.. _cce_faq_00098__section133416392418:

Check Item 1: Whether a Node Is Available in the Cluster
--------------------------------------------------------

You can log in to the CCE console and check whether the node status is **Available**. You can also use the following command to check whether the node status is **Ready**:

.. code-block::

   $ kubectl get node
   NAME           STATUS   ROLES    AGE   VERSION
   192.168.0.37   Ready    <none>   21d   v1.19.10-r1.0.0-source-121-gb9675686c54267
   192.168.0.71   Ready    <none>   21d   v1.19.10-r1.0.0-source-121-gb9675686c54267

If the status of all nodes is **Not Ready**, it means that there are no available nodes in the cluster.

**Solution**

-  Add a node. If no affinity rule is configured for the workload, the pod will be automatically scheduled to the new node to ensure proper service operation.
-  Locate the unavailable nodes and rectify the faults. For details, see :ref:`What Should I Do If a Cluster Is Available But Some Nodes in It Are Unavailable? <cce_faq_00120>`
-  Reset the unavailable nodes.

.. _cce_faq_00098__section29231833141817:

Check Item 2: Whether Node Resources (CPU and Memory) Are Sufficient
--------------------------------------------------------------------

**0/2 nodes are available: 2 Insufficient cpu.** indicates that the CPUs are insufficient.

**0/2 nodes are available: 2 Insufficient memory.** indicates that the memory is insufficient.

If the resources requested by the pod exceed the allocatable resources on the node where the pod will run, the pod scheduling onto the node will definitely fail due to insufficient node resources.

If there are fewer allocatable resources on the node than the resources that a pod requests, the pod scheduling will fail.

**Solution**

Add more nodes to the cluster. Scale-out is the common solution to insufficient resources.

.. _cce_faq_00098__section794092214205:

Check Item 3: Affinity and Anti-Affinity Configuration of the Workload
----------------------------------------------------------------------

Inappropriate affinity policies will cause the pod scheduling to fail.

For example, an anti-affinity policy is configured for workload 1 and workload 2. They run on node 1 and node 2, respectively.

If you try to configure an affinity policy for workload 3 and workload 2 and then deploy workload 3 on a node different from one hosting workload 2, such as node 1, it will cause a conflict and lead to the workload deployment failure.

0/2 nodes are available: 1 node(s) didn't match **node selector**, 1 node(s) didn't match **pod affinity rules**, 1 node(s) didn't match **pod affinity/anti-affinity**.

-  **node selector** indicates that the node affinity is not met.
-  **pod affinity rules** indicate that the pod affinity is not met.
-  **pod affinity/anti-affinity** indicates that the pod affinity and anti-affinity are not met.

**Solution**

-  When configuring workload-workload affinity and workload-node affinity policies, ensure that these policies do not conflict with each other, or the workload deployment will fail.

-  For a workload that has a node affinity policy configured, you need to make sure that **supportContainer** in the label of the affinity node is set to **true**. Otherwise, pods cannot be scheduled onto the node and the following event is generated:

   .. code-block::

      No nodes are available that match all of the following predicates: MatchNode Selector, NodeNotSupportsContainer

   If the value is **false**, the pod scheduling will fail.

.. _cce_faq_00098__section197421559143010:

Check Item 4: Whether the Workload's Volume and the Node Are in the Same AZ
---------------------------------------------------------------------------

**0/2 nodes are available: 2 node(s) had volume node affinity conflict.** indicates that an affinity conflict occurs between the volume mounted to the pod and the host node. As a result, the pod scheduling fails.

This is because EVS disks cannot be attached to nodes in different AZs from the EVS disks. For example, a workload pod with an EVS volume that is in AZ 1 cannot be scheduled to a node in AZ 2.

The EVS volumes created on CCE have affinity settings by default, as shown below.

.. code-block::

   kind: PersistentVolume
   apiVersion: v1
   metadata:
     name: pvc-c29bfac7-efa3-40e6-b8d6-229d8a5372ac
   spec:
     ...
     nodeAffinity:
       required:
         nodeSelectorTerms:
           - matchExpressions:
               - key: failure-domain.beta.kubernetes.io/zone
                 operator: In
                 values:
                   -

**Solution**

In the AZ where the workload's node resides, create a volume. Alternatively, create an identical workload and select an automatically assigned cloud storage volume.

.. _cce_faq_00098__section188241489126:

Check Item 5: Tolerations of the Pod
------------------------------------

**0/1 nodes are available: 1 node(s) had taints that the pod didn't tolerate.** indicates that there are some taints on the node, and the pod cannot tolerate these taints.

In this case, you can check the taints on the node. If information similar to the following is displayed, there are some taints on the node:

.. code-block::

   $ kubectl describe node 192.168.0.37
   Name:               192.168.0.37
   ...
   Taints:             key1=value1:NoSchedule
   ...

In some cases, the system automatically adds a taint to a node. The built-in taints include:

-  .. _cce_faq_00098__li64721233348:

   node.kubernetes.io/not-ready: The node is not ready.

-  node.kubernetes.io/unreachable: The node controller cannot access the node.

-  node.kubernetes.io/memory-pressure: The node is under memory pressure.

-  node.kubernetes.io/disk-pressure: The node is under disk pressure. In this case, follow the instructions described in :ref:`Check Item 4: Whether the Node Disk Space Is Insufficient <cce_faq_00015__section165209286116>` to handle it.

-  node.kubernetes.io/pid-pressure: The node is under PID pressure.

-  node.kubernetes.io/network-unavailable: The node network is unavailable.

-  node.kubernetes.io/unschedulable: The node is unschedulable.

-  node.cloudprovider.kubernetes.io/uninitialized: When kubelet is started with an external cloud platform driver specified, it adds a taint to the node, marking it as unavailable. After cloud-controller-manager initializes the node, kubelet deletes the taint.

**Solution**

To schedule the pod to the node, use either of the following methods:

-  If the taint is added by a user, you can delete the taint on the node. If the taint is :ref:`automatically added by the system <cce_faq_00098__li64721233348>`, the taint will be automatically deleted after the fault is rectified.

-  Specify a toleration for the pod containing the taint. For details, see `Taints and Tolerations <https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/>`__.

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        name: nginx
      spec:
        containers:
        - name: nginx
          image: nginx:alpine
        tolerations:
        - key: "key1"
          operator: "Equal"
          value: "value1"
          effect: "NoSchedule"

.. _cce_faq_00098__section096718509019:

Check Item 6: Ephemeral Volume Usage
------------------------------------

**0/7 nodes are available: 7 Insufficient ephemeral-storage.** indicates that there are not enough ephemeral storage space on the node.

In this case, you can check whether the space of the ephemeral volume is limited by the pod. If the ephemeral volume space required by the application exceeds the existing capacity on the node, the application cannot be scheduled to that node. To solve this problem, change the space of the ephemeral volume or expand the disk capacity on the node.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: frontend
   spec:
     containers:
     - name: app
       image: images.my-company.example/app:v4
       resources:
         requests:
           ephemeral-storage: "2Gi"
         limits:
           ephemeral-storage: "4Gi"
       volumeMounts:
       - name: ephemeral
         mountPath: "/tmp"
     volumes:
       - name: ephemeral
         emptyDir: {}

To obtain the total capacity (**Capacity**) and available capacity (**Allocatable**) of the temporary volumes on the node, run the **kubectl describe node** command and check the memory request and limit of the allocated temporary volume on the node.

The following is an example of the output:

.. code-block::

   ...
   Capacity:
     cpu:                4
     ephemeral-storage:  61607776Ki
     hugepages-1Gi:      0
     hugepages-2Mi:      0
     localssd:           0
     localvolume:        0
     memory:             7614352Ki
     pods:               40
   Allocatable:
     cpu:                3920m
     ephemeral-storage:  56777726268
     hugepages-1Gi:      0
     hugepages-2Mi:      0
     localssd:           0
     localvolume:        0
     memory:             6180752Ki
     pods:               40
   ...
   Allocated resources:
     (Total limits may be over 100 percent, i.e., overcommitted.)
     Resource           Requests      Limits
     --------           --------      ------
     cpu                1605m (40%)   6530m (166%)
     memory             2625Mi (43%)  5612Mi (92%)
     ephemeral-storage  0 (0%)        0 (0%)
     hugepages-1Gi      0 (0%)        0 (0%)
     hugepages-2Mi      0 (0%)        0 (0%)
     localssd           0             0
     localvolume        0             0
   Events:              <none>

.. _cce_faq_00098__section136595495137:

Check Item 7: Whether the CCE Container Storage (Everest) Add-on Works Properly
-------------------------------------------------------------------------------

**0/1 nodes are available: 1 everest driver not found at node**. indicates that everest-csi-driver of CCE Container Storage (Everest) is not started properly on the node.

In this case, you can check the daemon named **everest-csi-driver** in the **kube-system** namespace and check whether the pod is started properly. If it is not, delete the pod. The daemon will restart another pod.

.. _cce_faq_00098__section1739734419111:

Check Item 8: Whether the Thin Pool Space Is Sufficient
-------------------------------------------------------

A data disk dedicated for kubelet and the container engine will be attached to a new node. If the data disk space is insufficient, the pod cannot be created on the node.

**Solution 1: Clearing images**

Perform the following operations to clear unused images:

-  Nodes that use containerd

   #. Obtain local images on the node.

      .. code-block::

         crictl images -v

   #. Delete the unnecessary images by image ID.

      .. code-block::

         crictl rmi {Image ID}

-  Nodes that use Docker

   #. Obtain local images on the node.

      .. code-block::

         docker images

   #. Delete the unnecessary images by image ID.

      .. code-block::

         docker rmi {}Image ID}

.. note::

   Do not delete system images such as the **cce-pause** image. Otherwise, the pod creation may fail.

**Solution 2: Expanding the disk capacity**

To expand a disk capacity, perform the following operations:

#. Expand the capacity of a data disk on the EVS console.

   Only the storage capacity of EVS disks can be expanded. You need to perform the following operations to expand the capacity of logical volumes and file systems.

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Nodes**. In the right pane, click the **Nodes** tab, locate the row containing the target node, and choose **More** > **Sync Server Data** in the **Operation** column.

#. Log in to the target node.

#. Run **lsblk** to view the block device information of the node.

   A data disk is divided depending on the container storage **Rootfs**:

   Overlayfs: No independent thin pool is allocated. Image data is stored in **dockersys**.

   a. Check the disk and partition space of the device.

      .. code-block::

         # lsblk
         NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         sda                   8:0    0   50G  0 disk
         └─sda1                8:1    0   50G  0 part /
         sdb                   8:16   0  150G  0 disk      # The data disk has been expanded to 150 GiB, but 50 GiB space is free.
         ├─vgpaas-dockersys  253:0    0   90G  0 lvm  /var/lib/containerd
         └─vgpaas-kubernetes 253:1    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

   b. Expand the disk capacity.

      Add the new disk capacity to the **dockersys** logical volume used by the container engine.

      #. Expand the PV capacity so that LVM can identify the new EVS capacity. */dev/sdb* specifies the physical volume where dockersys is located.

         .. code-block::

            pvresize /dev/sdb

         Information similar to the following is displayed:

         .. code-block::

            Physical volume "/dev/sdb" changed
            1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      #. Expand 100% of the free capacity to the logical volume. *vgpaas/dockersys* specifies the logical volume used by the container engine.

         .. code-block::

            lvextend -l+100%FREE -n vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Size of logical volume vgpaas/dockersys changed from <90.00 GiB (23039 extents) to 140.00 GiB (35840 extents).
            Logical volume vgpaas/dockersys successfully resized.

      #. Adjust the size of the file system. */dev/vgpaas/dockersys* specifies the file system path of the container engine.

         .. code-block::

            resize2fs /dev/vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Filesystem at /dev/vgpaas/dockersys is mounted on /var/lib/containerd; on-line resizing required
            old_desc_blocks = 12, new_desc_blocks = 18
            The filesystem on /dev/vgpaas/dockersys is now 36700160 blocks long.

   c. Check whether the capacity has been expanded.

      .. code-block::

         # lsblk
         NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         sda                   8:0    0   50G  0 disk
         └─sda1                8:1    0   50G  0 part /
         sdb                   8:16   0  150G  0 disk
         ├─vgpaas-dockersys  253:0    0   140G  0 lvm  /var/lib/containerd
         └─vgpaas-kubernetes 253:1    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

   Device Mapper: A thin pool is allocated to store image data.

   a. Check the disk and partition space of the device.

      .. code-block::

         # lsblk
         NAME                                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         vda                                   8:0    0   50G  0 disk
         └─vda1                                8:1    0   50G  0 part /
         vdb                                   8:16   0  200G  0 disk
         ├─vgpaas-dockersys                  253:0    0   18G  0 lvm  /var/lib/docker
         ├─vgpaas-thinpool_tmeta             253:1    0    3G  0 lvm
         │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm                   # Space used by thin pool
         │   ...
         ├─vgpaas-thinpool_tdata             253:2    0   67G  0 lvm
         │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm
         │   ...
         └─vgpaas-kubernetes                 253:4    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

   b. Expand the disk capacity.

      Option 1: Add the new disk capacity to the thin pool.

      #. Expand the PV capacity so that LVM can identify the new EVS capacity. */dev/vdb* specifies the physical volume where thin pool is located.

         .. code-block::

            pvresize /dev/vdb

         Information similar to the following is displayed:

         .. code-block::

            Physical volume "/dev/vdb" changed
            1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      #. Expand 100% of the free capacity to the logical volume. *vgpaas/thinpool* specifies the logical volume used by the container engine.

         .. code-block::

            lvextend -l+100%FREE -n vgpaas/thinpool

         Information similar to the following is displayed:

         .. code-block::

            Size of logical volume vgpaas/thinpool changed from <67.00 GiB (23039 extents) to <167.00 GiB (48639 extents).
            Logical volume vgpaas/thinpool successfully resized.

      #. Do not need to adjust the size of the file system, because the thin pool is not mounted to any devices.

      #. Run the **lsblk** command to check the disk and partition space of the device and check whether the capacity has been expanded. If the new disk capacity was added to the thin pool, the capacity has been expanded.

         .. code-block::

            # lsblk
            NAME                                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
            vda                                   8:0    0   50G  0 disk
            └─vda1                                8:1    0   50G  0 part /
            vdb                                   8:16   0  200G  0 disk
            ├─vgpaas-dockersys                  253:0    0   18G  0 lvm  /var/lib/docker
            ├─vgpaas-thinpool_tmeta             253:1    0    3G  0 lvm
            │ └─vgpaas-thinpool                 253:3    0   167G  0 lvm             # Thin pool space after capacity expansion
            │   ...
            ├─vgpaas-thinpool_tdata             253:2    0   67G  0 lvm
            │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm
            │   ...
            └─vgpaas-kubernetes                 253:4    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

      Option 2: Add the new disk capacity to the **dockersys** disk.

      #. Expand the PV capacity so that LVM can identify the new EVS capacity. */dev/vdb* specifies the physical volume where dockersys is located.

         .. code-block::

            pvresize /dev/vdb

         Information similar to the following is displayed:

         .. code-block::

            Physical volume "/dev/vdb" changed
            1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      #. Expand 100% of the free capacity to the logical volume. *vgpaas/dockersys* specifies the logical volume used by the container engine.

         .. code-block::

            lvextend -l+100%FREE -n vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Size of logical volume vgpaas/dockersys changed from <18.00 GiB (4607 extents) to <118.00 GiB (30208 extents).
            Logical volume vgpaas/dockersys successfully resized.

      #. Adjust the size of the file system. */dev/vgpaas/dockersys* specifies the file system path of the container engine.

         .. code-block::

            resize2fs /dev/vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Filesystem at /dev/vgpaas/dockersys is mounted on /var/lib/docker; on-line resizing required
            old_desc_blocks = 3, new_desc_blocks = 15
            The filesystem on /dev/vgpaas/dockersys is now 30932992 blocks long.

      #. Run the **lsblk** command to check the disk and partition space of the device and check whether the capacity has been expanded. If the new disk capacity was added to the dockersys, the capacity has been expanded.

         .. code-block::

            # lsblk
            NAME                                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
            vda                                   8:0    0   50G  0 disk
            └─vda1                                8:1    0   50G  0 part /
            vdb                                   8:16   0  200G  0 disk
            ├─vgpaas-dockersys                  253:0    0   118G  0 lvm  /var/lib/docker     # dockersys after capacity expansion
            ├─vgpaas-thinpool_tmeta             253:1    0    3G  0 lvm
            │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm
            │   ...
            ├─vgpaas-thinpool_tdata             253:2    0   67G  0 lvm
            │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm
            │   ...
            └─vgpaas-kubernetes                 253:4    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

.. _cce_faq_00098__section24491119103316:

Check Item 9: Whether the Node Has Too Many Pods Scheduled onto It
------------------------------------------------------------------

**0/1 nodes are available: 1 Too many pods.** indicates excessive number of pods have been scheduled to the node.

When creating a node, configure **Max. Pods** in the **Advanced Settings** area to specify the maximum number of pods that can run properly on the node. The default value varies with the node flavor. You can change the value as needed.

On the **Nodes** page, obtain the **Pods (Allocated/Total Available Addresses/Total)** value of the node, and check whether the number of pods scheduled onto the node has reached the upper limit. If so, add nodes or change the maximum number of pods.

To change the maximum number of pods that can run on a node, do as follows:

-  For nodes in the default node pool: Change the **Max. Pods** value when resetting the node.
-  For nodes in a custom node pool: Change the value of the node pool parameter **max-pods**.

.. _cce_faq_00098__section27458132316:

Check Item 10: Whether the Static CPU Pinning of kubelet Is Abnormal
--------------------------------------------------------------------

If a pod has an init container with a CPU request that is different from the main container settings, and it is assigned a Guaranteed QoS class while the kubelet uses static CPU pinning, the pod scheduling could fail, resulting in the error **UnexpectedAdmissionError**.

Community-related issue: https://github.com/kubernetes/kubernetes/issues/112228

**Solution**

Set the CPU request of the init container to a decimal value that matches the CPU limit and avoid using CPU pinning.

For example: the main container: {"limits":{"cpu":"7","memory":"60G"},"requests":{"cpu":"7","memory":"60G"}}; the init container: {"limits":{"cpu":"6.9","memory":"60G"},"requests":{"cpu":"6.9","memory":"60G"}}
