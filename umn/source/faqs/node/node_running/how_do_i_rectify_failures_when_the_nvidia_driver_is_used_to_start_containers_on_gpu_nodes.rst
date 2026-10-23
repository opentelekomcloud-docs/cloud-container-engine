:original_name: cce_faq_00020.html

.. _cce_faq_00020:

How Do I Rectify Failures When the NVIDIA Driver Is Used to Start Containers on GPU Nodes?
==========================================================================================

Did a Resource Scheduling Failure Event Occur on a Cluster Node?
----------------------------------------------------------------

**Symptom**

A node is running properly and has GPU resources. However, the following error information is displayed:

0/9 nodes are available: 9 insufficient nvidia.com/gpu

**Fault Locating**

#. Check whether the node is attached with NVIDIA label.

   |image1|

#. Check whether the NVIDIA driver is running properly.

   Log in to the node where the add-on is running and view the driver installation log in the **/opt/cloud/cce/nvidia/nvidia_installer.log** or **/usr/local/nvidia/nvidia-installer.log** directory.

   View standard output logs of the NVIDIA container.

   Filter the container ID by running the following command:

   .. code-block::

      crictl ps -a | grep nvidia

   View logs by running the following command:

   .. code-block::

      crictl logs Container ID

What Should I Do If the NVIDIA Version Reported by a Service and the CUDA Version Do Not Match?
-----------------------------------------------------------------------------------------------

#. Check the CUDA version in the service container (preferentially using the official CUDA query method):

   .. code-block::

      cat /usr/local/cuda/version.txt

#. Check whether the CUDA version inside the container is included in the range of CUDA versions supported by the NVIDIA driver on the host node. Run **nvidia-smi** to check the maximum CUDA version supported by the driver on the node.

Helpful Links
-------------

:ref:`What Should I Do If an Error Occurs When I Deploy a Service on a GPU Node? <cce_faq_00109>`

.. |image1| image:: /_static/images/en-us_image_0000002662011331.png
