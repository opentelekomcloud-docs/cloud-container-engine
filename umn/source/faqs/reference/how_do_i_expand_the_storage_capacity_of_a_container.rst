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

   |image1|

   **Storage Settings**: Click **Expand** next to the data disk to set the following parameters:

   -  **Allocate Disk Space**: storage space used by the container engine to store the Docker/containerd working directory, container image data, and image metadata. Defaults to 90% of the data disk.
   -  **Allocate Pod Basesize**: CCE allows you to set an upper limit for the disk space occupied by each workload pod (including the space occupied by container images). This setting prevents the pods from taking all the disk space available, which may cause service exceptions. It is recommended that the value be smaller than or equal to 80% of the container engine space.

      .. note::

         -  The capability of customizing pod basesize is related to the node OS and container storage rootfs.

            -  When the rootfs uses Device Mapper, the node supports custom pod basesize. The default storage space of a single container is 10 GiB.

            -  When the rootfs uses OverlayFS, most nodes do not support custom pod basesize. The storage space of a single container is not limited and defaults to the container engine space.

               Only EulerOS 2.9 nodes in clusters of 1.19.16, 1.21.3, 1.23.3, and later versions support custom pod basesize.

         -  In the case of using Docker on EulerOS 2.9 nodes, **basesize** will not take effect if **CAP_SYS_RESOURCE** or **privileged** is configured for a container.

#. After the node is reset, log in to the node and run the following command to access the container and check whether the container storage capacity has been expanded:

   **docker exec -it** *container_id* **/bin/sh** or **kubectl exec -it** *container_id* **/bin/sh**

   **df -h**

   |image2|

.. |image1| image:: /_static/images/en-us_image_0000001797909113.png
.. |image2| image:: /_static/images/en-us_image_0000001797870097.png
