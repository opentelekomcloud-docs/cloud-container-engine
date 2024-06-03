:original_name: cce_faq_00224.html

.. _cce_faq_00224:

How Do I Expand the Storage Capacity of a Container?
====================================================

Application Scenarios
---------------------

The default storage size of a container is 10 GB. If a large volume of data is generated in the container, expand the capacity using the method described in this topic.

Solution
--------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Nodes** from the navigation pane.

#. Click the Nodes tab, locate the row containing the target node, and choose **More** > **Reset Node** in the **Operation** column.

   .. important::

      Resetting a node may make unavailable the node-specific resources (such as local storage and workloads scheduled to this node). Exercise caution when performing this operation to avoid impact on running services.

#. Click **Yes**.

#. Reconfigure node parameters.

   If you need to adjust the container storage space, pay attention to the following configurations:

   **Storage Settings**: Click **Expand** next to the data disk to set the following parameters:

   **Space Allocation for Pods**: indicates the base size of a pod. It is the maximum size that a workload's pods (including the container images) can grow to in the disk space. Proper settings can prevent pods from taking all the disk space available and avoid service exceptions. It is recommended that the value is less than or equal to 80% of the container engine space. This parameter is related to the node OS and container storage rootfs and is not supported in some scenarios. For details, see :ref:`Data Disk Space Allocation <cce_10_0341>`.

#. After the node is reset, log in to the node and run the following command to access the container and check whether the container storage capacity has been expanded:

   **docker exec -it** *container_id* **/bin/sh** or **kubectl exec -it** *container_id* **/bin/sh**

   **df -h**

   |image1|

.. |image1| image:: /_static/images/en-us_image_0000001851585112.png
