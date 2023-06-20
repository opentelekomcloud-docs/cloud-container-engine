:original_name: cce_10_0316.html

.. _cce_10_0316:

Overview
========

CCE allows you to mount a volume created from a Scalable File Service (SFS) file system to a container to store data persistently. SFS volumes are commonly used in ReadWriteMany scenarios, such as media processing, content management, big data analysis, and workload process analysis.


.. figure:: /_static/images/en-us_image_0000001568822709.png
   :alt: **Figure 1** Mounting SFS volumes to CCE

   **Figure 1** Mounting SFS volumes to CCE

Description
-----------

-  **Standard file protocols**: You can mount file systems as volumes to servers, the same as using local directories.
-  **Data sharing**: The same file system can be mounted to multiple servers, so that data can be shared.
-  **Private network**: User can access data only in private networks of data centers.
-  **Capacity and performance**: The capacity of a single file system is high (PB level) and the performance is excellent (ms-level read/write I/O latency).
-  **Use cases**: Deployments/StatefulSets in the ReadWriteMany mode and jobs created for high-performance computing (HPC), media processing, content management, web services, big data analysis, and workload process analysis
