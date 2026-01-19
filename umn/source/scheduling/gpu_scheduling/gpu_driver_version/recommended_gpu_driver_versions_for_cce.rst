:original_name: cce_10_0847.html

.. _cce_10_0847:

Recommended GPU Driver Versions for CCE
=======================================

This section describes the recommended driver versions for CCE clusters. If you use a non-recommended GPU driver version, make sure to check its compatibility with the model and OS. Select a proper NVIDIA driver version by checking the compatibility list for CUDA Toolkit and NVIDIA drivers based on the version used by your application.

Supported GPU Drivers
---------------------

.. important::

   -  The list of supported GPU drivers applies only to CCE AI Suite (NVIDIA GPU) of v1.2.28 or later.

   -  To use the latest GPU driver, upgrade your CCE AI Suite (NVIDIA GPU) to the latest version.

   -  The compatibility of the OSs, GPU drivers, and GPU models listed in :ref:`Table 2 <cce_10_0141__table056413288409>` has been tested and verified. To ensure excellent performance and stability, use these GPU drivers. If the deployment is performed in an unverified environment, perform full tests based on the environment to ensure the compatibility and stability.

   -  NVIDIA no longer provides updates or security patches for GPU drivers that have reached their end of life (EOL). For details, see `Driver Lifecycle <https://docs.nvidia.com/datacenter/tesla/drivers/#driver-lifecycle>`__. For example, a Production Branch (PB) provides one-year support from the date of release, and a Long-Term Support Branch (LTSB) provides three-year support.

      According to this policy, CCE does not provide technical support for GPU drivers that have reached EOL, including driver installation and updates. **The following drivers have reached EOL: 510.47.03, 470.141.03, and 470.57.02.**

   -  When installing a GPU driver on Ubuntu and CentOS, pay attention to the OS version. For details, see :ref:`Table 3 <cce_10_0141__table6519152112818>`. For more information, see `NVIDIA Driver Documentation <https://docs.nvidia.com/datacenter/tesla/>`__.

.. table:: **Table 1** Supported GPU drivers

   +------------+------------------------+---------------+------------+--------------+---------------------+
   | GPU Model  | Supported Cluster Type | Specification | OS         | OS           | OS                  |
   +============+========================+===============+============+==============+=====================+
   |            |                        |               | HCE OS 2.0 | Ubuntu 22.04 | EulerOS release 2.9 |
   +------------+------------------------+---------------+------------+--------------+---------------------+
   | Tesla T4   | CCE standard cluster   | g6            | 570.86.15  | 570.86.15    | 535.54.03           |
   |            |                        |               |            |              |                     |
   |            |                        | pi2           | 535.216.03 | 535.216.03   | 470.141.03          |
   |            |                        |               |            |              |                     |
   |            |                        |               | 535.161.08 | 535.161.08   |                     |
   |            |                        |               |            |              |                     |
   |            |                        |               | 535.54.03  | 535.54.03    |                     |
   |            |                        |               |            |              |                     |
   |            |                        |               | 510.47.03  |              |                     |
   |            |                        |               |            |              |                     |
   |            |                        |               | 470.57.02  |              |                     |
   +------------+------------------------+---------------+------------+--------------+---------------------+
   | Tesla V100 | CCE standard cluster   | p2s           | 570.86.15  | 570.86.15    | 535.54.03           |
   |            |                        |               |            |              |                     |
   |            |                        | p2vs          | 535.216.03 | 535.216.03   | 470.141.03          |
   |            |                        |               |            |              |                     |
   |            |                        | p2v           | 535.161.08 | 535.161.08   |                     |
   |            |                        |               |            |              |                     |
   |            |                        |               | 535.54.03  | 535.54.03    |                     |
   |            |                        |               |            |              |                     |
   |            |                        |               | 510.47.03  |              |                     |
   |            |                        |               |            |              |                     |
   |            |                        |               | 470.57.02  |              |                     |
   +------------+------------------------+---------------+------------+--------------+---------------------+

Helpful Links
-------------

-  `Official NVIDIA drivers <https://www.nvidia.com/Download/index.aspx>`__
-  `Version Mapping of Mainstream Tesla Series Drivers <https://docs.nvidia.com/datacenter/tesla/index.html>`__
