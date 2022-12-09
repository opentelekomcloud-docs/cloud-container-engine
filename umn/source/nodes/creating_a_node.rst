:original_name: cce_01_0033.html

.. _cce_01_0033:

Creating a Node
===============

Scenario
--------

A node is a virtual or physical machine that provides computing resources. Sufficient nodes must be available in your project to ensure that operations, such as creating workloads, can be performed.

Prerequisites
-------------

-  At least one cluster is available. For details on how to create a cluster, see :ref:`Creating a CCE Cluster <cce_01_0028>`.
-  A key pair has been created. The key pair will be used for identity authentication upon remote node login.

Notes and Constraints
---------------------

-  During the node creation, software packages are downloaded from OBS using the domain name. You need to use a private DNS server to resolve the OBS domain name, and configure the subnet where the node resides with a private DNS server address. When you create a subnet, the private DNS server is used by default. If you change the subnet DNS, ensure that the DNS server in use can resolve the OBS domain name.
-  Only KVM nodes can be created. Non-KVM nodes cannot be used after being created.
-  Once a node is created, its AZ cannot be changed.
-  CCE supports GPUs through an add-on named :ref:`gpu-beta <cce_01_0141>`. You need to install this add-on to use GPU-enabled nodes in your cluster.

Procedure
---------

#. Log in to the CCE console. Use either of the following methods to add a node:

   -  In the navigation pane, choose **Resource Management** > **Nodes**. Select the cluster to which the node will belong and click **Create** **Node** on the upper part of the node list page.
   -  In the navigation pane, choose **Resource Management** > **Clusters**. In the card view of the cluster to which you will add nodes, click **Create** **Node**.

#. Select a region and an AZ.

   -  **Current Region**: geographic location of the nodes to be created.

   -  **AZ**: Set this parameter based on the site requirements. An AZ is a physical region where resources use independent power supply and networks. AZs are physically isolated but interconnected through an internal network.

      You are advised to deploy worker nodes in different AZs after the cluster is created to make your workloads more reliable. When creating a cluster, you can deploy nodes only in one AZ.

#. Configure node parameters.

   -  **Node Type**

      -  **VM node**: A VM node will be created in the cluster.

   -  **Node Name**: Enter a node name. A node name contains 1 to 56 characters starting with a lowercase letter and not ending with a hyphen (-). Only lowercase letters, digits, and hyphens (-) are allowed.

   -  **Specifications**: Select node specifications that best fit your business needs.

      -  **General-purpose**: provides a balance of computing, memory, and network resources. It is a good choice for many applications, such as web servers, workload development, workload testing, and small-scale databases.
      -  **Memory-optimized**: provides higher memory capacity than general-purpose nodes and is suitable for relational databases, NoSQL, and other workloads that are both memory-intensive and data-intensive.
      -  **GPU-accelerated**: provides powerful floating-point computing and is suitable for real-time, highly concurrent massive computing. Graphical processing units (GPUs) of P series are suitable for deep learning, scientific computing, and CAE. GPUs of G series are suitable for 3D animation rendering and CAD. **GPU-accelerated nodes can be created only in clusters of v1.11 or later**. GPU-accelerated nodes are available only in certain regions.
      -  **General computing-plus**: provides stable performance and exclusive resources to enterprise-class workloads with high and stable computing performance.
      -  **Disk-intensive**: supports :ref:`local disk storage <cce_01_0053>` and provides high network performance. It is designed for workloads requiring high throughput and data switching, such as big data workloads.

      To ensure node stability, CCE automatically reserves some resources to run necessary system components. For details, see :ref:`Formula for Calculating the Reserved Resources of a Node <cce_01_0178>`.

   -  **OS**: Select an OS for the node to be created.

      Reinstalling the OS or modifying OS configurations could make the node unavailable. Exercise caution when performing these operations.

   -  **System Disk**: Set the system disk space of the worker node. The value ranges from 40GB to 1024 GB. The default value is 40GB.

      By default, system disks support Common I/O (SATA), High I/O (SAS), and Ultra-high I/O (SSD)High I/O (SAS) and Ultra-high I/O (SSD) EVS disks.

      **Encryption**: Data disk encryption safeguards your data. Snapshots generated from encrypted disks and disks created using these snapshots automatically inherit the encryption function. **This function is available only in certain regions.**

      -  **Encryption** is not selected by default.
      -  After you select **Encryption**, you can select an existing key in the displayed **Encryption Setting** dialog box. If no key is available, click the link next to the drop-down box to create a key. After the key is created, click the refresh icon.

   -  .. _cce_01_0033__li12223421320:

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

      -  **Data disk space allocation**: Click |image1| to specify the resource ratio for **Kubernetes Space** and **User Space**. Disk space of the data disks managed by LVM will be allocated according to the ratio you set. This function is available only to clusters of certain versions.

         -  **Kubernetes Space**: You can specify the ratio of the data disk space for storing Docker and kubelet resources. Docker resources include the Docker working directory, Docker images, and image metadata. kubelet resources include pod configuration files, secrets, and emptyDirs.

            The Docker space cannot be less than 10%, and the space size cannot be less than 60 GB. The kubelet space cannot be less than 10%.

            The Docker space size is determined by your service requirements. For details, see :ref:`Data Disk Space Allocation <cce_01_0341>`.

         -  **User Space**: You can set the ratio of the disk space that is not allocated to Kubernetes resources and the path to which the user space is mounted.

            .. note::

               Note that the mount path cannot be **/**, **/home/paas**, **/var/paas**, **/var/lib**, **/var/script**, **/var/log**, **/mnt/paas**, or **/opt/cloud**, and cannot conflict with the system directories (such as **bin**, **lib**, **home**, **root**, **boot**, **dev**, **etc**, **lost+found**, **mnt**, **proc**, **sbin**, **srv**, **tmp**, **var**, **media**, **opt**, **selinux**, **sys**, and **usr**). Otherwise, the system or node installation will fail.

      **If the cluster version is v1.13.10-r0 or later and the node specification is Disk-intensive, the following options are displayed for data disks:**

      -  **EVS**: Parameters are the same as those when the node type is not Disk-intensive. For details, see :ref:`Data Disk <cce_01_0033__li12223421320>` above.

      -  **Local disk**: Local disks may break down and do not ensure data reliability. It is recommended that you store service data in EVS disks, which are more reliable than local disks.

         Local disk parameters are as follows:

         -  **Disk Mode**: If the node type is **disk-intensive**, the supported disk mode is HDD.
         -  **Read/Write Mode**: When multiple local disks exist, you can set the read/write mode. The serial and sequential modes are supported. **Sequential** indicates that data is read and written in linear mode. When a disk is used up, the next disk is used. **Serial** indicates that data is read and written in striping mode, allowing multiple local disks to be read and written at the same time.
         -  **Kubernetes Space**: You can specify the ratio of the data disk space for storing Docker and kubelet resources. Docker resources include the Docker working directory, Docker images, and image metadata. kubelet resources include pod configuration files, secrets, and emptyDirs.
         -  **User Space**: You can set the ratio of the disk space that is not allocated to Kubernetes resources and the path to which the user space is mounted.

      .. important::

         -  The ratio of disk space allocated to the Kubernetes space and user space must be equal to 100% in total. You can click |image2| to refresh the data after you have modified the ratio.
         -  By default, disks run in the direct-lvm mode. If data disks are removed, the loop-lvm mode will be used and this will impair system stability.

   -  **VPC**: A VPC where the current cluster is located. This parameter cannot be changed and is displayed only for clusters of v1.13.10-r0 or later.

   -  **Subnet**: A subnet improves network security by providing exclusive network resources that are isolated from other networks. You can select any subnet in the cluster VPC. Cluster nodes can belong to different subnets.

      During the node creation, software packages are downloaded from OBS using the domain name. You need to use a private DNS server to resolve the OBS domain name, and configure the subnet where the node resides with a private DNS server address. When you create a subnet, the private DNS server is used by default. If you change the subnet DNS, ensure that the DNS server in use can resolve the OBS domain name.

#. **EIP**: an independent public IP address. If the nodes to be created require public network access, select **Automatically assign** or **Use existing**.

   An EIP bound to the node allows public network access. EIP bandwidth can be modified at any time. An ECS without a bound EIP cannot access the Internet or be accessed by public networks.

   -  **Do not use**: A node without an EIP cannot be accessed from public networks. It can be used only as a cloud server for deploying services or clusters on a private network.

   -  **Automatically assign**: An EIP with specified configurations is automatically assigned to each node. If the number of EIPs is smaller than the number of nodes, the EIPs are randomly bound to the nodes.

      Configure the EIP specifications, billing factor, bandwidth type, and bandwidth size as required. When creating an ECS, ensure that the elastic IP address quota is sufficient.

   -  **Use existing**: Existing EIPs are assigned to the nodes to be created.

   .. note::

      By default, VPC's SNAT feature is disabled for CCE. If SNAT is enabled, you do not need to use EIPs to access public networks. For details about SNAT, see :ref:`Custom Policies <cce_01_0188__section1437818291149>`.

#. **Login Mode**:

   -  **Key pair**: Select the key pair used to log in to the node. You can select a shared key.

      A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create a key pair**.

      .. important::

         When creating a node using a key pair, IAM users can select only the key pairs created by their own, regardless of whether these users are in the same group. For example, user B cannot use the key pair created by user A to create a node, and the key pair is not displayed in the drop-down list on the CCE console.

#. **Advanced ECS Settings** (optional): Click |image3| to show advanced ECS settings.

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

#. **Advanced Kubernetes Settings**: (Optional) Click |image4| to show advanced cluster settings.

   -  **Max Pods**: maximum number of pods that can be created on a node, including the system's default pods. If the cluster uses the **VPC network model**, the maximum value is determined by the number of IP addresses that can be allocated to containers on each node.

      This limit prevents the node from being overloaded by managing too many pods. For details, see :ref:`Maximum Number of Pods That Can Be Created on a Node <cce_01_0348>`.

   -  **Maximum Data Space per Container**: maximum data space that can be used by a container. The value ranges from 10 GB to 500 GB. If the value of this field is larger than the data disk space allocated to Docker resources, the latter will override the value specified here. Typically, 90% of the data disk space is allocated to Docker resources. This parameter is displayed only for clusters of v1.13.10-r0 and later.

#. **Nodes**: The value cannot exceed the management scale you select when configuring cluster parameters. Set this parameter based on service requirements and the remaining quota displayed on the page. Click |image5| to view the factors that affect the number of nodes to be added (depending on the factor with the minimum value).

#. Click **Next: Confirm**. After confirming that the configuration is correct, click **Submit**.

   The node list page is displayed. If the node status is **Available**, the node is added successfully. It takes about 6 to 10 minutes to create a node.

   .. note::

      -  Do not delete the security groups and related rules automatically configured during cluster creation. Otherwise, the cluster will exhibit unexpected behavior.

#. Click **Back to Node List**. The node has been created successfully if it changes to the **Available** state.

   .. note::

      The allocatable resources are calculated based on the resource request value (**Request**), which indicates the upper limit of resources that can be requested by pods on this node, but does not indicate the actual available resources of the node.

      The calculation formula is as follows:

      -  Allocatable CPUs = Total CPUs - Requested CPUs of all pods - Reserved CPUs for other resources
      -  Allocatable memory = Total memory - Requested memory of all pods - Reserved memory for other resources

.. |image1| image:: /_static/images/en-us_image_0273156799.png
.. |image2| image:: /_static/images/en-us_image_0220702939.png
.. |image3| image:: /_static/images/en-us_image_0183134608.png
.. |image4| image:: /_static/images/en-us_image_0183134479.png
.. |image5| image:: /_static/images/en-us_image_0250508826.png
