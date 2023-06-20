:original_name: cce_10_0445.html

.. _cce_10_0445:

Node Disk
=========

Check Item
----------

Check the following aspects:

-  Check whether the key data disks on the node meet the upgrade requirements.
-  Check whether the **/tmp** directory has 500 MB available space.

Solution
--------

During the node upgrade, the key disks store the upgrade component package, and the **/tmp** directory stores temporary files.

-  **Scenario 1: Check whether the disk meets the upgrade requirements.**

   Run the following command to check the usage of each key disk. After ensuring that the available space meets the requirements and check again. If the space of the master node is insufficient, contact technical support.

   -  Disk partition of Docker: 2 GB for master nodes and 1 GB for worker nodes

      .. code-block::

         df -h /var/lib/docker

   -  Disk partition of containerd: 2 GB for master nodes and 1 GB for worker nodes

      .. code-block::

         df -h /var/lib/containerd

   -  Disk partition of kubelet: 2 GB for master nodes and 1 GB for worker nodes

      .. code-block::

         df -h /var/lib/docker

   -  System disk: 10 GB for master nodes and 2 GB for worker nodes

      .. code-block::

         df -h /

-  **Scenario 2: The /tmp directory space is insufficient.**

   Run the following command to check the space usage of the file system where the /tmp directory is located. Ensure that the space is greater than 500 MB and check again.

   .. code-block::

      df -h /tmp
