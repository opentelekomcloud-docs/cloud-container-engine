:original_name: cce_faq_00098.html

.. _cce_faq_00098:

What Should I Do If Pod Scheduling Fails?
=========================================

Fault Locating
--------------

If the pod is in the **Pending** state and the event contains pod scheduling failure information, locate the cause based on the event information. For details about how to view events, see :ref:`How Do I Use Events to Fix Abnormal Workloads? <cce_faq_00134>`

Troubleshooting Process
-----------------------

Determine the cause based on the event information, as listed in :ref:`Table 1 <cce_faq_00098__table230510269532>`.

.. _cce_faq_00098__table230510269532:

.. table:: **Table 1** Pod scheduling failure

   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------+
   | Event                                                                                                                                                        | Cause and Solution                                                                                                        |
   +==============================================================================================================================================================+===========================================================================================================================+
   | no nodes available to schedule pods.                                                                                                                         | No node is available in the cluster.                                                                                      |
   |                                                                                                                                                              |                                                                                                                           |
   |                                                                                                                                                              | :ref:`Check Item 1: Whether a Node Is Available in the Cluster <cce_faq_00098__section133416392418>`                      |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------+
   | 0/2 nodes are available: 2 Insufficient cpu.                                                                                                                 | Node resources (CPU and memory) are insufficient.                                                                         |
   |                                                                                                                                                              |                                                                                                                           |
   | 0/2 nodes are available: 2 Insufficient memory.                                                                                                              | :ref:`Check Item 2: Whether Node Resources (CPU and Memory) Are Sufficient <cce_faq_00098__section29231833141817>`        |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------+
   | 0/2 nodes are available: 1 node(s) didn't match node selector, 1 node(s) didn't match pod affinity rules, 1 node(s) didn't match pod affinity/anti-affinity. | The node and pod affinity configurations are mutually exclusive. No node meets the pod requirements.                      |
   |                                                                                                                                                              |                                                                                                                           |
   |                                                                                                                                                              | :ref:`Check Item 3: Affinity and Anti-Affinity Configuration of the Workload <cce_faq_00098__section794092214205>`        |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------+
   | 0/2 nodes are available: 2 node(s) had volume node affinity conflict.                                                                                        | The EVS volume mounted to the pod and the node are not in the same AZ.                                                    |
   |                                                                                                                                                              |                                                                                                                           |
   |                                                                                                                                                              | :ref:`Check Item 4: Whether the Workload's Volume and Node Reside in the Same AZ <cce_faq_00098__section197421559143010>` |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------+
   | 0/1 nodes are available: 1 node(s) had taints that the pod didn't tolerate.                                                                                  | Taints exist on the node, but the pod cannot tolerate these taints.                                                       |
   |                                                                                                                                                              |                                                                                                                           |
   |                                                                                                                                                              | :ref:`Check Item 5: Taint Toleration of Pods <cce_faq_00098__section188241489126>`                                        |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------+
   | 0/7 nodes are available: 7 Insufficient ephemeral-storage.                                                                                                   | The ephemeral storage space of the node is insufficient.                                                                  |
   |                                                                                                                                                              |                                                                                                                           |
   |                                                                                                                                                              | :ref:`Check Item 6: Ephemeral Volume Usage <cce_faq_00098__section096718509019>`                                          |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------+
   | 0/1 nodes are available: 1 everest driver not found at node                                                                                                  | The everest-csi-driver on the node is not in the running state.                                                           |
   |                                                                                                                                                              |                                                                                                                           |
   |                                                                                                                                                              | :ref:`Check Item 7: Whether everest Works Properly <cce_faq_00098__section136595495137>`                                  |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------+
   | Failed to create pod sandbox: ...                                                                                                                            | The node thin pool space is insufficient.                                                                                 |
   |                                                                                                                                                              |                                                                                                                           |
   | Create more free space in thin pool or use dm.min_free_space option to change behavior                                                                       | :ref:`Check Item 8: Thin Pool Space <cce_faq_00098__section1739734419111>`                                                |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------+
   | 0/1 nodes are available: 1 Too many pods.                                                                                                                    | The number of pods scheduled to the node exceeded the maximum number allowed by the node.                                 |
   |                                                                                                                                                              |                                                                                                                           |
   |                                                                                                                                                              | :ref:`Check Item 9: Number of Pods Scheduled onto the Node <cce_faq_00098__section24491119103316>`                        |
   +--------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------+

.. _cce_faq_00098__section133416392418:

Check Item 1: Whether a Node Is Available in the Cluster
--------------------------------------------------------

Log in to the CCE console and check whether the node status is **Available**. Alternatively, run the following command to check whether the node status is **Ready**:

.. code-block::

   $ kubectl get node
   NAME           STATUS   ROLES    AGE   VERSION
   192.168.0.37   Ready    <none>   21d   v1.19.10-r1.0.0-source-121-gb9675686c54267
   192.168.0.71   Ready    <none>   21d   v1.19.10-r1.0.0-source-121-gb9675686c54267

If the status of all nodes is **Not Ready**, no node is available in the cluster.

**Solution**

-  Add a node. If an affinity policy is not configured for the workload, the pod will be automatically migrated to the new node to ensure that services are running properly.
-  Locate the unavailable node and rectify the fault. For details, see :ref:`What Should I Do If a Cluster Is Available But Some Nodes Are Unavailable? <cce_faq_00120>`
-  Reset the unavailable node.

.. _cce_faq_00098__section29231833141817:

Check Item 2: Whether Node Resources (CPU and Memory) Are Sufficient
--------------------------------------------------------------------

**0/2 nodes are available: 2 Insufficient cpu.** This means insufficient CPUs.

**0/2 nodes are available: 2 Insufficient memory.** This means insufficient memory.

If the resources requested by the pod exceed the allocatable resources of the node where the pod runs, the node cannot provide the resources required to run new pods and pod scheduling onto the node will definitely fail.

If the number of resources that can be allocated to a node is less than the number of resources that a pod requests, the node does not meet the resource requirements of the pod. As a result, the scheduling fails.

**Solution**

Add nodes to the cluster. Scale-out is the common solution to insufficient resources.

.. _cce_faq_00098__section794092214205:

Check Item 3: Affinity and Anti-Affinity Configuration of the Workload
----------------------------------------------------------------------

Inappropriate affinity policies will cause pod scheduling to fail.

Example:

An anti-affinity relationship is established between workload 1 and workload 2. Workload 1 is deployed on node 1 while workload 2 is deployed on node 2.

When you try to deploy workload 3 on node 1 and establish an affinity relationship with workload 2, a conflict occurs, resulting in a workload deployment failure.

0/2 nodes are available: 1 node(s) didn't match **node selector**, 1 node(s) didn't match **pod affinity rules**, 1 node(s) didn't match **pod affinity/anti-affinity**.

-  **node selector** indicates that the node affinity is not met.
-  **pod affinity rules** indicate that the pod affinity is not met.
-  **pod affinity/anti-affinity** indicates that the pod affinity/anti-affinity is not met.

**Solution**

-  When adding workload-workload affinity and workload-node affinity policies, ensure that the two types of policies do not conflict each other. Otherwise, workload deployment will fail.

-  If the workload has a node affinity policy, make sure that **supportContainer** in the label of the affinity node is set to **true**. Otherwise, pods cannot be scheduled onto the affinity node and the following event is generated:

   .. code-block::

      No nodes are available that match all of the following predicates: MatchNode Selector, NodeNotSupportsContainer

   If the value is **false**, the scheduling fails.

.. _cce_faq_00098__section197421559143010:

Check Item 4: Whether the Workload's Volume and Node Reside in the Same AZ
--------------------------------------------------------------------------

**0/2 nodes are available: 2 node(s) had volume node affinity conflict.** An affinity conflict occurs between volumes and nodes. As a result, the scheduling fails.

This is because EVS disks cannot be attached to nodes across AZs. For example, if the EVS volume is located in AZ 1 and the node is located in AZ 2, scheduling fails.

The EVS volume created on CCE has affinity settings by default, as shown below.

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

Check Item 5: Taint Toleration of Pods
--------------------------------------

**0/1 nodes are available: 1 node(s) had taints that the pod didn't tolerate.** This means the node is tainted and the pod cannot be scheduled to the node.

Check the taints on the node. If the following information is displayed, taints exist on the node:

.. code-block::

   $ kubectl describe node 192.168.0.37
   Name:               192.168.0.37
   ...
   Taints:             key1=value1:NoSchedule
   ...

In some cases, the system automatically adds a taint to a node. The current built-in taints include:

-  .. _cce_faq_00098__li64721233348:

   node.kubernetes.io/not-ready: The node is not ready.

-  node.kubernetes.io/unreachable: The node controller cannot access the node.

-  node.kubernetes.io/memory-pressure: The node has memory pressure.

-  node.kubernetes.io/disk-pressure: The node has disk pressure. Follow the instructions described in :ref:`Check Item 4: Whether the Node Disk Space Is Insufficient <cce_faq_00015__section165209286116>` to handle it.

-  node.kubernetes.io/pid-pressure: The node is under PID pressure.

-  node.kubernetes.io/network-unavailable: The node network is unavailable.

-  node.kubernetes.io/unschedulable: The node cannot be scheduled.

-  node.cloudprovider.kubernetes.io/uninitialized: If an external cloud platform driver is specified when kubelet is started, kubelet adds a taint to the current node and marks it as unavailable. After **cloud-controller-manager** initializes the node, kubelet deletes the taint.

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

**0/7 nodes are available: 7 Insufficient ephemeral-storage.** This means insufficient ephemeral storage of the node.

Check whether the size of the ephemeral volume in the pod is limited. If the size of the ephemeral volume required by the application exceeds the existing capacity of the node, the application cannot be scheduled. To solve this problem, change the size of the ephemeral volume or expand the disk capacity of the node.

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

To obtain the total capacity (**Capacity**) and available capacity (**Allocatable**) of the temporary volume mounted to the node, run the **kubectl describe node** command, and view the application value and limit value of the temporary volume mounted to the node.

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

Check Item 7: Whether everest Works Properly
--------------------------------------------

**0/1 nodes are available: 1 everest driver not found at node**. This means the everest-csi-driver of everest is not started properly on the node.

Check the daemon named **everest-csi-driver** in the kube-system namespace and check whether the pod is started properly. If not, delete the pod. The daemon will restart the pod.

.. _cce_faq_00098__section1739734419111:

Check Item 8: Thin Pool Space
-----------------------------

A data disk dedicated for kubelet and the container engine will be attached to a new node. If the data disk space is insufficient, the pod cannot be created.

**Solution 1: Clearing images**

Perform the following operations to clear unused images:

-  Nodes that use containerd

   #. Obtain local images on the node.

      .. code-block::

         crictl images -v

   #. Delete the images that are not required by image ID.

      .. code-block::

         crictl rmi Image ID

-  Nodes that use Docker

   #. Obtain local images on the node.

      .. code-block::

         docker images

   #. Delete the images that are not required by image ID.

      .. code-block::

         docker rmi Image ID

.. note::

   Do not delete system images such as the cce-pause image. Otherwise, pods may fail to be created.

**Solution 2: Expanding the disk capacity**

To expand a disk capacity, perform the following steps:

#. Expand the capacity of a data disk on the EVS console.

   Only the storage capacity of the EVS disk is expanded. You also need to perform the following steps to expand the capacity of the logical volume and file system.

#. Log in to the CCE console and click the cluster. In the navigation pane, choose **Nodes**. Click **More** > **Sync Server Data** in the row containing the target node.

#. Log in to the target node.

#. Run the **lsblk** command to check the block device information of the node.

   A data disk is divided depending on the container storage **Rootfs**:

   Overlayfs: No independent thin pool is allocated. Image data is stored in **dockersys**.

   a. Check the disk and partition sizes of the device.

      .. code-block::

         # lsblk
         NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         vda                   8:0    0   50G  0 disk
         └─vda1                8:1    0   50G  0 part /
         vdb                   8:16   0  200G  0 disk      # Data disk has been expanded but not allocated
         ├─vgpaas-dockersys  253:0    0   90G  0 lvm  /var/lib/containerd          # Space used by the container engine
         └─vgpaas-kubernetes 253:1    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet  # Space used by Kubernetes

   b. Expand the disk capacity.

      Add the new disk capacity to the **dockersys** logical volume used by the container engine.

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

            Size of logical volume vgpaas/dockersys changed from <90.00 GiB (23039 extents) to <190.00 GiB (48639 extents).
            Logical volume vgpaas/dockersys successfully resized.

      #. Adjust the size of the file system. */dev/vgpaas/dockersys* specifies the file system path of the container engine.

         .. code-block::

            resize2fs /dev/vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Filesystem at /dev/vgpaas/dockersys is mounted on /var/lib/containerd; on-line resizing required
            old_desc_blocks = 12, new_desc_blocks = 24
            The filesystem on /dev/vgpaas/dockersys is now 49807360 (4k) blocks long.

   Devicemapper: A thin pool is allocated to store image data.

   a. Check the disk and partition sizes of the device.

      .. code-block::

         # lsblk
         NAME                                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         vda                                   8:0    0   50G  0 disk
         └─vda1                                8:1    0   50G  0 part /
         vdb                                   8:16   0  200G  0 disk
         ├─vgpaas-dockersys                  253:0    0   18G  0 lvm  /var/lib/docker
         ├─vgpaas-thinpool_tmeta             253:1    0    3G  0 lvm
         │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm                   # Space used by thinpool
         │   ...
         ├─vgpaas-thinpool_tdata             253:2    0   67G  0 lvm
         │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm
         │   ...
         └─vgpaas-kubernetes                 253:4    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

   b. Expand the disk capacity.

      Option 1: Add the new disk capacity to the thin pool disk.

      #. Expand the PV capacity so that LVM can identify the new EVS capacity. */dev/vdb* specifies the physical volume where thinpool is located.

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

            Size of logical volume vgpaas/dockersys changed from <18.00 GiB (7679 extents) to <118.00 GiB (33279 extents).
            Logical volume vgpaas/dockersys successfully resized.

      #. Adjust the size of the file system. */dev/vgpaas/dockersys* specifies the file system path of the container engine.

         .. code-block::

            resize2fs /dev/vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Filesystem at /dev/vgpaas/dockersys is mounted on /var/lib/docker; on-line resizing required
            old_desc_blocks = 4, new_desc_blocks = 16
            The filesystem on /dev/vgpaas/dockersys is now 49807360 (4k) blocks long.

.. _cce_faq_00098__section24491119103316:

Check Item 9: Number of Pods Scheduled onto the Node
----------------------------------------------------

**0/1 nodes are available: 1 Too many pods.** indicates excessive number of pods have been scheduled to the node.

When creating a node, configure **Max. Pods** in **Advanced Settings** to specify the maximum number of pods that can run properly on the node. The default value varies with the node flavor. You can change the value as needed.

On the **Nodes** page, obtain the **Pods (Allocated/Total)** value of the node, and check whether the number of pods scheduled onto the node has reached the upper limit. If so, add nodes or change the maximum number of pods.

To change the maximum number of pods that can run on a node, do as follows:

-  For nodes in the default node pool: Change the **Max. Pods** value when resetting the node.
-  For nodes in a customized node pool: Change the value of the node pool parameter **max-pods**.
