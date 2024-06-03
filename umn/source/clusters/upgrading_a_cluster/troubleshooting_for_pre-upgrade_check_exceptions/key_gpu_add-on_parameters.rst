:original_name: cce_10_0511.html

.. _cce_10_0511:

Key GPU Add-on Parameters
=========================

Check Items
-----------

Check whether the configuration of the CCE AI Suite add-on in a cluster has been intrusively modified. If so, upgrading the cluster may fail.

Solution
--------

#. Use kubectl to access the cluster.

#. Run the following command to obtain the add-on instance details:

   .. code-block::

      kubectl get ds nvidia-driver-installer -nkube-system -oyaml

#. Check whether the **UpdateStrategy** value is changed to **OnDelete**. If so, change it back to **RollingUpdate**.

#. Check whether the **NVIDIA_DRIVER_DOWNLOAD_URL** value is the same as the add-on IP address on the add-on details page. If no, change the value on the web page.
