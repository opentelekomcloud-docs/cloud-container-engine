:original_name: cce_10_0345.html

.. _cce_10_0345:

Default GPU Scheduling in Kubernetes
====================================

CCE standard and Turbo clusters support Kubernetes' default GPU scheduling mode. This mode uses a device plugin to manage GPUs as a standard resource type. After the CCE AI Suite (NVIDIA GPU) add-on is installed on a node, CCE automatically detects the number of GPUs on the node and allocates them to pods based on the **resources.limits** specified during scheduling. **nvidia.com/gpu** can be set to an integer or a decimal.

-  If **nvidia.com/gpu** is set to a positive integer, for example, **1**, an entire physical GPU will be exclusively allocated to the target pod. This is ideal for scenarios requiring high performance and strict isolation.
-  If **nvidia.com/gpu** is set to a decimal, for example, **0.2**, multiple pods can share a physical GPU, including its compute and memory resources. This is suitable for scenarios with lower compute demands, such as lightweight inference tasks.

This section describes how to use Kubernetes' default GPU scheduling. For more information, see `Schedule GPUs <https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/>`__.

Prerequisites
-------------

-  A GPU node has been created. For details, see :ref:`Creating a Node <cce_10_0363>`.

-  The CCE AI Suite (NVIDIA GPU) add-on has been installed, with the selected driver matching the GPU model on the node. For details, see :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>`.

-  When default GPU scheduling is used in clusters of v1.27 or earlier, the CCE AI Suite (NVIDIA GPU) add-on mounts the driver directory to **/usr/local/nvidia/lib64**. To use GPU resources in containers, you need to add **/usr/local/nvidia/lib64** to the **LD_LIBRARY_PATH** environment variable. You can skip this step for clusters of v1.28 or later.

   You can add environment variables in any of the following ways:

   -  (Recommended) Configure the **LD_LIBRARY_PATH** environment variable in the Dockerfile used for creating an image.

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

Using the Console
-----------------

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Workloads**. In the upper right corner of the displayed page, click **Create Workload**.
#. In the **Container Settings** area, choose **Basic Info**, locate **GPU Quota**, select a scheduling mode, and specify the required resources. Default GPU scheduling involves **GPU card** and **Shared**.

   -  **GPU card**: An entire physical GPU will be exclusively allocated to the target pod.
   -  **Shared**: Multiple pods can share a physical GPU, including its compute and memory resources.

#. (Optional) Specify GPU memory for the workload. After the setting, CCE will enable affinity between pods and nodes based on the resource type so that the pods can be scheduled to appropriate nodes.
#. Configure other parameters by referring to :ref:`Creating a Workload <cce_10_0673>`. After completing the settings, click **Create Workload** in the lower right corner. When the workload changes to the **Running** state, it is created.

Using kubectl
-------------

#. Use kubectl to access the cluster.

#. Write a YAML file for creating a workload with Kubernetes' default GPU scheduling enabled:

   .. code-block::

      vim gpu-app.yaml

   Example file content:

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
                  nvidia.com/gpu: 1   # (Optional) The value must be the same as that of limits.nvidia.com/gpu.
                limits:
                  cpu: 250m
                  memory: 512Mi
                  nvidia.com/gpu: 1   # Specified number of GPUs
            imagePullSecrets:
            - name: default-secret

   -  **nodeSelector**: (Optional) specifies a node selector. After a GPU node is created, CCE adds a label to it. When using GPUs, you can enable affinity between pods and nodes based on labels so that the pods can be scheduled to appropriate nodes.

      Obtain nodes with a specified label:

      .. code-block::

         kubectl get node -L accelerator

      Information similar to the following is displayed, where the information in bold is the label value:

      .. code-block::

         NAME           STATUS   ROLES    AGE     VERSION                                    ACCELERATOR
         10.100.2.179   Ready    <none>   8m43s   v1.19.10-r0-CCE21.11.1.B006-21.11.1.B006   nvidia-t4

   -  **resources.limits.nvidia.com/gpu**: specifies the number of GPUs.

      -  If **nvidia.com/gpu** is set to a positive integer, for example, **1**, an entire physical GPU will be exclusively allocated to the target pod.
      -  If **nvidia.com/gpu** is set to a decimal, for example, **0.2**, multiple pods can share a physical GPU, including its compute and memory resources.

      .. note::

         **requests.nvidia.com/gpu** is optional. **If it is specified, ensure its value is the same as that of limits.nvidia.com/gpu.**

#. Create the workload.

   .. code-block::

      kubectl apply -f gpu-app.yaml

   If information similar to the following is displayed, the workload has been created:

   .. code-block::

      deployment.apps/gpu-test created

#. View the created pod.

   .. code-block::

      kubectl get pod -n default

   Information similar to the following is displayed:

   .. code-block::

      NAME                      READY   STATUS    RESTARTS   AGE
      gpu-test-6bdb4d7cb-pmtc2   1/1     Running   0          21s

#. Access the container.

   .. code-block::

      kubectl -n default exec -it gpu-test-6bdb4d7cb-pmtc2 -c container-0 -- /bin/bash

#. Check whether the GPU has been allocated to the container.

   .. code-block::

      nvidia-smi

   The command output indicates that the GPU has been allocated.

   |image1|

.. |image1| image:: /_static/images/en-us_image_0000002467678641.png
