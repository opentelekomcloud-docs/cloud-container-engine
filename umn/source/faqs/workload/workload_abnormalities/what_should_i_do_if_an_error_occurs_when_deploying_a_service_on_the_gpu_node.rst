:original_name: cce_faq_00109.html

.. _cce_faq_00109:

What Should I Do If an Error Occurs When Deploying a Service on the GPU Node?
=============================================================================

Symptom
-------

The following exceptions occur when services are deployed on the GPU nodes in a CCE cluster:

#. The GPU memory of containers cannot be queried.
#. Seven GPU services are deployed, but only two of them can be accessed properly. Errors are reported during the startup of the remaining five services.

   -  The CUDA versions of the two services that can be accessed properly are 10.1 and 10.0, respectively.
   -  The CUDA versions of the failing services are also 10.0 and 10.1.

#. Files named **core.\*** are found in the GPU service containers. No such files existed in any of the previous deployments.

Fault Locating
--------------

#. The driver version of the gpu add-on is too old. After a new driver is downloaded and installed, the fault is rectified.
#. The workloads do not declare that GPU resources are required.

Suggested Solution
------------------

After you install gpu-beta (gpu-device-plugin) on a node, nvidia-smi will be automatically installed. If an error is reported during GPU deployment, this issue is typically caused by an NVIDIA driver installation failure. Check whether the NVIDIA driver has been downloaded.

-  GPU node:

   .. code-block::

      # If the add-on version is earlier than 2.0.0, run the following command:
      cd /opt/cloud/cce/nvidia/bin && ./nvidia-smi

      # If the add-on version is 2.0.0 or later and the driver installation path is changed, run the following command:
      cd /usr/local/nvidia/bin && ./nvidia-smi

-  Container:

   .. code-block::

      cd /usr/local/nvidia/bin && ./nvidia-smi

If GPU information is returned, the device is available and the add-on has been installed.

If the driver address is incorrect, uninstall the add-on, reinstall it, and configure the correct address.

.. note::

   You are advised to store the NVIDIA driver in the OBS bucket and set the bucket policy to public read.

Helpful Links
-------------

-  :ref:`How Do I Rectify Failures When the NVIDIA Driver Is Used to Start Containers on GPU Nodes? <cce_faq_00020>`
