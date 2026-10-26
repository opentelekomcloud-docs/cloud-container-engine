:original_name: cce_faq_00429.html

.. _cce_faq_00429:

How Do I Configure Add-on Resource Quotas Based on Cluster Scale?
=================================================================

After changing the cluster scale, adjust the add-on resource quotas based on the cluster scale to ensure that the add-on pods can run properly. For example, if you expand the cluster scale from 50 worker nodes to 200 worker nodes or more, increase the CPU and memory quotas of the add-on pods to avoid exceptions such as OOM caused by too many nodes required for scheduling the add-on pods.

Configuring Resource Quotas for CoreDNS
---------------------------------------

Queries per Second (QPS) of the CoreDNS add-on is positively correlated with the CPU consumption. If the number of nodes or containers in the cluster grows, the CoreDNS pod will bear heavier workloads. Adjust the number of add-on pods and their CPU and memory quotas based on the cluster scale.

.. table:: **Table 1** Recommended values for CoreDNS

   +-------+---------------------------------+------+-----------------+---------------+----------------------+--------------------+
   | Nodes | Recommended Configuration (QPS) | Pods | CPU Request (m) | CPU Limit (m) | Memory Request (MiB) | Memory Limit (MiB) |
   +=======+=================================+======+=================+===============+======================+====================+
   | 50    | 2500                            | 2    | 500             | 500           | 512                  | 512                |
   +-------+---------------------------------+------+-----------------+---------------+----------------------+--------------------+
   | 200   | 5000                            | 2    | 1000            | 1000          | 1024                 | 1024               |
   +-------+---------------------------------+------+-----------------+---------------+----------------------+--------------------+
   | 1000  | 10000                           | 2    | 2000            | 2000          | 2048                 | 2048               |
   +-------+---------------------------------+------+-----------------+---------------+----------------------+--------------------+
   | 2000  | 20000                           | 4    | 2000            | 2000          | 2048                 | 2048               |
   +-------+---------------------------------+------+-----------------+---------------+----------------------+--------------------+

Configuring Resource Quotas for CCE Container Storage (Everest)
---------------------------------------------------------------

After the cluster scale is adjusted, the everest specifications need to be modified based on the cluster scale and the number of PVCs. The requested CPU and memory can be increased based on the number of nodes and PVCs. For details, see :ref:`Table 2 <cce_faq_00429__table1764221411617>`.

In non-typical scenarios, the formulas for estimating the limit values are as follows:

-  everest-csi-controller

   -  CPU limit: 250m for 200 or fewer nodes, 350m for 1000 nodes, and 500m for 2000 nodes
   -  Memory limit = (200 MiB + Number of nodes x 1 MiB + Number of PVCs x 0.2 MiB) x 1.2

-  everest-csi-driver

   -  CPU limit: 300m for 200 or fewer nodes, 500m for 1000 nodes, and 800m for 2000 nodes
   -  Memory limit: 300 MiB for 200 or fewer nodes, 600 MiB for 1000 nodes, and 900 MiB for 2000 nodes

.. _cce_faq_00429__table1764221411617:

.. table:: **Table 2** Recommended configuration limits in typical scenarios

   +-------+----------+-------------+----------------------------------+-------------------------------------+------------------------------+---------------------------------+
   | Nodes | PVs/PVCs | Add-on Pods | everest-csi-controller CPU Limit | everest-csi-controller Memory Limit | everest-csi-driver CPU Limit | everest-csi-driver Memory Limit |
   +=======+==========+=============+==================================+=====================================+==============================+=================================+
   | 50    | 1000     | 2           | 250m                             | 600 MiB                             | 300m                         | 300 MiB                         |
   +-------+----------+-------------+----------------------------------+-------------------------------------+------------------------------+---------------------------------+
   | 200   | 1000     | 2           | 250m                             | 1 GiB                               | 300m                         | 300 MiB                         |
   +-------+----------+-------------+----------------------------------+-------------------------------------+------------------------------+---------------------------------+
   | 1000  | 1000     | 2           | 350m                             | 2 GiB                               | 500m                         | 600 MiB                         |
   +-------+----------+-------------+----------------------------------+-------------------------------------+------------------------------+---------------------------------+
   | 1000  | 5000     | 2           | 450m                             | 3 GiB                               | 500m                         | 600 MiB                         |
   +-------+----------+-------------+----------------------------------+-------------------------------------+------------------------------+---------------------------------+
   | 2000  | 5000     | 2           | 550m                             | 4 GiB                               | 800m                         | 900 MiB                         |
   +-------+----------+-------------+----------------------------------+-------------------------------------+------------------------------+---------------------------------+
   | 2000  | 10000    | 2           | 650m                             | 5 GiB                               | 800m                         | 900 MiB                         |
   +-------+----------+-------------+----------------------------------+-------------------------------------+------------------------------+---------------------------------+

.. note::

   In the table, the requests and limits for CPU and memory are the same.

Configuring Resource Quotas for Autoscaler
------------------------------------------

Autoscaler automatically adjusts the number of nodes in a cluster based on workloads. Adjust the number of add-on pods and their CPU and memory quotas based on the cluster scale.

.. table:: **Table 3** Recommended values for Autoscaler

   +-------+------+-----------------+---------------+----------------------+--------------------+
   | Nodes | Pods | CPU Request (m) | CPU Limit (m) | Memory Request (MiB) | Memory Limit (MiB) |
   +=======+======+=================+===============+======================+====================+
   | 50    | 2    | 1000            | 1000          | 1000                 | 1000               |
   +-------+------+-----------------+---------------+----------------------+--------------------+
   | 200   | 2    | 4000            | 4000          | 2000                 | 2000               |
   +-------+------+-----------------+---------------+----------------------+--------------------+
   | 1000  | 2    | 8000            | 8000          | 8000                 | 8000               |
   +-------+------+-----------------+---------------+----------------------+--------------------+
   | 2000  | 2    | 8000            | 8000          | 8000                 | 8000               |
   +-------+------+-----------------+---------------+----------------------+--------------------+

Configuring Resource Quotas for Volcano
---------------------------------------

After the cluster scale is increased, the resource quotas required by Volcano need to be modified based on the cluster scale.

-  If the number of nodes is no more than 100, retain the default configuration. The requested vCPUs are 500m, and the limit is 2,000m. The requested memory is 500 MiB, and the limit is 2,500 MiB.
-  If the number of nodes is greater than 100 and no more than 1,000, increase the requested vCPUs by 500m and the requested memory by 1,000 MiB each time 100 nodes (10,000 pods) are added. Set the vCPU limit to 1,500m higher than the requested vCPUs, and the memory limit to 6,000 MiB higher than the requested memory.
-  If the number of nodes is greater than 1,000, see the recommended calculation formula for details about the requested value. It is recommended that the memory limit be 10,000 MiB higher than the requested value.

   .. note::

      Formulas for calculating the requests:

      -  CPU request: Calculate the number of nodes multiplied by the number of pods, perform interpolation search using the product of the number of nodes in the cluster multiplied by the number of pods in :ref:`Table 4 <cce_faq_00429__table1518116718431>`, and round up the request and limit which are closest to the specifications.

         For example, for 2,000 nodes and 20,000 pods, the requested vCPUs are 40 million (Number of target nodes x Number of target pods = 40 million), which is close to the specification of 700/70,000 (Number of cluster nodes x Number of pods = 49 million). According to the table below, set the requested vCPUs to 4,000m and the limit to 5,500m.

      -  Requested memory: It is recommended that 2.4 GiB memory be allocated to every 1,000 nodes and 1.4 GiB memory be allocated to every 10,000 pods. The requested memory is the sum of these two values. (The obtained value may be different from the recommended value in :ref:`Table 4 <cce_faq_00429__table1518116718431>`. You can use either of them.)

         Requested memory = Number of target nodes/1000 x 2.4 GiB + Number of target pods/10,000 x 1.4 GiB

         For example, for 2,000 nodes and 20,000 pods, the requested memory is 7.6 GiB (2 x 2.4 GiB + 2 x 1.4 GiB).

.. _cce_faq_00429__table1518116718431:

.. table:: **Table 4** Recommended resource requests and limits for volcano-controller and volcano-scheduler

   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | Nodes/Pods in a Cluster | CPU Request (m) | CPU Limit (m) | Memory Request (MiB) | Memory Limit (MiB) |
   +=========================+=================+===============+======================+====================+
   | 50/5000                 | 500             | 2000          | 500                  | 2500               |
   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | 100/10000               | 1000            | 2500          | 1500                 | 3500               |
   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | 200/20000               | 1500            | 3000          | 2500                 | 8500               |
   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | 300/30000               | 2000            | 3500          | 3500                 | 9500               |
   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | 400/40000               | 2500            | 4000          | 4500                 | 10500              |
   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | 500/50000               | 3000            | 4500          | 5500                 | 11500              |
   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | 600/60000               | 3500            | 5000          | 6500                 | 12500              |
   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | 700/70000               | 4000            | 5500          | 7500                 | 13500              |
   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | 800/8w                  | 4500            | 6000          | 8500                 | 14500              |
   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | 900/9w                  | 5000            | 6500          | 9500                 | 15500              |
   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | 1000/10w                | 5500            | 7000          | 10500                | 16500              |
   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | 1100/11w                | 6000            | 7500          | 18500                | 28500              |
   +-------------------------+-----------------+---------------+----------------------+--------------------+
   | 1200/12w                | 6500            | 8000          | 20000                | 30000              |
   +-------------------------+-----------------+---------------+----------------------+--------------------+

Configuring Resource Quotas for Other Add-ons
---------------------------------------------

Resource quotas of other add-ons may also be insufficient due to cluster scale expansion. If, for example, the CPU or memory usage of the add-on pods increases and even OOM occurs, modify the resource quotas as required.

For example, the resources occupied by the Cloud Native Cluster Monitoring add-on are related to the number of pods in the cluster. If the cluster scale is expanded, the number of pods may also grow. In this case, increase the resource quotas of the add-on pods.
