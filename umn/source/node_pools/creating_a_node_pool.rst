:original_name: cce_01_0012.html

.. _cce_01_0012:

Creating a Node Pool
====================

Scenario
--------

This section describes how to create a node pool and perform operations on the node pool. For details about how a node pool works, see :ref:`Node Pool Overview <cce_01_0081>`.

Notes and Constraints
---------------------

-  For details about how to add a node pool to a CCE Turbo cluster, see :ref:`Procedure - for CCE Turbo Clusters <cce_01_0012__section953835110714>`.
-  The autoscaler add-on needs to be installed for node auto scaling. For details about the add-on installation and parameter configuration, see :ref:`autoscaler <cce_01_0154>`.

Procedure
---------

To create a node pool in a cluster, perform the following steps:

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Node Pools**.

#. In the upper right corner of the page, click **Create Node Pool**.

#. Set node pool parameters.

   -  **Current Region**: geographic location of the node pool to be created.

      To minimize network latency and resource access time, select the region nearest to your node pool. Cloud resources are region-specific and cannot be used across regions over internal networks.

   -  **Name**: name of the new node pool. By default, the name is in the format of *Cluster name*-nodepool-*Random number*. You can also use a custom name.

   -  **Node Type**: Currently, only VM nodes are supported.

   -  **Nodes**: number of nodes to be created for this node pool. The value cannot exceed the maximum number of nodes that can be managed by the cluster.

   -  .. _cce_01_0012__li153215432027:

      **Auto Scaling**:

      -  By default, this parameter is disabled.

      -  After you enable autoscaler by clicking |image1|, nodes in the node pool will be automatically created or deleted based on cluster loads.

         -  **Maximum Nodes** and **Minimum Nodes**: You can set the maximum and minimum number of nodes to ensure that the number of nodes to be scaled is within a proper range.

         -  **Priority**: Set this parameter based on service requirements. A larger value indicates a higher priority. For example, if this parameter is set to **1** and **4** respectively for node pools A and B, B has a higher priority than A. If the priorities of multiple node pools are set to the same value, for example, **2**, the node pools are not prioritized and the system performs scaling based on the minimum resource waste principle.

            .. note::

               CCE selects a node pool for auto scaling based on the following policies:

               a. CCE uses algorithms to determine whether a node pool meets the conditions to allow scheduling of a pod in pending state, including whether the node resources are greater than requested by the pod, and whether the nodeSelect, nodeAffinity, and taints meet the conditions. In addition, the node pools that fail to be scaled (due to insufficient resources or other reasons) and are still in the 15-minute cool-down interval are filtered.
               b. If multiple node pools meet the scaling requirements, the system checks the priority of each node pool and selects the node pool with the highest priority for scaling. The value ranges from 0 to 100 and the default priority is 0. The value 100 indicates the highest priority, and the value 0 indicates the lowest priority.
               c. If multiple node pools have the same priority or no priority is configured for them, the system selects the node pool that will consume the least resources based on the configured VM specification.
               d. If the VM specifications of multiple node pools are the same but the node pools are deployed in different AZs, the system randomly selects a node pool to trigger scaling.

         -  .. _cce_01_0012__li16733114525110:

            **Scale-In Cooling Interval**: Set this parameter in the unit of minute or hour. This parameter indicates the interval between the previous scale-out action and the next scale-in action.

            Scale-in cooling intervals can be configured in the node pool settings and the :ref:`autoscaler add-on <cce_01_0154>` settings.

            **Scale-in cooling interval configured in a node pool**

            This interval indicates the period during which nodes added to the current node pool after a scale-out operation cannot be deleted. This interval takes effect at the node pool level.

            **Scale-in cooling interval configured in the autoscaler add-on**

            The interval after a scale-out indicates the period during which the entire cluster cannot be scaled in after the autoscaler add-on triggers scale-out (due to the unschedulable pods, metrics, and scaling policies). This interval takes effect at the cluster level.

            The interval after a node is deleted indicates the period during which the cluster cannot be scaled in after the autoscaler add-on triggers scale-in. This interval takes effect at the cluster level.

            The interval after a failed scale-in indicates the period during which the cluster cannot be scaled in after the autoscaler add-on triggers scale-in. This interval takes effect at the cluster level.

         .. note::

            You are advised not to store important data on nodes in a node pool because after auto scaling, data cannot be restored as nodes may be deleted.

         If **Autoscaler** is enabled, install the :ref:`autoscaler add-on <cce_01_0154>` to use the auto scaling feature.

   -  **AZ**: An AZ is a physical region where resources use independent power supply and networks. AZs are physically isolated but interconnected through an internal network.

      Set an AZ based on your requirements. After a node pool is created, **AZ** cannot be modified. Exercise caution when selecting an AZ for the node pool.

      To enhance workload reliability, you are advised to select **Random AZ**, allowing nodes to be randomly and evenly distributed among different AZs.

      .. note::

         In a CCE Turbo cluster, an AZ is randomly selected from available AZs, and all nodes are created in the selected AZ.

   -  **Specifications**: Select node specifications that best fit your business needs.

      -  **General-purpose**: provides a balance of computing, memory, and network resources. It is a good choice for many applications, such as web servers, workload development, workload testing, and small-scale databases.
      -  **Memory-optimized**: provides higher memory capacity than general-purpose nodes and is suitable for relational databases, NoSQL, and other workloads that are both memory-intensive and data-intensive.
      -  **GPU-accelerated**: provides powerful floating-point computing and is suitable for real-time, highly concurrent massive computing. Graphical processing units (GPUs) of P series are suitable for deep learning, scientific computing, and CAE. GPUs of G series are suitable for 3D animation rendering and CAD. **GPU-accelerated nodes can be created only in clusters of v1.11 or later**. GPU-accelerated nodes are available only in certain regions.
      -  **General computing-plus**: provides stable performance and exclusive resources to enterprise-class workloads with high and stable computing performance.
      -  **Disk-intensive**: supports :ref:`local disk storage <cce_01_0053>` and provides high network performance. It is designed for workloads requiring high throughput and data switching, such as big data workloads.

      To ensure node stability, CCE automatically reserves some resources to run necessary system components. For details, see :ref:`Formula for Calculating the Reserved Resources of a Node <cce_01_0178>`.

   -  **OS**: Select an OS for the node to be created.

      .. important::

         Reinstalling the OS or modifying OS configurations could make the node unavailable. Exercise caution when performing these operations.

   -  **VPC**: The value is the same as that of the cluster and cannot be changed.

      **This parameter is displayed only for clusters of v1.13.10-r0 and later.**

   -  **Subnet**: A subnet improves network security by providing exclusive network resources that are isolated from other networks.

      You can select any subnet in the cluster VPC. Cluster nodes can belong to different subnets.

      Ensure that the DNS server in the subnet can resolve the OBS domain name. Otherwise, nodes cannot be created.

      **This parameter is displayed only for clusters of v1.13.10-r0 and later.**

   -  **System Disk**: Set the system disk space of the worker node. The value ranges from 40GB to 1024 GB. The default value is 40GB.

      By default, system disks support Common I/O (SATA), High I/O (SAS), and Ultra-high I/O (SSD)High I/O (SAS) and Ultra-high I/O (SSD) EVS disks.

      **Encryption**: Data disk encryption safeguards your data. Snapshots generated from encrypted disks and disks created using these snapshots automatically inherit the encryption function. **This function is available only in certain regions.**

      -  **Encryption** is not selected by default.
      -  After you select **Encryption**, you can select an existing key in the displayed **Encryption Setting** dialog box. If no key is available, click the link next to the drop-down box to create a key. After the key is created, click the refresh icon.

   -  .. _cce_01_0012__li1931815591054:

      **Data Disk**: Set the data disk space of the worker node. The value ranges from 100 GB to 32,768 GB. The default value is 100 GB. The EVS disk types provided for the data disk are the same as those for the system disk.

      .. caution::

         If the data disk is uninstalled or damaged, the Docker service becomes abnormal and the node becomes unavailable. You are advised not to delete the data disk.

      -  **LVM**: If this option is selected, CCE data disks are managed by the Logical Volume Manager (LVM). On this condition, you can adjust the disk space allocation for different resources. This option is selected for the first disk by default and cannot be unselected. You can choose to enable or disable LVM for new data disks.

         -  This option is selected by default, indicating that LVM management is enabled.
         -  You can deselect the check box to disable LVM management.

            .. caution::

               -  Disk space of the data disks managed by LVM will be allocated according to the ratio you set.
               -  When creating a node in a cluster of v1.13.10 or later, if LVM is not selected for a data disk, follow instructions in :ref:`Adding a Second Data Disk to a Node in a CCE Cluster <cce_01_0344>` to fill in the pre-installation script and format the data disk. Otherwise, the data disk will still be managed by LVM.
               -  When creating a node in a cluster earlier than v1.13.10, you must format the data disks that are not managed by LVM. Otherwise, either these data disks or the first data disk will be managed by LVM.

      -  **Encryption**: Data disk encryption safeguards your data. Snapshots generated from encrypted disks and disks created using these snapshots automatically inherit the encryption function.

         **This function is supported only for clusters of v1.13.10 or later in certain regions,** and is not displayed for clusters of v1.13.10 or earlier.

         -  **Encryption** is not selected by default.
         -  After you select **Encryption**, you can select an existing key in the displayed **Encryption Setting** dialog box. If no key is available, click the link next to the drop-down box to create a key. After the key is created, click the refresh icon.

      -  **Add Data Disk**: Currently, a maximum of two data disks can be attached to a node. After the node is created, you can go to the ECS console to attach more data disks. This function is available only to clusters of certain versions.

      -  **Data disk space allocation**: Click |image2| to specify the resource ratio for **Kubernetes Space** and **User Space**. Disk space of the data disks managed by LVM will be allocated according to the ratio you set. This function is available only to clusters of certain versions.

         -  **Kubernetes Space**: You can specify the ratio of the data disk space for storing Docker and kubelet resources. Docker resources include the Docker working directory, Docker images, and image metadata. kubelet resources include pod configuration files, secrets, and emptyDirs.

            The Docker space cannot be less than 10%, and the space size cannot be less than 60 GB. The kubelet space cannot be less than 10%.

            The Docker space size is determined by your service requirements. For details, see :ref:`Data Disk Space Allocation <cce_01_0341>`.

         -  **User Space**: You can set the ratio of the disk space that is not allocated to Kubernetes resources and the path to which the user space is mounted.

            .. note::

               Note that the mount path cannot be **/**, **/home/paas**, **/var/paas**, **/var/lib**, **/var/script**, **/var/log**, **/mnt/paas**, or **/opt/cloud**, and cannot conflict with the system directories (such as **bin**, **lib**, **home**, **root**, **boot**, **dev**, **etc**, **lost+found**, **mnt**, **proc**, **sbin**, **srv**, **tmp**, **var**, **media**, **opt**, **selinux**, **sys**, and **usr**). Otherwise, the system or node installation will fail.

      **If the cluster version is v1.13.10-r0 or later and the node specification is Disk-intensive, the following options are displayed for data disks:**

      -  **EVS**: Parameters are the same as those when the node type is not Disk-intensive. For details, see :ref:`Data Disk <cce_01_0012__li1931815591054>` above.

      -  **Local disk**: Local disks may break down and do not ensure data reliability. It is recommended that you store service data in EVS disks, which are more reliable than local disks.

         Local disk parameters are as follows:

         -  **Disk Mode**: If the node type is **disk-intensive**, the supported disk mode is HDD.
         -  **Read/Write Mode**: When multiple local disks exist, you can set the read/write mode. The serial and sequential modes are supported. **Sequential** indicates that data is read and written in linear mode. When a disk is used up, the next disk is used. **Serial** indicates that data is read and written in striping mode, allowing multiple local disks to be read and written at the same time.
         -  **Kubernetes Space**: You can specify the ratio of the data disk space for storing Docker and kubelet resources. Docker resources include the Docker working directory, Docker images, and image metadata. kubelet resources include pod configuration files, secrets, and emptyDirs.
         -  **User Space**: You can set the ratio of the disk space that is not allocated to Kubernetes resources and the path to which the user space is mounted.

      .. important::

         -  The ratio of disk space allocated to the Kubernetes space and user space must be equal to 100% in total. You can click |image3| to refresh the data after you have modified the ratio.
         -  By default, disks run in the direct-lvm mode. If data disks are removed, the loop-lvm mode will be used and this will impair system stability.

   -  **Login Mode**:

      -  **Key pair**: Select the key pair used to log in to the node. You can select a shared key.

         A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create a key pair**.

         .. important::

            When creating a node using a key pair, IAM users can select only the key pairs created by their own, regardless of whether these users are in the same group. For example, user B cannot use the key pair created by user A to create a node, and the key pair is not displayed in the drop-down list on the CCE console.

#. **Advanced ECS Settings** (optional): Click |image4| to show advanced ECS settings.

   -  **ECS Group**: An ECS group logically groups ECSs. The ECSs in the same ECS group comply with the same policy associated with the ECS group.

      -  **Anti-affinity**: ECSs in an ECS group are deployed on different physical hosts to improve service reliability.

      Select an existing ECS group, or click **Create ECS Group** to create one. After the ECS group is created, click the refresh button.

   -  **Resource Tags**: By adding tags to resources, you can classify resources.

      You can create predefined tags in Tag Management Service (TMS). Predefined tags are visible to all service resources that support the tagging function. You can use predefined tags to improve tag creation and migration efficiency.

      CCE will automatically create the "CCE-Dynamic-Provisioning-Node=node id" tag. A maximum of 5 tags can be added.

   -  **Agency**: An agency is created by a tenant administrator on the IAM console. By creating an agency, you can share your cloud server resources with another account, or entrust a more professional person or team to manage your resources. To authorize an ECS or BMS to call cloud services, select **Cloud service** as the agency type, click **Select**, and then select **ECS BMS**.

   -  **Pre-installation Script**: Enter a maximum of 1,000 characters.

      The script will be executed before Kubernetes software is installed. Note that if the script is incorrect, Kubernetes software may fail to be installed. The script is usually used to format data disks.

   -  **Post-installation Script**: Enter a maximum of 1,000 characters.

      The script will be executed after Kubernetes software is installed and will not affect the installation. The script is usually used to modify Docker parameters.

   -  **Subnet IP Address**: Select **Automatically assign IP address** (recommended) or **Manually assigning IP addresses**.

#. **Advanced Kubernetes Settings** (optional): Click |image5| to show advanced Kubernetes settings.

   -  **Max Pods**: maximum number of pods that can be created on a node, including the system's default pods. If the cluster uses the **VPC network model**, the maximum value is determined by the number of IP addresses that can be allocated to containers on each node.

      This limit prevents the node from being overloaded by managing too many pods. For details, see :ref:`Maximum Number of Pods That Can Be Created on a Node <cce_01_0348>`.

   -  **Taints**: This field is left blank by default. Taints allow nodes to repel a set of pods. You can add a maximum of 10 taints for each node. Each taint contains the following parameters:

      -  **Key**: A key must contain 1 to 63 characters starting with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed. A DNS subdomain name can be used as the prefix of a key.
      -  **Value**: A value must start with a letter or digit and can contain a maximum of 63 characters, including letters, digits, hyphens (-), underscores (_), and periods (.).
      -  **Effect**: Available options are **NoSchedule**, **PreferNoSchedule**, and **NoExecute**.

      .. important::

         -  If taints are used, you must configure tolerations in the YAML files of pods. Otherwise, scale-up may fail or pods cannot be scheduled onto the added nodes.
         -  After a node pool is created, you can click **Edit** to modify its configuration. The modification will be synchronized to all nodes in the node pool.

   -  **K8S Labels**: Labels are key/value pairs that are attached to objects, such as pods. Labels are used to specify identifying attributes of objects that are meaningful and relevant to users, but do not directly imply semantics to the core system. For more information, see `Labels and Selectors <https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/>`__.

   -  **Maximum Data Space per Container**: maximum data space that can be used by a container. The value ranges from 10 GB to 500 GB. If the value of this field is larger than the data disk space allocated to Docker resources, the latter will override the value specified here. Typically, 90% of the data disk space is allocated to Docker resources. This parameter is displayed only for clusters of v1.13.10-r0 and later.

#. Click **Next: Confirm** to confirm the configured service parameters and specifications.

#. Click **Submit**.

   It takes about 6 to 10 minutes to create a node pool. You can click **Back to Node Pool List** to perform other operations on the node pool or click **Go to Node Pool Events** to view the node pool details. If the status of the node pool is Normal, the node pool is successfully created.

.. _cce_01_0012__section953835110714:

Procedure - for CCE Turbo Clusters
----------------------------------

#. Log in to the CCE console.
#. Click the cluster name to open its details page, choose **Nodes** on the left, and click the **Node Pool** tab on the right.
#. In the upper right corner of the page, click **Create Node Pool**.
#. Configure computing parameters.

   -  **AZ**: An AZ is a physical region where resources use independent power supply and networks. AZs are physically isolated but interconnected through an internal network.

      Set an AZ based on your requirements. After a node pool is created, **AZ** cannot be modified. Exercise caution when selecting an AZ for the node pool.

      To enhance workload reliability, you are advised to select **Random AZ**, allowing nodes to be randomly and evenly distributed among different AZs.

   -  **Container Runtime**: runc or kata.

      For details about common containers and secure containers, see :ref:`Secure Containers and Common Containers <cce_01_0180__section7201124294111>`.

   -  **Specifications**: Select node specifications that best fit your business needs.

      -  **General-purpose**: provides a balance of computing, memory, and network resources. It is a good choice for many applications, such as web servers, workload development, workload testing, and small-scale databases.
      -  **Memory-optimized**: provides higher memory capacity than general-purpose nodes and is suitable for relational databases, NoSQL, and other workloads that are both memory-intensive and data-intensive.
      -  **GPU-accelerated**: provides powerful floating-point computing and is suitable for real-time, highly concurrent massive computing. Graphical processing units (GPUs) of P series are suitable for deep learning, scientific computing, and CAE. GPUs of G series are suitable for 3D animation rendering and CAD. **GPU-accelerated nodes can be created only in clusters of v1.11 or later**. GPU-accelerated nodes are available only in certain regions.
      -  **General computing-plus**: provides stable performance and exclusive resources to enterprise-class workloads with high and stable computing performance.
      -  **Disk-intensive**: supports :ref:`local disk storage <cce_01_0053>` and provides high network performance. It is designed for workloads requiring high throughput and data switching, such as big data workloads.

      To ensure node stability, CCE automatically reserves some resources to run necessary system components. For details, see :ref:`Formula for Calculating the Reserved Resources of a Node <cce_01_0178>`.

   -  **OS**: Select an OS for the node to be created. In certain regions, only OSs are displayed and options **Public image** and **Private image** are unavailable.

      -  **Public image**: Select an OS for the node.
      -  **Private image (OBT)**: If no private image is available, click **Creating a Private Image** to create one. **This function is available only for clusters of v1.15 or later.**

      .. important::

         Reinstalling the OS or modifying OS configurations could make the node unavailable. Exercise caution when performing these operations.

   -  **Login Mode**:

      -  **Key pair**: Select the key pair used to log in to the node. You can select a shared key.

         A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create a key pair**.

         .. important::

            When creating a node using a key pair, IAM users can select only the key pairs created by their own, regardless of whether these users are in the same group. For example, user B cannot use the key pair created by user A to create a node, and the key pair is not displayed in the drop-down list on the CCE console.

#. Configure storage parameters.

   -  **System Disk**: Set the system disk space of the worker node. The value ranges from 40GB to 1024 GB. The default value is 50 GB.

      By default, system disks support Common I/O (SATA), High I/O (SAS), and Ultra-high I/O (SSD)High I/O (SAS) and Ultra-high I/O (SSD) EVS disks.

   -  **Data Disk**: Set the data disk space of the worker node. The value ranges from 100 GB to 32,768 GB. The default value is 100 GB. The data disk space size is determined by your service requirements. For details, see :ref:`Data Disk Space Allocation <cce_01_0341>`.

      If the cluster version is v1.13.10-r0 or later and the node type is Disk-intensive, data disks can be EVS disks or local disks.

      .. caution::

         If the data disk is uninstalled or damaged, the Docker service becomes abnormal and the node becomes unavailable. You are advised not to delete the data disk.

      -  Data disk space allocation: Click **Expand** and select **Allocate Disk Space** to customize the data disk space usage.

         You can customize the resource proportion for the container runtime and kubelet in the data disk. By default, 90% of the space is allocated to containers, and the remaining space is allocated to the kubelet component.

         You can also define the maximum space that can be occupied by a single container. The default value is 10 GB.

      -  Adding data disks: The node must have at least one data disk, and data disks can be added. Click **Add Data Disk**. Click **Expand** to attach the new data disk to the specified directory.

         .. note::

            Note that the mount path cannot be **/**, **/home/paas**, **/var/paas**, **/var/lib**, **/var/script**, **/var/log**, **/mnt/paas**, or **/opt/cloud**, and cannot conflict with the system directories (such as **bin**, **lib**, **home**, **root**, **boot**, **dev**, **etc**, **lost+found**, **mnt**, **proc**, **sbin**, **srv**, **tmp**, **var**, **media**, **opt**, **selinux**, **sys**, and **usr**). Otherwise, the system or node installation will fail.

      -  **Encryption**: Data disk encryption safeguards your data. Snapshots generated from encrypted disks and disks created using these snapshots automatically inherit the encryption function.

         -  **Encryption** is not selected by default.
         -  After you select **Encryption**, you can select an existing key in the displayed **Encryption Setting** dialog box. If no key is available, click the link next to the drop-down box to create a key. After the key is created, click the refresh icon.

#. Configure networking parameters.

   -  **VPC**: The value is the same as that of the cluster and cannot be changed.

      **This parameter is displayed only for clusters of v1.13.10-r0 and later.**

   -  **Subnet**: A subnet improves network security by providing exclusive network resources that are isolated from other networks.

      You can select any subnet in the cluster VPC. Cluster nodes can belong to different subnets.

      Ensure that the DNS server in the subnet can resolve the OBS domain name. Otherwise, nodes cannot be created.

      **This parameter is displayed only for clusters of v1.13.10-r0 and later.**

#. Configure advanced settings.

   -  **Kubernetes Label**: Kubernetes provides labels for you to run kubectl commands to filter node resources by label.

   -  **Resource Tags**: Resource tags can be added to classify resources.

      You can create **predefined tags** in Tag Management Service (TMS). Predefined tags are visible to all service resources that support the tagging function. You can use these tags to improve tagging and resource migration efficiency.

      CCE will automatically create the "CCE-Dynamic-Provisioning-Node=\ *Node ID*" tag. A maximum of 5 tags can be added.

   -  **Taints**: Taints allow a node to repel a set of pods and work with tolerations to ensure that pods are not scheduled onto inappropriate nodes. For details, see :ref:`Configuring Node Scheduling (Tainting) <cce_01_0352>`.

   -  **Max Pods**: maximum number of pods that can be created on a node, including the system's default pods. If the cluster uses the **VPC network model**, the maximum value is determined by the number of IP addresses that can be allocated to containers on each node.

      This limit prevents the node from being overloaded by managing too many pods. For details, see :ref:`Maximum Number of Pods That Can Be Created on a Node <cce_01_0348>`.

   -  **Pre-installation Script**: Enter a maximum of 1,000 characters.

      The script will be executed before Kubernetes software is installed. Note that if the script is incorrect, Kubernetes software may fail to be installed. It is commonly used to format data disks.

   -  **Post-installation Script**: Enter a maximum of 1,000 characters.

      The script will be executed after Kubernetes software is installed and will not affect the installation. It is commonly used to modify Docker parameters.

   -  **Maximum Data Space per Container**: maximum data space that can be used by a container. The value ranges from 10 GB to 500 GB. If the value of this field is larger than the data disk space allocated to Docker resources, the latter will override the value specified here. Typically, 90% of the data disk space is allocated to Docker resources. This parameter is displayed only for clusters of v1.13.10-r0 and later.

#. Click **Next: Confirm**.
#. Click **Submit**.

Viewing Node Pools in a Cluster
-------------------------------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Node Pools**.
#. In the upper right corner of the node pool list, select a cluster. All node pools in the cluster will be displayed. You can view the node type, node specifications, autoscaler status, and OS of each node pool.

   .. note::

      -  A default node pool **DefaultPool** is automatically created in each cluster. The default node pool cannot be edited, deleted, or migrated. All nodes created during and after cluster creation are displayed in the default node pool.
      -  To display a list of nodes in **DefaultPool**, click the **Nodes** subcard in the **DefaultPool** card.

#. To filter node pools by autoscaler status, select the autoscaler status in the upper right corner of the node pool list.
#. In the node pool list, click a node pool name. On the node pool details page, view the basic information, advanced ECS settings, advanced Kubernetes settings, and node list of the node pool.

.. |image1| image:: /_static/images/en-us_image_0258503428.png
.. |image2| image:: /_static/images/en-us_image_0273156799.png
.. |image3| image:: /_static/images/en-us_image_0220702939.png
.. |image4| image:: /_static/images/en-us_image_0183134608.png
.. |image5| image:: /_static/images/en-us_image_0183134473.png
