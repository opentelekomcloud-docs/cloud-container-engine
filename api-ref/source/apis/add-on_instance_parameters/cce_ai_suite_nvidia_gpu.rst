:original_name: cce_02_0415.html

.. _cce_02_0415:

CCE AI Suite (NVIDIA GPU)
=========================

Add-on Overview
---------------

CCE AI Suite (NVIDIA GPU) is a device management add-on that supports GPUs in containers. To use GPU nodes in a cluster, this add-on must be installed.

Add-on Parameters
-----------------

.. table:: **Table 1** Parameters

   +-----------+-----------+----------------------------------------------------------------------------------------+---------------------------------------+
   | Parameter | Mandatory | Type                                                                                   | Description                           |
   +===========+===========+========================================================================================+=======================================+
   | basic     | Yes       | object                                                                                 | Basic add-on configuration parameters |
   +-----------+-----------+----------------------------------------------------------------------------------------+---------------------------------------+
   | custom    | Yes       | :ref:`Table 3 <cce_02_0415__en-us_topic_0000001788047466_table109631116113412>` object | Custom parameters                     |
   +-----------+-----------+----------------------------------------------------------------------------------------+---------------------------------------+

.. table:: **Table 2** Configuration of basic

   +-----------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------+
   | Parameter       | Mandatory | Type   | Description                                                                                                             |
   +=================+===========+========+=========================================================================================================================+
   | cluster_version | No        | String | CCE cluster version                                                                                                     |
   +-----------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------+
   | device_version  | Yes       | String | Add-on version                                                                                                          |
   +-----------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------+
   | driver_version  | Yes       | String | Image tag of an add-on pod where a driver is installed. Generally, the value is the same as that of **device_version**. |
   +-----------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------+
   | obs_url         | Yes       | String | When a GPU driver is downloaded from the default driver address, the value is the GPU driver address.                   |
   +-----------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------+
   | swr_addr        | Yes       | String | Image repository address                                                                                                |
   +-----------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------+
   | swr_user        | Yes       | String | Tenant path of an image repository                                                                                      |
   +-----------------+-----------+--------+-------------------------------------------------------------------------------------------------------------------------+

.. _cce_02_0415__en-us_topic_0000001788047466_table109631116113412:

.. table:: **Table 3** Configuration of custom

   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | Parameter                  | Mandatory       | Type            | Description                                                                                                     |
   +============================+=================+=================+=================================================================================================================+
   | compatible_with_legacy_api | No              | Bool            | API compatibility switch                                                                                        |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | Default value: **false**                                                                                        |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | **true**: The add-on supports the GPU native mode and xGPU virtualization.                                      |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | component_schedulername    | Yes             | String          | Name of the scheduler used by the add-on.                                                                       |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | Default value: **default-scheduler**                                                                            |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | disable_mount_path_v1      | No              | Bool            | Default value: **false**                                                                                        |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | **true**: **/opt/cloud/cce/nvidia** is not mounted to the **/usr/lib/nvidia** directory of a GPU container.     |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | disable_nvidia_gsp         | No              | Bool            | Default value: **true**                                                                                         |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | **true**: The GPU GSP firmware is disabled.                                                                     |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | driver_mount_paths         | No              | String          | Driver file directory that needs to be automatically mounted to a GPU container                                 |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | Default value: **"bin,lib64"**                                                                                  |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | enable_fault_isolation     | No              | Bool            | Default value: **true**                                                                                         |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | **true**: The add-on detects hardware faults or driver issues of a GPU and then sets the GPU to be unavailable. |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | enable_health_monitoring   | No              | Bool            | Default value: **true**                                                                                         |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | **true**: The add-on detects hardware faults or driver issues of a GPU.                                         |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | enable_metrics_monitoring  | No              | Bool            | Default value: **true**                                                                                         |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | **true**: The add-on collects GPU metrics and reports these metrics to Prometheus.                              |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | enable_simple_lib64_mount  | No              | Bool            | Default value: **true**                                                                                         |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | **true**: Only the **libxxx.so.x** file is mounted to a container.                                              |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | enable_xgpu                | No              | Bool            | Default value: **false**                                                                                        |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | Whether to enable xGPU virtualization.                                                                          |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | gpu_driver_config          | No              | Map             | Configurations of the GPU driver for a single node pool                                                         |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | Default value: **{}**                                                                                           |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | health_check_xids_v2       | No              | String          | GPU error range for the add-on health checks                                                                    |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | Default value: **"74,79"**                                                                                      |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | inject_ld_Library_path     | No              | String          | Value of the **LD_LIBRARY_PATH** environment variable automatically injected by the add-on to a GPU container   |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | Default value: **""**                                                                                           |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | lib64_container_paths      | No              | String          | Mount path of NVIDIA lib64 in a GPU container                                                                   |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | Default value: **"/usr/lib64,/usr/lib/x86_64-linux-gnu"**                                                       |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | metrics_delete_interval    | No              | int             | Timeout threshold for deleting a metric when the metric cannot be obtained. The unit is millisecond.            |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | Default value: **30000**                                                                                        |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | metrics_monitor_interval   | No              | int             | Interval for obtaining metrics, in milliseconds.                                                                |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | Default value: **15000**                                                                                        |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+
   | nvidia_driver_download_url | Yes             | String          | Path for downloading the NVIDIA driver                                                                          |
   |                            |                 |                 |                                                                                                                 |
   |                            |                 |                 | Default value: **""**                                                                                           |
   +----------------------------+-----------------+-----------------+-----------------------------------------------------------------------------------------------------------------+

Example Request
---------------

.. code-block::

   {
     "kind": "Addon",
     "apiVersion": "v3",
     "metadata": {
       "name": "gpu-beta",
     },
     "spec": {
       "clusterID": "80c9e306-***-***-***-0255ac100043",
       "version": "2.0.69",
       "addonTemplateName": "gpu-beta",
       "values": {
         "basic": {
           "cluster_version": "v1.27",
           "device_version": "2.0.69",
           "driver_version": "2.0.69",
           "obs_url": "***",
           "region": "***",
           "swr_addr": "***",
           "swr_user": "***"
         },
         "custom": {
           "compatible_with_legacy_api": true,
           "component_schedulername": "kube-scheduler",
           "disable_mount_path_v1": false,
           "disable_nvidia_gsp": true,
           "driver_mount_paths": "bin,lib64",
           "enable_fault_isolation": true,
           "enable_health_monitoring": true,
           "enable_metrics_monitoring": true,
           "enable_simple_lib64_mount": true,
           "enable_xgpu": true,
           "gpu_driver_config": {},
           "health_check_xids_v2": "74,79",
           "inject_ld_Library_path": "",
           "lib64_container_paths": "/usr/lib64,/usr/lib/x86_64-linux-gnu",
           "metrics_delete_interval": 30000,
           "metrics_monitor_interval": 15000,
           "nvidia_driver_download_url": ""
         },
       }
     }
   }
