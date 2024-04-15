:original_name: cce_10_0511.html

.. _cce_10_0511:

Key Parameters of the GPU Add-on
================================

Check Items
-----------

Check whether some configurations of the CCE AI Suite add-on installed in a cluster are intrusively modified. If yes, the upgrade may fail.

Solutions
---------

#. Use kubectl to access the cluster.

#. Run the following command to obtain the add-on instance details:

   .. code-block::

      kubectl get ds nvidia-driver-installer -nkube-system -oyaml

#. Check whether the value of **UpdateStrategy** is changed to **OnDelete**. If yes, change it to **RollingUpdate**.

#. Check whether the value of **NVIDIA_DRIVER_DOWNLOAD_URL** is the same as the add-on IP address on the add-on details page. If no, change the value on the web page.
