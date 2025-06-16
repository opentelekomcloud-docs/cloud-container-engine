:original_name: cce_faq_00468.html

.. _cce_faq_00468:

How Can I Locate a Fault That Occurs with a Node?
=================================================

Fault Locating
--------------

CCE allows you to locate a node fault using the CCE Node Problem Detector add-on (:ref:`Locating a Node Fault Using the CCE Node Problem Detector Add-on <cce_faq_00468__section892614917315>`). You can also refer to :ref:`Locating a Node Fault by Performing a Self-Check <cce_faq_00468__section88261201143>` to locate the fault.

If the fault persists, submit a service ticket.

.. _cce_faq_00468__section892614917315:

Locating a Node Fault Using the CCE Node Problem Detector Add-on
----------------------------------------------------------------

CCE provides an add-on called CCE Node Problem Detector for you to locate faults occurred with nodes. In 1.16.0 and later versions of this add-on, a large number of check items have been added. They allow you to detect the exceptions of resources and components on nodes and locate the faults.

It is strongly recommended that you install this add-on or upgrade it to **version 1.16.0 or later**.

With this add-on, if an exception occurs with a node, you can view the abnormal metrics on the console.

You can also view the events reported by the add-on in node events and locate the faults based on the events.

.. table:: **Table 1** Fault events

   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | Event                             | Description                                                                                                                                         |
   +===================================+=====================================================================================================================================================+
   | OOMKilling                        | Check whether OOM events occurred and are reported.                                                                                                 |
   |                                   |                                                                                                                                                     |
   |                                   | Handling suggestions: :ref:`Check Item 1: Whether the Node Is Overloaded <cce_faq_00120__section745921416917>`                                      |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | TaskHung                          | Check whether taskHung events occurred and are reported.                                                                                            |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | KernelOops                        | Check the kernel null pointer panic errors.                                                                                                         |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | ConntrackFull                     | Check whether the conntrack table is full.                                                                                                          |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | FrequentKubeletRestart            | Check whether kubelet restarts frequently.                                                                                                          |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | FrequentDockerRestart             | Check whether Docker restarts frequently.                                                                                                           |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | FrequentContainerdRestart         | Check whether containerd restarts frequently.                                                                                                       |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | CRIProblem                        | Check the CRI components.                                                                                                                           |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | KUBELETProblem                    | Check the kubelet.                                                                                                                                  |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | NTPProblem                        | Check the NTP service.                                                                                                                              |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | PIDProblem                        | Check whether PIDs are sufficient.                                                                                                                  |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | FDProblem                         | Check whether file handles are sufficient.                                                                                                          |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | MemoryProblem                     | Check whether the overall node memory is sufficient.                                                                                                |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | CNIProblem                        | Check the CNI components.                                                                                                                           |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | KUBEPROXYProblem                  | Check the kube-proxy.                                                                                                                               |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | ReadonlyFilesystem                | Check whether the **Remount root filesystem read-only** error occurred in the system kernel.                                                        |
   |                                   |                                                                                                                                                     |
   |                                   | Possible cause: The data disks were detached from the ECS by mistake, or the VDB disk of the node has been deleted.                                 |
   |                                   |                                                                                                                                                     |
   |                                   | Handling suggestions:                                                                                                                               |
   |                                   |                                                                                                                                                     |
   |                                   | -  :ref:`Check Item 6: Whether the Disk Is Abnormal <cce_faq_00120__section165209286116>`                                                           |
   |                                   | -  :ref:`Check Item 9: Whether the vdb Disk on the Node Has Been Deleted <cce_faq_00120__section1903800103>`                                        |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | DiskReadonly                      | Check whether the system disk, Docker disk, and kubelet disk are read-only.                                                                         |
   |                                   |                                                                                                                                                     |
   |                                   | Possible cause: The data disks were detached from the ECS by mistake, or the VDB disk of the node has been deleted.                                 |
   |                                   |                                                                                                                                                     |
   |                                   | Handling suggestions:                                                                                                                               |
   |                                   |                                                                                                                                                     |
   |                                   | -  :ref:`Check Item 6: Whether the Disk Is Abnormal <cce_faq_00120__section165209286116>`                                                           |
   |                                   | -  :ref:`Check Item 9: Whether the vdb Disk on the Node Has Been Deleted <cce_faq_00120__section1903800103>`                                        |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | DiskProblem                       | Check the disk usage and whether the key logical disk is properly attached to the node.                                                             |
   |                                   |                                                                                                                                                     |
   |                                   | Check the usage of the system disk, Docker disk, and kubelet disk, and check whether the Docker and kubelet disks are properly attached to the ECS. |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | PIDPressure                       | Check whether PIDs are sufficient.                                                                                                                  |
   |                                   |                                                                                                                                                     |
   |                                   | Handling suggestions: If there are not enough PIDs available, adjust the upper limit of PIDs as needed.                                             |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | MemoryPressure                    | Check whether the allocable memory for the containers is sufficient.                                                                                |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | DiskPressure                      | Check the usage of kubelet and Docker disks and inodes.                                                                                             |
   |                                   |                                                                                                                                                     |
   |                                   | Handling suggestions: Expand the capacity of the data disks.                                                                                        |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_faq_00468__section88261201143:

Locating a Node Fault by Performing a Self-Check
------------------------------------------------


.. figure:: /_static/images/en-us_image_0000002253778301.png
   :alt: **Figure 1** Performing a self-check

   **Figure 1** Performing a self-check

#. Log in to the CCE console.

#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Nodes** tab.

#. Locate the row containing the target node, choose **More** > **View YAML** in the **Operation** column, and check the **Status** field of the node.

   **The node is in the NotReady state.**

   -  Check the node status and verify whether the value of **PIDPressure**, **DiskPressure**, or **MemoryPressure** becomes **True**. If any of them becomes **True**, you can find the appropriate solution based on the exception keyword.
   -  Check the key components on the node and the logs of these components. The key components on a node include a kubelet and the node runtime (Docker or containerd). For details, see :ref:`Checking Key Components of the Node <cce_faq_00468__section1969171115149>`.

      -  Check the kubelet.

         Check whether the kubelet and its logs are normal. If there is an exception, see :ref:`Abnormal kubelet <cce_faq_00468__section157165596254>`.

      -  Check the runtime (Docker or containerd).

         -  Check the runtime of the node. If you are not sure whether the runtime is Docker or containerd, log in to the CCE console and view the runtime of the node.
         -  If there is an exception, see :ref:`Abnormal Runtime <cce_faq_00468__section35411769262>`.

      -  Check the NTP.

         -  Check whether the NTP, its logs, and the configurations are normal.
         -  If there is an exception, see :ref:`Abnormal NTP <cce_faq_00468__section385318165266>`.

   -  Check the node monitoring data and see whether the CPU, memory, and network resources of the node are normal. If there is an exception, rectify the fault by referring to :ref:`Memory Pressure <cce_faq_00468__section1587219358259>`.

   **The node is in the Unknown state.**

   -  Log in to the ECS console and check whether the node is present in the ECS list.
   -  Check whether the node is running properly.
   -  Check the key components on the node and the logs of these components. The key components on a node include a kubelet and the node runtime (Docker or containerd). For details, see :ref:`Checking Key Components of the Node <cce_faq_00468__section1969171115149>`.

      -  Check the kubelet.

         -  Check whether the kubelet and its logs are normal. If there is an exception, see :ref:`Abnormal kubelet <cce_faq_00468__section157165596254>`.

   -  Check the network connectivity of the node.

Common Problems and Troubleshooting Methods
-------------------------------------------

-  :ref:`Checking a Node <cce_faq_00468__section199699448112>`
-  :ref:`Checking the Node Monitoring Data <cce_faq_00468__section153218576118>`
-  :ref:`Checking the Node Events <cce_faq_00468__section519231018123>`
-  :ref:`Verifying Whether the ECS Has Been Deleted or Is Faulty <cce_faq_00468__section12601731141210>`
-  :ref:`Verifying Whether the ECS Can Be Logged In <cce_faq_00468__section17645545111216>`
-  :ref:`Checking the Node Security Group <cce_faq_00468__section48911515161316>`
-  :ref:`Checking the Disks Attached to the Node <cce_faq_00468__section14974183916131>`
-  :ref:`Checking Key Components of the Node <cce_faq_00468__section1969171115149>`
-  :ref:`Verifying Whether the Node DNS Address Is Properly Configured <cce_faq_00468__section9694184382315>`

.. _cce_faq_00468__section199699448112:

Checking a Node
---------------

#. Log in to the CCE console.
#. Click the name of the target cluster to access the cluster console.
#. In the navigation pane, choose **Nodes**. In the right pane, click the **Nodes** tab, locate the row containing the unavailable node, and view its status. (If NPD 1.6.10 or a later version is installed in the cluster, you will see a message indicating that the metrics for the unavailable node are abnormal. In this case, you can move the cursor to the upper part to view the specific problem. If the add-on is not installed, you can rectify the fault by referring to the check items.)

.. _cce_faq_00468__section153218576118:

Checking the Node Monitoring Data
---------------------------------

#. Log in to the CCE console.

#. Click the name of the target cluster to access the cluster console.

#. In the navigation pane, choose **Nodes**. In the right pane, click the **Nodes** tab, locate the row containing the abnormal node, and click **Monitor** in the **Operation** column.

   On the top of the displayed page, click **More Monitoring Data** to go to the AOM console and view historical monitoring records. If the CPU or memory usage of the node is too high, it can lead to high network latency or trigger system OOM, causing the node to be marked as unavailable.

.. _cce_faq_00468__section519231018123:

Checking the Node Events
------------------------

#. Log in to the CCE console.
#. Click the name of the target cluster to access the cluster console.
#. In the navigation pane, choose **Nodes**. In the right pane, click the **Nodes** tab, locate the row containing the abnormal node, click **View Events** in the **Operation** column, and check whether any abnormal event is reported. (The NPD add-on must be installed.)

.. _cce_faq_00468__section12601731141210:

Verifying Whether the ECS Has Been Deleted or Is Faulty
-------------------------------------------------------

#. Log in to the CCE console, click the name of the target cluster to access the cluster console, and view the name of the unavailable node.
#. Log in to the ECS console, search for the node, and check the ECS status.

   -  If the ECS has been deleted, go back to the CCE console, delete the node from the node list, and create another one.
   -  If the ECS is stopped or frozen, restore it first. It takes about 3 minutes to restore the node.
   -  If the ECS is faulty, restart it to rectify the fault.
   -  If the ECS is available, rectify the fault by referring to :ref:`Checking Key Components of the Node <cce_faq_00468__section1969171115149>`.

.. _cce_faq_00468__section17645545111216:

Verifying Whether the ECS Can Be Logged In
------------------------------------------

#. Log in to the ECS console.

#. Check whether the node name displayed on the ECS console is the same as that on the VM and whether the password or key can be used to log in to the node.

   If the node names are inconsistent and the password or key cannot be used to log in to the node, Cloud-Init problems occurred when the ECS was created. In this case, you can restart the node and then **submit a service ticket to the ECS personnel** to locate the root cause.

.. _cce_faq_00468__section48911515161316:

Checking the Node Security Group
--------------------------------

-  The node security group was changed.

   #. Log in to the VPC console. In the navigation pane, choose **Access Control** > **Security Groups** and find the master node security group of the cluster.
   #. Search for the name of the security group that contains the cluster name and **-cce-control-**. The name of a master node security group is in the format of *cluster-name*\ **-cce-control-**\ *random-ID*.
   #. Check whether the security group rules have been changed. For details about security groups, see :ref:`How Can I Configure a Security Group Rule for a Cluster? <cce_faq_00265>`

-  The node security group rules must contain a policy that allows the communication between the master nodes and the worker nodes.

   -  Check whether such a security group policy is present.

   -  When adding a node to the cluster, add the security group rules listed in :ref:`Table 2 <cce_faq_00468__table173615012426>` to the *cluster-name*\ **-cce-control-**\ *random-ID* security group to ensure the availability of the added node. This is necessary if a secondary CIDR block is added to the VPC of the node subnet and the subnet is in the secondary CIDR block. However, if a secondary CIDR block has already been added to the VPC during cluster creation, this step is not required.

   -  For details about security groups, see :ref:`How Can I Configure a Security Group Rule for a Cluster? <cce_faq_00265>`

      .. _cce_faq_00468__table173615012426:

      .. table:: **Table 2** Security group rules to be added

         +-------------------+------+-----------------------------------------------------+
         | Protocol and Port | Type | Source IP Address                                   |
         +===================+======+=====================================================+
         | TCP port 8445     | IPv4 | The new secondary CIDR block where the subnet is in |
         +-------------------+------+-----------------------------------------------------+
         | TCP port 9443     | IPv4 | The new secondary CIDR block where the subnet is in |
         +-------------------+------+-----------------------------------------------------+
         | TCP port 5444     | IPv4 | The new secondary CIDR block where the subnet is in |
         +-------------------+------+-----------------------------------------------------+

.. _cce_faq_00468__section14974183916131:

Checking the Disks Attached to the Node
---------------------------------------

-  By default, a 100-GiB data disk is attached to a node for runtime purposes. You have the option to attach additional data disks to the node if needed. If the data disk is detached or damaged, the runtime becomes abnormal and the node becomes unavailable.

-  You need to check whether the data disks of the node are detached from it. If they are, you are advised to create a node and delete the unavailable node. (To minimize risks, you are not advised to perform operations on the CCE nodes through the ECS console.)

.. _cce_faq_00468__section1969171115149:

Checking Key Components of the Node
-----------------------------------

**kubelet**

-  Check the kubelet.

   Log in to the target node, run the following command on it, and check the kubelet:

   .. code-block::

      systemctl status kubelet

   The following shows an example of the expected output.

   |image1|

-  View the kubelet logs.

   Log in to the target node, run the following command on it, and check the kubelet:

   .. code-block::

      journalctl -u kubelet

**Runtime**

-  Docker

   -  Check the Docker runtime.

      Log in to the target node, run the following command on it, and check the Docker process:

      .. code-block::

         systemctl status docker

      The following shows an example of the expected output.

      |image2|

   -  View the Docker logs.

      Log in to the target node, run the following command on it, and check the Docker logs:

      .. code-block::

         journalctl -u docker

-  containerd

   -  Check the containerd runtime.

      Log in to the target node, run the following command on it, and check the containerd process:

      .. code-block::

         systemctl status containerd

      The following shows an example of the expected output.

      |image3|

   -  View the containerd logs.

      Log in to the target node, run the following command on it, and check the containerd logs:

      .. code-block::

         journalctl -u containerd

-  NTP

   -  Check whether the NTP is normal.

      Log in to the target node, run the following command on it, and check the chronyd process:

      .. code-block::

         systemctl status chronyd

      The following shows an example of the expected output.

      |image4|

   -  View the NTP logs.

      Log in to the target node, run the following command on it, and check the NTP logs:

      .. code-block::

         journalctl -u chronyd

.. _cce_faq_00468__section9694184382315:

Verifying Whether the Node DNS Address Is Properly Configured
-------------------------------------------------------------

-  Log in to the target node and check whether any domain name resolution failure is recorded in **/var/log/cloud-init-output.log**:

   .. code-block::

      cat /var/log/cloud-init-output.log | grep resolv

   If information similar to the following is displayed, the domain name cannot be resolved:

   .. code-block::

      Could not resolve host: xxx ; Unknown error

-  Ping the domain name that cannot be resolved on the node:

   .. code-block::

      ping xxx

   If the domain name cannot be pinged, the DNS cannot resolve the IP address. You need to verify if the DNS address in the **/etc/resolv.conf** file matches the configuration on the VPC subnet. In most cases, the DNS address in the file is improperly configured, leading to the inability to resolve domain names.

Common Issues and Solutions
---------------------------

-  :ref:`PID Pressure <cce_faq_00468__section7917523112516>`
-  :ref:`Memory Pressure <cce_faq_00468__section1587219358259>`
-  :ref:`Disk Pressure <cce_faq_00468__section9667492258>`
-  :ref:`Abnormal kubelet <cce_faq_00468__section157165596254>`
-  :ref:`Abnormal Runtime <cce_faq_00468__section35411769262>`
-  :ref:`Abnormal NTP <cce_faq_00468__section385318165266>`
-  :ref:`Abnormal Node Restart <cce_faq_00468__section661117235262>`
-  :ref:`Abnormal Node Network <cce_faq_00468__section1232652917268>`
-  :ref:`Abnormal PLEG <cce_faq_00468__section8175154116263>`
-  :ref:`Node Overloaded <cce_faq_00468__section2794711161910>`
-  :ref:`Restricted Node Scheduling with the node.kubernetes.io/route-unreachable Taint <cce_faq_00468__section26241627068>`
-  :ref:`Node Unavailable Due to OOM <cce_faq_00468__section3736518293>`

.. _cce_faq_00468__section7917523112516:

PID Pressure
------------

**Possible cause**

The pods on the node are using up a large number of PIDs, causing a shortage of available PIDs on the node. By default, CCE reserves 10% of the available PIDs for pods.

**Symptom**

If the number of available PIDs on a node is lower than the specified value of **pid.available**, the **PIDPressure** of the node will be set to **True**, resulting in the eviction of pods running on that node. For details about node eviction, see `Node-pressure Eviction <https://kubernetes.io/docs/concepts/scheduling-eviction/node-pressure-eviction/>`__.

**Solution**

#. Check the maximum number of PIDs on the node and the processes that use the most PIDs:

   .. code-block::

      sysctl kernel.pid_max # Check the maximum number of PIDs.
      ps -eLf|awk '{print $2}' | sort -rn| head -n 1 #: Check the processes that use the most PIDs on the node.

#. Check the top five processes that use the most PIDs:

   .. code-block::

      ps -elT | awk '{print $4}' | sort | uniq -c | sort -k1 -g | tail -5

   The following shows an example of the expected output:

   .. code-block::

      17 1211619
      18 3739112
      18 5299
      24 964
      25 3739756

   The first column shows the number of PIDs used by each process, while the second column displays the current process IDs. You can locate the process and associated pods with the given process ID, analyze the reason for excessive PID usage, and optimize the relevant code accordingly.

#. Reduce the load of the node.

#. To restart the node, go to the ECS console and restart it. (**Be cautious when restarting the node because it may cause interruptions to your services.**)

.. _cce_faq_00468__section1587219358259:

Memory Pressure
---------------

**Possible cause**

The pods on the node are using up a large amount of memory, causing a shortage of available memory on the node. By default, the available memory of a CCE node is 100 MiB.

**Symptom**

-  If the number of available memory resources on a node is lower than the specified value of **memory.available**, the **MemoryPressure** of the node will be set to **True**, resulting in the eviction of pods running on that node. For details about node eviction, see `Node-pressure Eviction <https://kubernetes.io/docs/concepts/scheduling-eviction/node-pressure-eviction/>`__.
-  If the memory of a node is not enough, the following message will show:

   -  The value of **MemoryPressure** becomes **True**.
   -  When some pods on the node are evicted:

      -  You can see "The node was low on resource: memory" in the events of the evicted pods.
      -  You can see "attempting to reclaim memory" in the node events.

   -  The system OOM may behave abnormally. If such an error occurs, you can see "System OOM" in the node events.

**Solution**

-  Check the node memory usage through the node monitoring data, identify the time when the exception occurs, and verify if there is any memory leak in the processes on the node. For details, see :ref:`Checking the Node Monitoring Data <cce_faq_00468__section153218576118>`.
-  Reduce the load of the node.
-  To restart the node, go to the ECS console and restart it. (**Be cautious when restarting the node because it may cause interruptions to your services.**)

.. _cce_faq_00468__section9667492258:

Disk Pressure
-------------

**Possible cause**

The root file system, image file system, or container file system on the node is consuming excessive disk space and inodes, surpassing the eviction threshold. This leads to the **nodefs.available**, **nodefs.inodesFree**, **imagefs.available**, or **imagefs.inodesFree** metric met the eviction threshold, causing disk pressure. The table below shows the default values for these parameters.

+--------------------+-----------------------------------------------------------------------------------------------------------------------+---------------+
| Parameter          | Description                                                                                                           | Default Value |
+====================+=======================================================================================================================+===============+
| nodefs.available   | Percentage of the available capacity in the file system used by kubelet.                                              | 10%           |
+--------------------+-----------------------------------------------------------------------------------------------------------------------+---------------+
| nodefs.inodesFree  | Percentage of available inodes in the file system used by kubelet.                                                    | 5%            |
+--------------------+-----------------------------------------------------------------------------------------------------------------------+---------------+
| imagefs.available  | Percentage of the available capacity in the file system used by container runtimes to store resources such as images. | 10%           |
+--------------------+-----------------------------------------------------------------------------------------------------------------------+---------------+
| imagefs.inodesFree | Percentage of available inodes in the file system used by container runtimes to store resources such as images.       | 5%            |
+--------------------+-----------------------------------------------------------------------------------------------------------------------+---------------+

**Symptom**

-  If the available disk space on the node is less than the value of **imagefs.available**, the value of **DiskPressure** of the node becomes **True**.
-  If the available disk space is less than the value of **nodefs.available**, all pods on the node will be evicted. For details about node eviction, see `Node-pressure Eviction <https://kubernetes.io/docs/concepts/scheduling-eviction/node-pressure-eviction/>`__.
-  If the disk space on the node is insufficient, the following message will show:

   -  The value of **DiskPressure** becomes **True**.
   -  If the disk space remains insufficient to meet the healthy threshold (defaulted at 80%) even after the image reclaim policy is triggered, you can see "failed to garbage collect required amount of images" in the node events.
   -  When some pods on the node are evicted:

      -  You can see "The node was low on resource: [DiskPressure]" in the events of the evicted pods.
      -  You can see "attempting to reclaim ephemeral-storage" or "attempting to reclaim nodefs" in the node events.

**Solution**

-  View the node disk usage through the node monitoring data, identify the time when the exception occurs, and check if processes on the node are consuming excessive disk space. For details, see :ref:`Checking the Node Monitoring Data <cce_faq_00468__section153218576118>`.
-  If a large number of files are not deleted from the node disks, delete these files.
-  Restrict the ephemeral-storage configurations of the pods based on service requirements.
-  Use cloud storage services instead of hostPath volumes.
-  Expand the capacity of the node disks.
-  Reduce the load of the node.

.. _cce_faq_00468__section157165596254:

Abnormal kubelet
----------------

**Possible cause**

The kubelet process is not functioning properly or the kubelet configuration is improper. Typically, CCE has set up health checks for kubelet as a default configuration. There is a greater chance of startup failure if the configuration is incorrect.

**Symptom**

kubelet is inactive.

**Solution**

#. Log in to the abnormal node and restart kubelet: (Restarting kubelet does not affect the running containers.)

   .. code-block::

      systemctl restart kubelet

#. Check whether the kubelet status becomes normal:

   .. code-block::

      systemctl status kubelet

#. If the kubelet status is still abnormal after the restart, log in to the node and view the kubelet logs:

   .. code-block::

      journalctl -u kubelet

   -  If there are error messages in the logs, find the cause by looking for specific keywords associated with the error.
   -  If there is an issue with the kubelet configuration, find the node pool that the node belongs to, click **Manage** in the **Operation** column, and make changes to the kubelet configuration.

.. _cce_faq_00468__section35411769262:

Abnormal Runtime
----------------

**Possible cause**

The Docker or containerd configuration or process is not functioning properly.

**Symptom**

-  Docker

   -  Docker is inactive.
   -  Docker is active and running, but the node is experiencing issues and not functioning properly, resulting in abnormal behavior. In this case, the **docker ps** or **docker exec** command fails to be executed.
   -  The value of **RuntimeOffline** of the node becomes **True**.

-  containerd

   -  containerd is inactive.
   -  The value of **RuntimeOffline** of the node becomes **True**.

**Solution**

#. Log in to the abnormal node.

#. Restart the runtime:

   .. code-block::

      # Docker
      systemctl restart docker
      # Containerd
      systemctl restart containerd

#. After the command is executed, check whether the running status is normal.

   .. code-block::

      # Docker
      systemctl status docker
      # Containerd
      systemctl status containerd

#. If the component status is still abnormal after the restart, check the component logs:

   .. code-block::

      # Docker
      journalctl -u docker
      # containerd
      journalctl -u containerd

.. _cce_faq_00468__section385318165266:

Abnormal NTP
------------

**Possible cause**

The NTP process is abnormal.

**Symptom**

-  chronyd is inactive.
-  The value of **NTPProblem** becomes **True**.

**Solution**

#. Log in to the abnormal node and restart chronyd:

   .. code-block::

      systemctl restart chronyd

#. After the restart, check whether the chronyd status becomes normal:

   .. code-block::

      systemctl status chronyd

#. If the chronyd status is still abnormal after the restart, log in to the node and view the chronyd logs:

   .. code-block::

      journalctl -u chronyd

.. _cce_faq_00468__section661117235262:

Abnormal Node Restart
---------------------

**Possible cause**

The node is experiencing abnormal load.

**Symptom**

During the restart, the node is in the **NotReady** state.

**Solution**

#. Check the time when the node was restarted:

   .. code-block::

      last reboot

   The following shows an example of the expected output.

   |image5|

#. View the node monitoring data and locate the abnormal resource based on the restart time of the node. For details, see :ref:`Checking the Node Monitoring Data <cce_faq_00468__section153218576118>`.

#. Check the kernel logs and locate the fault based on the restart time.

.. _cce_faq_00468__section1232652917268:

Abnormal Node Network
---------------------

**Possible cause**

The node is experiencing abnormal running status, incorrect security group configuration, or excessive network load.

**Symptom**

-  The node cannot be logged in to.
-  The node is in the **Unknown** state.

**Solution**

-  If you cannot log in to the node, take the following steps to locate the fault:

   -  Check whether the node is running on the ECS console.
   -  Check whether the fault is caused by the execution failure of Cloud-Init of the ECS. For details, see :ref:`Verifying Whether the ECS Can Be Logged In <cce_faq_00468__section17645545111216>`.
   -  Check the security group configuration of the node. For details, see :ref:`Checking the Node Security Group <cce_faq_00468__section48911515161316>`.

-  If the network load of the node is too high, perform the following operations:

   -  View the node networking through the node monitoring data and check whether the pods on the node are consuming excessive network bandwidth.
   -  Use network policies to control network traffic of the pods on the node.

.. _cce_faq_00468__section8175154116263:

Abnormal PLEG
-------------

**Possible cause**

The pod lifecycle event generator (PLEG) records different events in the lifecycle of a pod, such as the pod startup and termination. The error "PLEG is not healthy" is usually due to abnormal runtime processes on the node or issues with the systemd version on the node.

**Symptom**

-  The node is in the **NotReady** state.

-  You can see the following information in the kubelet logs:

   .. code-block::

      skipping pod synchronization - PLEG is not healthy: pleg was last seen active 3m17.028393648s ago; threshold is 3m0s.

**Solution**

-  Restart Docker or containerd and kubelet in sequence and then check whether the node is restored.
-  If the node is not restored after the restart of the key components, restart the node. (**Be cautious when restarting the node because it may cause interruptions to your services.**)

.. _cce_faq_00468__section2794711161910:

Node Overloaded
---------------

**Possible cause**

The node resources are not enough for pod scheduling.

**Symptom**

If there are not enough scheduling resources available on the node, pod scheduling will fail and the following error information will be displayed: (Only errors related to common resources are listed.)

-  Insufficient CPUs in a cluster: 0/2 nodes are available: 2 Insufficient cpu
-  Insufficient memory in a cluster: 0/2 nodes are available: 2 Insufficient memory
-  Insufficient temporary storage space in a cluster: 0/2 nodes are available: 2 Insufficient ephemeral-storage

The scheduler determines that node resources are insufficient using the following calculation methods:

-  Whether the CPUs of a node are insufficient: Total CPUs requested by a pod > (Total allocatable CPUs on the node - Total CPUs that have been allocated to the pods on the node)
-  Whether the memory of a node is insufficient: Total memory requested by a > (Total allocatable memory on the node - Total memory that has been allocated to the pods on the node)
-  Whether the temporary storage space of a node is insufficient: Temporary storage space requested by a pod > (Total allocatable temporary storage space on the node - Total temporary storage space that has been allocated to the pods on the node)

If the total resources requested by the pod exceed the allocatable resources on the node (after subtracting the allocated resources to the pods on the node), the pod will not be scheduled on that node.

Check the resource allocation details on the node:

.. code-block::

   kubectl describe node $nodeName

Pay attention to the resource allocation in the command output:

.. code-block::

   Allocatable:
     cpu:                1930m
     ephemeral-storage:  94576560382
     hugepages-1Gi:      0
     hugepages-2Mi:      0
     localssd:           0
     localvolume:        0
     memory:             2511096Ki
   pods:               20
   Allocated resources:
     (Total limits may be over 100 percent, i.e., overcommitted.)
     Resource           Requests      Limits
     --------           --------      ------
     cpu                1255m (65%)   4600m (238%)
     memory             1945Mi (79%)  3876Mi (158%)
     ephemeral-storage  0 (0%)        0 (0%)
     hugepages-1Gi      0 (0%)        0 (0%)
     hugepages-2Mi      0 (0%)        0 (0%)
     localssd           0             0
     localvolume        0             0

Specifically:

-  **Allocatable**: specifies the total number of allocatable resources like CPUs, memory, and temporary storage on a node.
-  **Allocated resources**: specifies the total number of resources like CPUs, memory, and temporary storage that have been allocated to the pods on a node.

**Solution**

If the resources on a node are not enough for pod scheduling, reduce the node load through either of the following ways:

-  Delete unnecessary pods.
-  Restrict the resource configurations of pods based on service requirements.
-  Add more nodes to the cluster.

.. _cce_faq_00468__section26241627068:

Restricted Node Scheduling with the node.kubernetes.io/route-unreachable Taint
------------------------------------------------------------------------------

**Possible cause**

The network infrastructure's route tables allow the container networks in a cluster that uses the VPC network model to be accessible. A newly created CCE node has network isolation support. If the node network is not functioning properly, the system will automatically add the **node.kubernetes.io/route-unreachable** taint to the node and remove it once the network is ready. If the **node.kubernetes.io/route-unreachable** taint remains for an extended period, it indicates abnormal network connectivity for the node.

**Symptom**

A newly created node is **restricted for scheduling** for a long time.

**Solution**

#. If there are other normal nodes in the cluster, run the ping command to check the network connectivity between containers on different nodes.

   Create a container for testing. In the following example, {node_ip} indicates the IP address of the abnormal node.

   .. code-block::

      kind: Pod
      apiVersion: v1
      metadata:
        name: nginx
        namespace: default
      spec:
        containers:
          - name: container-1
            image: nginx:latest
            imagePullPolicy: IfNotPresent
        imagePullSecrets:
          - name: default-secret
        affinity:
          nodeAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
              nodeSelectorTerms:
                - matchExpressions:
                    - key: kubernetes.io/hostname
                      operator: In
                      values:
                        - {node_ip}
        schedulerName: default-scheduler
        tolerations:
          - key: node.kubernetes.io/route-unreachabe
            operator: Exists
            effect: NoSchedule

   After the container is started, log in to another normal node and ping the IP address of the container. If the communication is abnormal, go to the next step.

#. On the **Route Tables** page of the VPC console, check whether the node route has been added, whether the next hop type is cloud container, and whether the next hop is the node name. If the node route is present in the route table but the node is still unable to connect to the network, it suggests a potential problem with the underlying network. In such situations, submit a service ticket to the networking team for assistance.

#. If the fault persists, submit a service ticket to CCE for troubleshooting.

.. _cce_faq_00468__section3736518293:

Node Unavailable Due to OOM
---------------------------

**Possible cause**

Many containers are scheduled onto a node, using up all its resources and causing an OOM issue. This issue is primarily seen on nodes running the Docker container engine.

**Symptom**

If a node in the cluster is assigned too many containers, the node OS might crash, rendering the node unavailable. You may see the information similar to the following after logging in to the node with VNC.

|image6|

Check the node events:

.. code-block::

   kubectl describe node {nodeName}

Pay attention to the abnormal node events in the output.

|image7|

**Solution**

If the resources on a node are not enough for pod scheduling, reduce the node load through either of the following ways:

-  Reset the faulty node.
-  Delete unnecessary pods.
-  Restrict the resource configurations of pods based on service requirements.
-  Add more nodes to the cluster.

.. |image1| image:: /_static/images/en-us_image_0000002218658526.png
.. |image2| image:: /_static/images/en-us_image_0000002253778245.png
.. |image3| image:: /_static/images/en-us_image_0000002218818366.png
.. |image4| image:: /_static/images/en-us_image_0000002218658654.png
.. |image5| image:: /_static/images/en-us_image_0000002218818398.png
.. |image6| image:: /_static/images/en-us_image_0000002253778265.png
.. |image7| image:: /_static/images/en-us_image_0000002218818382.png
