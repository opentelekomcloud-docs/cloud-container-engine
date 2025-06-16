:original_name: cce_10_0511.html

.. _cce_10_0511:

Key CCE AI Suite (NVIDIA GPU) Parameters
========================================

Check Items
-----------

Check whether the configuration of CCE AI Suite (NVIDIA GPU) in a cluster has been intrusively modified. If so, upgrading the cluster may fail.

Solution
--------

#. Use kubectl to access the cluster.

#. Run the following command to obtain the add-on instance details:

   .. code-block::

      kubectl get ds nvidia-driver-installer -nkube-system -oyaml

#. Check whether the **UpdateStrategy** value is changed to **OnDelete**. If so, change it back to **RollingUpdate**.

#. Check whether the **NVIDIA_DRIVER_DOWNLOAD_URL** value is the same as the GPU driver version on the add-on page. If not, correct the version on the add-on page.
