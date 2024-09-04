:original_name: cce_faq_00200.html

.. _cce_faq_00200:

What Should I Do If a Storage Volume Cannot Be Mounted or the Mounting Times Out?
=================================================================================

Troubleshooting Process
-----------------------

The issues here are described in order of how likely they are to occur.

Check these causes one by one until you find the cause of the fault.

-  :ref:`Check Item 1: Whether EVS Volumes Are Mounted Across AZs <cce_faq_00200__section239014414225>`
-  :ref:`Check Item 2: Whether Multiple Permission Configurations Exist in the Storage Volume <cce_faq_00200__section15311186248>`
-  :ref:`Check Item 3: Whether There Is More Than One Replica for a Deployment with EVS Volumes <cce_faq_00200__section151615354218>`
-  :ref:`Check Item 4: Whether the EVS Disk File System Is Damaged <cce_faq_00200__section0802134413523>`


.. figure:: /_static/images/en-us_image_0000001981435165.png
   :alt: **Figure 1** Troubleshooting for storage volume mounting failure or mounting timeout

   **Figure 1** Troubleshooting for storage volume mounting failure or mounting timeout

.. _cce_faq_00200__section239014414225:

Check Item 1: Whether EVS Volumes Are Mounted Across AZs
--------------------------------------------------------

**Symptom**

Mounting an EVS volume to a StatefulSet times out.

**Fault Locating**

If your node is in **AZ 1** but the volume to be mounted is in **AZ 2**, the mounting times out and the volume cannot be mounted.

**Solution**

Create a volume in the same AZ as the node and mount the volume.

.. _cce_faq_00200__section15311186248:

Check Item 2: Whether Multiple Permission Configurations Exist in the Storage Volume
------------------------------------------------------------------------------------

If the volume to be mounted stores too many data and involves permission-related configurations, the file permissions need to be modified one by one, which results in mounting timeout.

**Fault Locating**

-  Check whether the **securityContext** field contains **runAsuser** and **fsGroup**. **securityContext** is a Kubernetes field that defines the permission and access control settings of pods or containers.
-  Check whether the startup commands contain commands used to obtain or modify file permissions, such as **ls**, **chmod**, and **chown**.

**Solution**

Determine whether to modify the settings based on your service requirements.

.. _cce_faq_00200__section151615354218:

Check Item 3: Whether There Is More Than One Replica for a Deployment with EVS Volumes
--------------------------------------------------------------------------------------

**Symptom**

The pod fails to be created, and an event indicating that the storage fails to be added is reported.

.. code-block::

   Multi-Attach error for volume "pvc-62a7a7d9-9dc8-42a2-8366-0f5ef9db5b60" Volume is already used by pod(s) testttt-7b774658cb-lc98h

**Fault Locating**

Check whether the number of replicas of the Deployment is greater than 1.

If the Deployment uses an EVS volume, the number of replicas can only be 1. If you specify more than two pods for the Deployment on the backend, CCE does not restrict the creation of the Deployment. However, if these pods are scheduled to different nodes, some pods cannot be started because the EVS volumes used by the pods cannot be mounted to the nodes.

**Solution**

Set the number of replicas of the Deployment that uses an EVS volume to 1 or use other volume types.

.. _cce_faq_00200__section0802134413523:

Check Item 4: Whether the EVS Disk File System Is Damaged
---------------------------------------------------------

**Symptom**

The pod fails to be created, and information similar to the following is displayed, indicating that the disk file system is damaged.

.. code-block::

   MountVolume.MountDevice failed for volume "pvc-08178474-c58c-4820-a828-14437d46ba6f" : rpc error: code = Internal desc = [09060def-afd0-11ec-9664-fa163eef47d0] /dev/sda has file system, but it is detected to be damaged

**Solution**

Back up the disk in EVS and run the following command to restore the file system:

**fsck -y {Drive letter}**
