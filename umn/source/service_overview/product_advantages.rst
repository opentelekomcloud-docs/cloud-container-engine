:original_name: cce_productdesc_0003.html

.. _cce_productdesc_0003:

Product Advantages
==================

Why CCE?
--------

CCE is a container service built on Docker and Kubernetes. A wealth of features enable you to run container clusters at scale. CCE eases containerization thanks to its reliability, performance, and open source engagement.

**Easy to Use**

-  Creating a Kubernetes cluster is as easy as a few clicks on the web console. You can deploy and manage VMs and BMSs together.
-  CCE automates deployment and O&M of containerized applications throughout their lifecycle.
-  You can resize clusters and workloads by setting auto scaling policies. In-the-moment load spikes are no longer headaches.
-  The console walks you through the steps to upgrade Kubernetes clusters.
-  CCE supports turnkey Helm charts.

**High Performance**

-  CCE runs on mature IaaS services and heterogeneous compute resources. You can launch containers at scale.
-  AI computing is 3x to 5x better with NUMA BMSs and high-speed InfiniBand network cards.

**Highly Available and Secure**

-  HA: Three master nodes in different AZs for your cluster control plane. Multi-active DR for your nodes and workloads. All these ensure service continuity when one of the nodes is down or an AZ gets hit by natural disasters.


   .. figure:: /_static/images/en-us_image_0000001550365685.png
      :alt: **Figure 1** High-availability setup of clusters

      **Figure 1** High-availability setup of clusters

-  Secure: Integrating IAM and Kubernetes RBAC, CCE clusters are under your full control. You can set different RBAC permissions for IAM users on the console.

**Open and Compatible**

-  CCE runs on Docker that automates container deployment, discovery, scheduling, and scaling.
-  CCE is compatible with native Kubernetes APIs and kubectl. Updates from Kubernetes and Docker communities are regularly incorporated into CCE.

Comparative Analysis of CCE and On-Premises Kubernetes Cluster Management Systems
---------------------------------------------------------------------------------

.. table:: **Table 1** CCE clusters versus on-premises Kubernetes clusters

   +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Area of Focus         | On-Premises Cluster                                                                                                                              | CCE                                                                                                                                                                                                            |
   +=======================+==================================================================================================================================================+================================================================================================================================================================================================================+
   | Ease of use           | You have to handle all the complexity in deploying and managing Kubernetes clusters. Cluster upgrades are often a heavy burden to O&M personnel. | **Easy to manage and use clusters**                                                                                                                                                                            |
   |                       |                                                                                                                                                  |                                                                                                                                                                                                                |
   |                       |                                                                                                                                                  | You can create a Kubernetes container cluster in a few clicks. No need to set up Docker or Kubernetes environments. CCE automates deployment and O&M of containerized applications throughout their lifecycle. |
   |                       |                                                                                                                                                  |                                                                                                                                                                                                                |
   |                       |                                                                                                                                                  | CCE supports turnkey Helm charts.                                                                                                                                                                              |
   |                       |                                                                                                                                                  |                                                                                                                                                                                                                |
   |                       |                                                                                                                                                  | Using CCE is as simple as choosing a cluster and the workloads that you want to run in the cluster. CCE takes care of cluster management and you focus on app development.                                     |
   +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Scalability           | You have to assess service loads and cluster health before resizing a cluster.                                                                   | **Managed scaling service**                                                                                                                                                                                    |
   |                       |                                                                                                                                                  |                                                                                                                                                                                                                |
   |                       |                                                                                                                                                  | CCE auto scales clusters and workloads according to resource metrics and scaling policies.                                                                                                                     |
   +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Reliability           | Only one master node is available in a cluster. Once this node is down, the entire cluster is down, as well as all the applications in it.       | **High availability**                                                                                                                                                                                          |
   |                       |                                                                                                                                                  |                                                                                                                                                                                                                |
   |                       |                                                                                                                                                  | Enabling HA when creating a cluster will create three master nodes for the control plane. Single points of failure (SPOFs) will not shut down your cluster.                                                    |
   +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Efficiency            | You have to either build an image repository or turn to a third-party one. Images are pulled in serial.                                          | **Rapid deployment with images**                                                                                                                                                                               |
   |                       |                                                                                                                                                  |                                                                                                                                                                                                                |
   |                       |                                                                                                                                                  | CCE connects to SWR to pull images in parallel. Faster pulls, faster container build.                                                                                                                          |
   +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Cost                  | Heavy upfront investment in installing, managing, and scaling cluster management infrastructure                                                  | **Cost effective**                                                                                                                                                                                             |
   |                       |                                                                                                                                                  |                                                                                                                                                                                                                |
   |                       |                                                                                                                                                  | You only pay for master nodes and the resources used to run and manage applications.                                                                                                                           |
   +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Why Containers?
---------------

Docker is written in the Go language designed by Google. It provides operating-system-level virtualization. Linux Control Groups (cgroups), namespaces, and UnionFS (for example, AUFS) isolate each software process. A Docker container packages everything needed to run a software process. Containers are independent from each other and from the host.

Docker has moved forward to enhance container isolation. Containers have their own file systems. They cannot see each other's processes or network interfaces. This simplifies container creation and management.

VMs use a hypervisor to virtualize and allocate hardware resources (such as memory, CPU, network, and disk) of a host machine. A complete operating system runs on a VM. Each VM needs to run its own system processes. On the contrary, a container does not require hardware resource virtualization. It runs an application process directly in the the host machine OS kernel. No resource overheads are incurred by running system processes. Therefore, Docker is lighter and faster than VMs.


.. figure:: /_static/images/en-us_image_0000001499406022.png
   :alt: **Figure 2** Comparison between Docker containers and VMs

   **Figure 2** Comparison between Docker containers and VMs

To sum up, Docker containers have many advantages over VMs.

**Resource use**

Containers have no overheads for virtualizing hardware and running a complete OS. They are faster than VMs in execution and file storage, while having no memory loss.

**Start speed**

It takes several minutes to start an application on a VM. Docker containers run on the host kernel without needing an independent OS. Apps in containers can start in seconds or even milliseconds. Development, testing, and deployment can be much faster.

**Consistent environment**

Different development, testing, and production environments sometimes prevent bug discovery before rollout. A Docker container image includes everything needed to run an application. You can deploy the same copy of configurations in different environments.

**Continuous delivery and deployment**

"Deploy once, run everywhere" would be great for DevOps personnel.

Docker supports CI/CD by allowing you to customize container images. You compile Dockerfiles to build container images and use CI systems for testing. The Ops team can deploy images into production environments and use CD systems for auto deployment.

The use of Dockerfiles makes the DevOps process visible to everyone in a DevOps team. Developers can better understand both user needs and the O&M headaches faced by the Ops team. The Ops team can also have some knowledge of the must-met conditions to run the application. The knowledge is helpful when the Ops personnel deploy container images in production.

**Portability**

Docker ensures environmental consistency across development, testing, and production. Portable Docker containers work the same, regardless of their running environments. Physical machines, VMs, public clouds, private clouds, or even laptops, you name it. Apps are now free to migrate and run anywhere.

**Application update**

Docker images consist of layers. Each layer is only stored once and different images can contain the exact same layers. When transferring such images, those same layers get transferred only once. This makes distribution efficient. Updating a containerized application is also simple. Either edit the top-most writable layer in the final image or add layers to the base image. Docker joins hands with many open source projects to maintain a variety of high-quality official images. You can directly use them in the production environment or easily build new images based on them.

.. table:: **Table 2** Containers versus traditional VMs

   ==================== ======================= ===========
   Feature              Containers              VMs
   ==================== ======================= ===========
   Start speed          In seconds              In minutes
   Disk capacity        MB                      GB
   Performance          Near-native performance Weak
   Per-machine capacity Thousands of containers Tens of VMs
   ==================== ======================= ===========
