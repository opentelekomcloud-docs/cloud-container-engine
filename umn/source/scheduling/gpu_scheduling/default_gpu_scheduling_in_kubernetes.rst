:original_name: cce_10_0345.html

.. _cce_10_0345:

Default GPU Scheduling in Kubernetes
====================================

You can use GPUs in CCE containers.

Prerequisites
-------------

-  A GPU node has been created. For details, see :ref:`Creating a Node <cce_10_0363>`.

-  The CCE AI Suite (NVIDIA GPU) add-on has been installed, with the selected driver matching the GPU model on the node. For details, see :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>`.

-  When default GPU scheduling is used in clusters of v1.27 or earlier, the CCE AI Suite (NVIDIA GPU) add-on mounts the driver directory to **/usr/local/nvidia/lib64**. To use GPU resources in containers, you need to add **/usr/local/nvidia/lib64** to the **LD_LIBRARY_PATH** environment variable. You can skip this step for clusters of v1.28 or later.

   You can add environment variables in any of the following ways:

   -  Configure the **LD_LIBRARY_PATH** environment variable in the Dockerfile used for creating an image. (Recommended)

      .. code-block::

         ENV LD_LIBRARY_PATH /usr/local/nvidia/lib64:$LD_LIBRARY_PATH

   -  Configure the **LD_LIBRARY_PATH** environment variable in the image startup command.

      .. code-block::

         /bin/bash -c "export LD_LIBRARY_PATH=/usr/local/nvidia/lib64:$LD_LIBRARY_PATH && ..."

   -  Define the **LD_LIBRARY_PATH** environment variable when creating a workload. (Ensure that this variable is not configured in the container. Otherwise, it will be overwritten.)

      .. code-block::

         ...
                   env:
                     - name: LD_LIBRARY_PATH
                       value: /usr/local/nvidia/lib64
         ...

Using GPUs
----------

Create a workload and request GPUs. You can specify the number of GPUs as follows:

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: gpu-test
     namespace: default
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: gpu-test
     template:
       metadata:
         labels:
           app: gpu-test
       spec:
         containers:
         - image: nginx:perl
           name: container-0
           resources:
             requests:
               cpu: 250m
               memory: 512Mi
               nvidia.com/gpu: 1   # Number of requested GPUs
             limits:
               cpu: 250m
               memory: 512Mi
               nvidia.com/gpu: 1   # Maximum number of GPUs that can be used
         imagePullSecrets:
         - name: default-secret

**nvidia.com/gpu** specifies the number of GPUs to be requested. The value can be smaller than **1**. For example, **nvidia.com/gpu: 0.5** indicates that multiple pods share a GPU. In this case, all the requested GPU resources come from the same GPU card.

.. note::

   When you use **nvidia.com/gpu** to specify the number of GPUs, the values of requests and limits must be the same.

After **nvidia.com/gpu** is specified, workloads will not be scheduled to nodes without GPUs. If the node is GPU-starved, Kubernetes events similar to the following are reported:

-  0/2 nodes are available: 2 Insufficient nvidia.com/gpu.
-  0/4 nodes are available: 1 InsufficientResourceOnSingleGPU, 3 Insufficient nvidia.com/gpu.

To use GPU resources on the CCE console, you only need to configure the GPU quota when creating a workload.

GPU Node Labels
---------------

CCE will label GPU nodes that are ready to use. When using GPUs, you can enable affinity between pods and nodes based on labels so that the pods can be scheduled to the correct nodes. To do so, perform the following operations:

#. Run the following command to view all nodes in the cluster and retrieve the value of the **accelerator** label (if applicable):

   .. code-block::

      kubectl get node -L accelerator

   The following information is displayed, showing the label value:

   .. code-block::

      NAME           STATUS   ROLES    AGE     VERSION                                    ACCELERATOR
      10.100.2.179   Ready    <none>   8m43s   v1.19.10-r0-CCE21.11.1.B006-21.11.1.B006   nvidia-t4

#. When using a YAML file to create a workload, add the following parameters to define affinity between pods and nodes:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: gpu-test
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: gpu-test
        template:
          metadata:
            labels:
              app: gpu-test
          spec:
            nodeSelector:
              accelerator: nvidia-t4
            containers:
            - image: nginx:perl
              name: container-0
              resources:
                requests:
                  cpu: 250m
                  memory: 512Mi
                  nvidia.com/gpu: 1   # Number of requested GPUs
                limits:
                  cpu: 250m
                  memory: 512Mi
                  nvidia.com/gpu: 1   # Maximum number of GPUs that can be used
            imagePullSecrets:
            - name: default-secret
