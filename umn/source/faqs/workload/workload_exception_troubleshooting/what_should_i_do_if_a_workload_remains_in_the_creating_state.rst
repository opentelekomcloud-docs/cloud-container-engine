:original_name: cce_faq_00140.html

.. _cce_faq_00140:

What Should I Do If a Workload Remains in the Creating State?
=============================================================

Symptom
-------

The workload remains in the creating state.

Troubleshooting
---------------

Possible causes are listed in order of likelihood.

Check these causes one by one until you find the cause of the fault.

-  :ref:`Check Item 1: Whether the cce-pause Image Is Deleted by Mistake <cce_faq_00140__section141501505112>`
-  :ref:`Check Item 2: Modifying Node Specifications After the CPU Management Policy Is Enabled in the Cluster <cce_faq_00140__section109321639135910>`

.. _cce_faq_00140__section141501505112:

Check Item 1: Whether the cce-pause Image Is Deleted by Mistake
---------------------------------------------------------------

**Symptom**

When creating a workload, you may see an error indicating that the sandbox cannot be created. This is because the **cce-pause:3.1** image fails to be pulled.

.. code-block::

   Failed to create pod sandbox: rpc error: code = Unknown desc = failed to get sandbox image "cce-pause:3.1": failed to pull image "cce-pause:3.1": failed to pull and unpack image "docker.io/library/cce-pause:3.1": failed to resolve reference "docker.io/library/cce-pause:3.1": pulling from host **** failed with status code [manifests 3.1]: 400 Bad Request

**Possible Cause**

The image is a system image added during node creation. If the image is deleted by mistake, the workload cannot be created.

**Solution**

.. note::

   In clusters v1.25.16-r30, v1.27.16-r30, v1.28.15-r20, v1.29.13-r0, v1.30.10-r0, v1.31.6-r0, v1.32.1-r0, or later, the **cce-pause** container image supports protection against accidental deletion. For clusters that do not meet these version requirements, perform the following steps to manually restore the image.

#. Log in to the faulty node.

#. Decompress the cce-pause image installation package.

   .. code-block::

      tar -xzvf /opt/cloud/cce/package/node-package/pause-*.tgz

#. Import the image.

   -  Docker nodes:

      .. code-block::

         docker load -i ./pause/package/image/cce-pause-*.tar

   -  containerd nodes:

      .. code-block::

         ctr -n k8s.io images import --all-platforms ./pause/package/image/cce-pause-*.tar

#. Check the **cce-pause** image.

   -  Docker nodes

      .. code-block::

         docker images | grep pause

      Information similar to the following is displayed:

      .. code-block::

         cce-pause               3.1               83e7b9ecaec8               2 months ago               785kB

   -  containerd nodes

      .. code-block::

         crictl image | grep pause

      The expected command output is as follows:

      .. code-block::

         cce-pause               3.1               1fab9a083c585               686kB

#. Check the **cce-pause** image used by kubelet.

   .. code-block::

      ps aux | grep kubelet | grep -o "pod-infra-container-image=[^ ]*"

   Information similar to the following is displayed:

   .. code-block::

      pod-infra-container-image=cce-pause:3.1

   The output shows that the pause image used by kubelet is **cce-pause:3.1**. If the image name obtained in the previous step is **docker.io/library/cce-pause** instead of **cce-pause**, rename the image to **cce-pause**.

   -  Docker nodes

      .. code-block::

         docker tag docker.io/library/cce-pause:3.1 cce-pause:3.1

   -  containerd nodes

      .. code-block::

         ctr -n k8s.io image tag docker.io/library/cce-pause:3.1 cce-pause:3.1

#. Lock the image to prevent accidental deletion.

   -  Docker nodes do not support image deletion protection.

   -  containerd nodes:

      .. code-block::

         ctr -n k8s.io images label cce-pause:3.1 io.cri-containerd.pinned=pinned

      .. note::

         This operation requires containerd version 1.7.0 or later on the node. To check the version, run **containerd version**.

         |image1|

      Check whether the image is locked.

      .. code-block::

         crictl images -v

      The output is similar to the following:

      .. code-block::

         Pinned: true

      If the value is **true**, the image is locked and will not be reclaimed by kubelet garbage collection (GC).

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

.. |image1| image:: /_static/images/en-us_image_0000002631532272.png
