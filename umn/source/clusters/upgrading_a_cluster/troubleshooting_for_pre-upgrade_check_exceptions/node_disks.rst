:original_name: cce_10_0445.html

.. _cce_10_0445:

Node Disks
==========

Check Items
-----------

Check the following items:

-  Check whether the key data disks on the node meet the upgrade requirements.
-  Check whether the **/tmp** directory has 500 MB available space.

Solution
--------

During the node upgrade, the key disks store the upgrade component package, and the **/tmp** directory stores temporary files.

-  **Scenario 1: Master node disks fail to meet the upgrade requirements.**

   Contact technical support.

-  **Scenario 2: Worker node disks fail to meet the upgrade requirements.**

   Check the usage of each key disk. After ensuring that the available space meets the requirements, check again.

   -  Disk partition of Docker: at least 1 GB of available space

      .. code-block::

         df -h /var/lib/docker

   -  Disk partition of containerd: at least 1 GB of available space

      .. code-block::

         df -h /var/lib/containerd

   -  Disk partition of kubelet: at least 1 GB of available space

      .. code-block::

         df -h /mnt/paas/kubernetes/kubelet

   -  System disk: at least 2 GB of available space

      .. code-block::

         df -h /

-  **Scenario 3: The available space of the /tmp directory on worker nodes is insufficient.**

   Run the following command to check the usage of the file system where the **/tmp** directory is located. Ensure that the space is greater than 500 MB and check again.

   .. code-block::

      df -h /tmp
