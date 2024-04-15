:original_name: cce_faq_00140.html

.. _cce_faq_00140:

What Should I Do If a Workload Remains in the Creating State?
=============================================================

Symptom
-------

The workload remains in the creating state.

Troubleshooting Process
-----------------------

The issues here are described in order of how likely they are to occur.

Check these causes one by one until you find the cause of the fault.

-  :ref:`Check Item 1: Whether the cce-pause Image Is Deleted by Mistake <cce_faq_00140__section141501505112>`
-  :ref:`Check Item 2: Modifying Node Specifications After the CPU Management Policy Is Enabled in the Cluster <cce_faq_00140__section109321639135910>`

.. _cce_faq_00140__section141501505112:

Check Item 1: Whether the cce-pause Image Is Deleted by Mistake
---------------------------------------------------------------

**Symptom**

When creating a workload, an error message indicating that the sandbox cannot be created is displayed. This is because the **cce-pause:3.1** image fails to be pulled.

.. code-block::

   Failed to create pod sandbox: rpc error: code = Unknown desc = failed to get sandbox image "cce-pause:3.1": failed to pull image "cce-pause:3.1": failed to pull and unpack image "docker.io/library/cce-pause:3.1": failed to resolve reference "docker.io/library/cce-pause:3.1": pulling from host **** failed with status code [manifests 3.1]: 400 Bad Request

**Possible Causes**

The image is a system image added during node creation. If the image is deleted by mistake, the workload cannot be created.

**Solution**

#. Log in to the faulty node.

#. Decompress the cce-pause image installation package.

   .. code-block::

      tar -xzvf /opt/cloud/cce/package/node-package/pause-*.tgz

#. Import the image.

   -  Docker nodes:

      .. code-block::

         docker load -i ./pause/package/image/cce-pause-3.1.tar

   -  containerd nodes:

      .. code-block::

         ctr -n k8s.io image import ./pause/package/image/cce-pause-3.1.tar

#. Create a workload.

.. _cce_faq_00140__section109321639135910:

Check Item 2: Modifying Node Specifications After the CPU Management Policy Is Enabled in the Cluster
-----------------------------------------------------------------------------------------------------

The kubelet option **cpu-manager-policy** defaults to **static**. This allows granting enhanced CPU affinity and exclusivity to pods with certain resource characteristics on the node. If you modify CCE node specifications on the ECS console, the original CPU information does not match the new CPU information. As a result, workloads on the node cannot be restarted or created.

#. Log in to the CCE node (ECS) and delete the **cpu_manager_state** file.

   Example command for deleting the file:

   .. code-block::

      rm -rf /mnt/paas/kubernetes/kubelet/cpu_manager_state

#. Restart the node or kubelet. The following is the kubelet restart command:

   .. code-block::

      systemctl restart kubelet

   Verify that workloads on the node can be successfully restarted or created.

   For details, see :ref:`What Should I Do If I Fail to Restart or Create Workloads on a Node After Modifying the Node Specifications? <cce_faq_00189>`.
