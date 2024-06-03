:original_name: cce_faq_00020.html

.. _cce_faq_00020:

How Do I Rectify Failures When the NVIDIA Driver Is Used to Start Containers on GPU Nodes?
==========================================================================================

Did a Resource Scheduling Failure Event Occur on a Cluster Node?
----------------------------------------------------------------

**Symptom**

A node is running properly and has GPU resources. However, the following error information is displayed:

0/9 nodes are available: 9 insufficient nvidia.com/gpu

**Analysis**

#. Check whether the node is attached with NVIDIA label.

   |image1|

#. Check whether the NVIDIA driver is running properly.

   Log in to the node where the add-on is running and view the driver installation log in the following path:

   .. code-block::

      /opt/cloud/cce/nvidia/nvidia_installer.log

   View standard output logs of the NVIDIA container.

   Filter the container ID by running the following command:

   .. code-block::

      docker ps -a | grep nvidia

   View logs by running the following command:

   .. code-block::

      docker logs Container ID

What Should I Do If the NVIDIA Version Reported by a Service and the CUDA Version Do Not Match?
-----------------------------------------------------------------------------------------------

Run the following command to check the CUDA version in the container:

.. code-block::

   cat /usr/local/cuda/version.txt

Check whether the CUDA version supported by the NVIDIA driver version of the node where the container is located contains the CUDA version of the container.

Helpful Links
-------------

:ref:`What Should I Do If an Error Occurs When Deploying a Service on the GPU Node? <cce_faq_00109>`

.. |image1| image:: /_static/images/en-us_image_0000001898023841.png
