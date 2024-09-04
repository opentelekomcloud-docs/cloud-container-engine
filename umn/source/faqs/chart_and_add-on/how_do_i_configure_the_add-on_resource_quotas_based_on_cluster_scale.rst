:original_name: cce_faq_00429.html

.. _cce_faq_00429:

How Do I Configure the Add-on Resource Quotas Based on Cluster Scale?
=====================================================================

After changing the cluster scale, adjust the add-on resource quotas based on the cluster scale to ensure that the add-on pods can run properly. For example, if you expand the cluster scale from 50 worker nodes to 200 worker nodes or more, increase the CPU and memory quotas of the add-on pods to avoid exceptions such as OOM caused by too many nodes required for scheduling the add-on pods.

Configuring Resource Quotas for coredns
---------------------------------------

Queries per Second (QPS) of the coredns add-on is positively correlated with the CPU consumption. If the number of nodes or containers in the cluster grows, the coredns pod will bear heavier workloads. Adjust the number of the coredns pods and their CPU and memory quotas based on the cluster scale.

.. table:: **Table 1** Recommended values for coredns

   +-------+---------------------------+-----+-------------+-----------+----------------+--------------+
   | Node  | Recommended Configuration | Pod | CPU Request | CPU Limit | Memory Request | Memory Limit |
   +=======+===========================+=====+=============+===========+================+==============+
   | 50    | 2500 QPS                  | 2   | 500m        | 500m      | 512Mi          | 512Mi        |
   +-------+---------------------------+-----+-------------+-----------+----------------+--------------+
   | 200   | 5000 QPS                  | 2   | 1000m       | 1000m     | 1024Mi         | 1024Mi       |
   +-------+---------------------------+-----+-------------+-----------+----------------+--------------+
   | 1000  | 10,000 QPS                | 2   | 2000m       | 2000m     | 2048Mi         | 2048Mi       |
   +-------+---------------------------+-----+-------------+-----------+----------------+--------------+
   | 2,000 | 20,000 QPS                | 4   | 2000m       | 2000m     | 2048Mi         | 2048Mi       |
   +-------+---------------------------+-----+-------------+-----------+----------------+--------------+

Configuring Resource Quotas for everest
---------------------------------------

After the cluster scale is adjusted, the everest specifications need to be modified based on the cluster scale and the number of PVCs. The requested CPU and memory can be increased based on the number of nodes and PVCs. For details, see :ref:`Table 2 <cce_faq_00429__table1764221411617>`.

In non-typical scenarios, the formulas for estimating the limit values are as follows:

-  everest-csi-controller

   -  CPU limit: 250m for 200 or fewer nodes, 350m for 1000 nodes, and 500m for 2000 nodes
   -  Memory limit = (200 Mi + Number of nodes x 1 Mi + Number of PVCs x 0.2 Mi) x 1.2

-  everest-csi-driver

   -  CPU limit: 300m for 200 or fewer nodes, 500m for 1000 nodes, and 800m for 2000 nodes
   -  Memory limit: 300 Mi for 200 or fewer nodes, 600 Mi for 1000 nodes, and 900 Mi for 2000 nodes

.. _cce_faq_00429__table1764221411617:

.. table:: **Table 2** Recommended configuration limits in typical scenarios

   +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
   | Configuration Scenario |          |                  | everest-csi-controller    |                            | everest-csi-driver        |                            |
   +========================+==========+==================+===========================+============================+===========================+============================+
   | Nodes                  | PVs/PVCs | Add-on Instances | vCPUs (Limit = Requested) | Memory (Limit = Requested) | vCPUs (Limit = Requested) | Memory (Limit = Requested) |
   +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
   | 50                     | 1000     | 2                | 250m                      | 600 MiB                    | 300m                      | 300 MiB                    |
   +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
   | 200                    | 1000     | 2                | 250m                      | 1 GiB                      | 300m                      | 300 MiB                    |
   +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
   | 1000                   | 1000     | 2                | 350m                      | 2 GiB                      | 500m                      | 600 MiB                    |
   +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
   | 1000                   | 5000     | 2                | 450m                      | 3 GiB                      | 500m                      | 600 MiB                    |
   +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
   | 2000                   | 5000     | 2                | 550m                      | 4 GiB                      | 800m                      | 900 MiB                    |
   +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+
   | 2000                   | 10000    | 2                | 650m                      | 5 GiB                      | 800m                      | 900 MiB                    |
   +------------------------+----------+------------------+---------------------------+----------------------------+---------------------------+----------------------------+

Configuring Resource Quotas for autoscaler
------------------------------------------

autoscaler automatically adjusts the number of nodes in a cluster based on workloads. Adjust the number of add-on pods and their CPU and memory quotas based on the cluster scale.

.. table:: **Table 3** Recommended values for autoscaler

   ===== === =========== ========= ============== ============
   Node  Pod CPU Request CPU Limit Memory Request Memory Limit
   ===== === =========== ========= ============== ============
   50    2   1000m       1000m     1000Mi         1000Mi
   200   2   4000m       4000m     2000Mi         2000Mi
   1,000 2   8000m       8000m     8000Mi         8000Mi
   2,000 2   8000m       8000m     8000Mi         8000Mi
   ===== === =========== ========= ============== ============

Configuring Resource Quotas for volcano
---------------------------------------

After the cluster scale is increased, the resource quotas required by volcano need to be modified based on the cluster scale.

-  If the number of nodes is less than 100, retain the default configuration. The requested CPU is 500m, and the limit is 2000m. The requested memory is 500 MiB, and the limit is 2000 MiB.
-  If the number of nodes is greater than 100, increase the requested CPU by 500m and the requested memory by 1000 MiB each time 100 nodes (10,000 pods) are added. Increase the CPU limit by 1500m and the memory limit by 1000 MiB.

   .. note::

      Formulas for calculating the requests:

      -  CPU request: Calculate the number of nodes multiplied by the number of pods, perform interpolation search using the product of the number of nodes in the cluster multiplied by the number of pods in :ref:`Table 4 <cce_faq_00429__table1518116718431>`, and round up the request and limit that are closest to the specifications.

         For example, for 2000 nodes (20,000 pods), the product of the number of nodes multiplied by the number of pods is 40 million, which is close to 700/70,000 in the specification (Number of nodes x Number of pods = 49 million). Set the CPU request to 4000m and the limit to 5500m.

      -  Memory request: Allocate 2.4 GiB of memory to every 1000 nodes and 1 GiB of memory to every 10,000 pods. The memory request is the sum of the two values. (The obtained value may be different from the recommended value in :ref:`Table 4 <cce_faq_00429__table1518116718431>`. You can use either of them.)

         Memory request = Number of nodes/1000 x 2.4 GiB + Number of pods/10000 x 1 GiB

         For example, for 2000 nodes and 20,000 pods, the memory request value is 6.8 GiB (2000/1000 x 2.4 GiB + 20000/10000 x 1 GiB).

.. _cce_faq_00429__table1518116718431:

.. table:: **Table 4** Recommended values for volcano-controller and volcano-scheduler

   +-------------------------+---------------------+----------------+------------------------+--------------------+
   | Nodes/Pods in a Cluster | Requested vCPUs (m) | vCPU Limit (m) | Requested Memory (MiB) | Memory Limit (MiB) |
   +=========================+=====================+================+========================+====================+
   | 50/5000                 | 500                 | 2000           | 500                    | 2000               |
   +-------------------------+---------------------+----------------+------------------------+--------------------+
   | 100/10,000              | 1000                | 2500           | 1500                   | 2500               |
   +-------------------------+---------------------+----------------+------------------------+--------------------+
   | 200/20,000              | 1500                | 3000           | 2500                   | 3500               |
   +-------------------------+---------------------+----------------+------------------------+--------------------+
   | 300/30,000              | 2000                | 3500           | 3500                   | 4500               |
   +-------------------------+---------------------+----------------+------------------------+--------------------+
   | 400/40,000              | 2500                | 4000           | 4500                   | 5500               |
   +-------------------------+---------------------+----------------+------------------------+--------------------+
   | 500/50,000              | 3000                | 4500           | 5500                   | 6500               |
   +-------------------------+---------------------+----------------+------------------------+--------------------+
   | 600/60,000              | 3500                | 5000           | 6500                   | 7500               |
   +-------------------------+---------------------+----------------+------------------------+--------------------+
   | 700/70,000              | 4000                | 5500           | 7500                   | 8500               |
   +-------------------------+---------------------+----------------+------------------------+--------------------+

Configuring Resource Quotas for Other Add-ons
---------------------------------------------

Resource quotas of other add-ons may also be insufficient due to cluster scale expansion. If, for example, the CPU or memory usage of the add-on pods increases and even OOM occurs, modify the resource quotas as required.

For example, the resources occupied by the kube-prometheus-stack add-ons are related to the number of pods in the cluster. If the cluster scale is expanded, the number of pods may also grow. In this case, increase the resource quotas of the prometheus pods.
