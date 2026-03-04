:original_name: cce_10_0503.html

.. _cce_10_0503:

CCE AI Suite (NVIDIA GPU) Exceptions
====================================

Check Items
-----------

Check whether CCE AI Suite (NVIDIA GPU) involved in the upgrade affects the GPU driver installation when creating a GPU node.

Solutions
---------

The driver of CCE AI Suite (NVIDIA GPU) needs to be configured by yourself. Check the compatibility between this add-on and the GPU driver. It is a good practice to verify the upgrade of the GPU driver to the target version in the test environment, configure the current GPU driver, and check whether the created GPU node can run properly.

Perform the following operations to check the target GPU driver version and the current driver configuration of CCE AI Suite (NVIDIA GPU):

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. Then, check the CCE AI Suite (NVIDIA GPU) add-on.

#. Click **Upgrade** of the add-on and check the target version and driver version of the add-on.

#. Verify the upgraded GPU driver version in the test environment, configure the current GPU driver, and check whether the created GPU node can run properly.

   If the GPU add-on and the GPU driver are incompatible, install the driver of a later version. If necessary, contact technical support.
