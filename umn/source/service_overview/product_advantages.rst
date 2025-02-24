:original_name: cce_productdesc_0003.html

.. _cce_productdesc_0003:

Product Advantages
==================

Why CCE?
--------

CCE is a container service developed on Docker and Kubernetes. It offers a wide range of features that allow you to run containers on a large scale. CCE containers are highly reliable, have high-performance, and compatible with open-source communities, making them an ideal choice for enterprise needs.

**Easy to Use**

-  Creating CCE clusters (CCE standard, CCE Turbo, and CCE Autopilot) is a breeze with just a few clicks on the web page. You can deploy VMs in a cluster.
-  CCE automates deployment and O&M of containerized applications throughout their lifecycle.
-  You can resize clusters and workloads by setting auto scaling policies. In-the-moment load spikes are no longer headaches.
-  The console walks you through the steps to upgrade Kubernetes clusters.
-  CCE seamlessly integrates with Helm charts and add-ons to provide an out-of-the-box user experience.

**High Performance**

-  CCE draws on years of field experience in compute, networking, storage, and heterogeneous infrastructure and provides you high-performance cluster services. You can concurrently launch containers at scale.
-  AI computing is 3x to 5x better with NUMA BMSs and high-speed InfiniBand network cards.

**Highly Available and Secure**

-  HA: Three master nodes in different AZs for your cluster control plane. Multi-active DR for your nodes and workloads. All these ensure service continuity when one of the nodes is down or an AZ gets hit by natural disasters.


   .. figure:: /_static/images/en-us_image_0000002065477978.png
      :alt: **Figure 1** Achieving cluster HA

      **Figure 1** Achieving cluster HA

-  Secure: Integrating IAM and Kubernetes RBAC, CCE clusters are under your full control. You can set different RBAC permissions for IAM users on the console.

   CCE offers secure containers so that each pod runs on a separate micro-VM, has its own OS kernel, and is securely isolated at the virtualization layer.

**Open and Compatible**

-  CCE uses Docker to simplify the management of containerized applications by providing features such as deployment, resource scheduling, networking, and auto scaling.
-  CCE is compatible with native Kubernetes APIs and kubectl. Updates from Kubernetes and Docker communities are regularly incorporated into CCE.

Comparative Analysis of CCE and On-Premises Kubernetes Cluster Management Systems
---------------------------------------------------------------------------------

.. table:: **Table 1** CCE clusters versus on-premises Kubernetes clusters

   +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Area of Focus         | On-Premises Kubernetes Cluster                                                                                                                                                                          | CCE Cluster                                                                                                                                                                                                                                                                                                |
   +=======================+=========================================================================================================================================================================================================+============================================================================================================================================================================================================================================================================================================+
   | Ease of use           | You have to handle all the complexity in deploying and managing Kubernetes clusters. Cluster upgrades are often a heavy burden to O&M personnel.                                                        | **Easy to manage and use clusters**                                                                                                                                                                                                                                                                        |
   |                       |                                                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                            |
   |                       |                                                                                                                                                                                                         | You can create and update a Kubernetes container cluster in a few clicks. There is no need to set up Docker or Kubernetes environments. CCE automates deployment and O&M of containerized applications throughout their lifecycle.                                                                         |
   |                       |                                                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                            |
   |                       |                                                                                                                                                                                                         | CCE supports turnkey Helm charts.                                                                                                                                                                                                                                                                          |
   |                       |                                                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                            |
   |                       |                                                                                                                                                                                                         | Using CCE is as simple as choosing a cluster and the workloads that you want to run in the cluster. CCE takes care of cluster management and you focus on app development.                                                                                                                                 |
   +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Scalability           | You have to assess service loads and cluster health before resizing a Kubernetes cluster.                                                                                                               | **Managed scaling service**                                                                                                                                                                                                                                                                                |
   |                       |                                                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                            |
   |                       |                                                                                                                                                                                                         | CCE auto scales clusters and workloads according to resource metrics and scaling policies.                                                                                                                                                                                                                 |
   +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Reliability           | There might be security vulnerabilities or configuration errors may occur in the OS of an on-premises Kubernetes cluster, which may cause security issues such as unauthorized access and data leakage. | **Enterprise-class security and reliability**                                                                                                                                                                                                                                                              |
   |                       |                                                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                            |
   |                       |                                                                                                                                                                                                         | CCE offers container-optimized OS images that have undergone additional stability tests and security hardening. These images are based on native Kubernetes clusters and runtime versions, resulting in reduced management costs and risks, as well as improved reliability and security for applications. |
   +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Efficiency            | You have to either build an image repository or turn to a third-party one. Images are pulled in serial.                                                                                                 | **Rapid deployment with images**                                                                                                                                                                                                                                                                           |
   |                       |                                                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                            |
   |                       |                                                                                                                                                                                                         | CCE connects to SWR to pull images in parallel. Faster pulls, faster container build.                                                                                                                                                                                                                      |
   +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Why Containerization?
---------------------

Docker is written in the Go language designed by Google. It provides operating-system-level virtualization. Linux Control Groups (cgroups), namespaces, and UnionFS (for example, AUFS) isolate each software process. The isolated software processes, which are called containers, are independent from each other and from the host.

Docker has moved forward to enhance container isolation. Containers have their own file systems. They cannot see each other's processes or network interfaces. This simplifies container creation and management.

VMs use a hypervisor to virtualize and allocate hardware resources (such as memory, CPU, network, and disk) of a host machine. A complete operating system runs on a VM. Each VM needs to run its own system processes. On the contrary, a container does not require hardware resource virtualization. It runs an application process directly in the host machine OS kernel. No resource overheads are incurred by running system processes. Therefore, Docker is lighter and faster than VMs.


.. figure:: /_static/images/en-us_image_0000002101676373.png
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

Docker ensures environmental consistency across development, testing, and production. Portable Docker containers work the same, regardless of their running environments. Physical machines, VMs, or even laptops, you name it. Apps are now free to migrate and run anywhere.

**Application update**

A Docker image is built up from a series of layers and these layers are stacked. When you create a new container, you add a container layer on top of image layers. In this way, duplicate layers are reused, which simplify application maintenance and update as well as further image extension on base images. Docker joins hands with many open source projects to maintain a variety of high-quality official images. You can directly use them in the production environment or easily build new images based on them.

.. table:: **Table 2** Containers versus traditional VMs

   ==================== ======================= ===========
   Feature              Containers              VMs
   ==================== ======================= ===========
   Start speed          In seconds              In minutes
   Disk capacity        MB                      GB
   Performance          Near-native performance Weak
   Per-machine capacity Thousands of containers Tens of VMs
   ==================== ======================= ===========
