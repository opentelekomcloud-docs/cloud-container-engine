:original_name: cce_10_0777.html

.. _cce_10_0777:

DRF
===

Dominant Resource Fairness (DRF) is a scheduling algorithm based on the dominant resource of a container group. DRF scheduling can be used to enhance the service throughput of a cluster, shorten the overall service execution time, and improve service running performance. It is suitable for batch AI training and big data jobs.

Prerequisites
-------------

-  A cluster of v1.19 or later is available. For details, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.
-  The Volcano add-on has been installed. For details, see :ref:`Volcano Scheduler <cce_10_0193>`.

How It Works
------------

In actual services, limited cluster resources are often allocated to multiple users. Each user has the same rights to obtain resources, but the number of resources they need may be different. It is crucial to fairly allocate resources to each user. A common scheduling algorithm is the max-min fairness share, which allocates resources to meet users' minimum requirements as far as possible and then fairly allocates the remaining resources. The rules are as follows:

#. Resources are allocated in order of increasing demand.
#. No source gets a resource share larger than its demand.
#. Sources with unsatisfied demands get an equal share of the resource.

The max-min fairness algorithm applies to the single resource scenario, where all jobs are requesting the same resources. However, in actual situations, multiple resources are involved. For example, CPU, memory, and GPU resources are requested for allocation. DRF can be used to resolve the preceding issue. DRF can be considered as a general version of the max-min fairness algorithm and supports fair allocation of multiple types of resources so that the dominant resource of each user meets the max-min fairness requirement.

The share value of each job resource is calculated using the following formula:

**Share = Total requested resources/Cluster resources**

If a job involves multiple resources, the resource with the largest share value is the dominant resource. The share value of the dominant resource will be used in priority-based scheduling.

For example, there are two workloads, job 1 and job 2. The following figure shows the resources requested by the two jobs. After DRF calculation, the dominant resource of job 1 is memory, and its share value is 0.4; the dominant resource of job 2 is CPU, and its share value is 0.5. Since the dominant resource share of job 1 is less than that of job 2, job 1 takes precedence over job 2 in scheduling according to the max-min fairness policy.


.. figure:: /_static/images/en-us_image_0000001950317396.png
   :alt: **Figure 1** DRF scheduling

   **Figure 1** DRF scheduling

Configuring DRF
---------------

After Volcano is installed, you can enable or disable DRF scheduling on the **Scheduling** page. This function is enabled by default.

#. Log in to the CCE console.

#. Click the cluster name to access the cluster console. Choose **Settings** in the navigation pane. In the right pane, click the **Scheduling** tab.

#. In the **AI task performance enhanced scheduling** pane, select whether to enable DRF.

   This function helps you enhance the service throughput of the cluster and improve service running performance.

#. Click **Confirm**.
