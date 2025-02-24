:original_name: cce_10_0503.html

.. _cce_10_0503:

GPU Add-on
==========

Check Items
-----------

The GPU add-on is involved in the upgrade, which may affect the GPU driver installation during the creation of a GPU node.

Solution
--------

The GPU add-on driver needs to be configured by yourself. Check the compatibility between the GPU add-on and the GPU driver. It is a good practice to verify the upgrade of the GPU driver to the target version in the test environment, configure the current GPU driver, and check whether the created GPU node can run properly.

Perform the following operations to check the upgrade of the GPU driver to the target version and current driver configuration of GPU add-on:

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. Then, check the CCE AI Suite (NVIDIA GPU) add-on.

#. Click **Upgrade** of the add-on and check the target version and driver version of the add-on.

#. Verify the upgrade of the GPU driver to the target version in the test environment, configure the current GPU driver, and check whether the created GPU node can run properly.

   If the GPU add-on and the GPU driver are incompatible, install the driver of a later version. If necessary, contact technical support.
