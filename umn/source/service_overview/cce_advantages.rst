:original_name: cce_productdesc_0003.html

.. _cce_productdesc_0003:

CCE Advantages
==============

Product Advantages
------------------

CCE is a container service developed on Docker and Kubernetes. It offers a wide range of features that allow you to run containers in large clusters. CCE containers are highly reliable, have high performance, and compatible with open-source communities. They are ideal for enterprises.

**Easy to Use**

-  Creating CCE clusters (including CCE standard and Turbo clusters) is a breeze with just a few clicks on the web page. You can deploy VMs in a cluster.
-  CCE automates deployment and O&M of containerized applications throughout their lifecycle.
-  You can easily scale in or out cluster nodes and workloads on the console and flexibly combine auto scaling policies to handle ever-changing traffic surges.
-  The console walks you through the steps to upgrade Kubernetes clusters.
-  CCE seamlessly integrates with Helm charts and add-ons to provide an out-of-the-box user experience.

**High Performance**

-  CCE draws on years of field experience in compute, networking, storage, and heterogeneous infrastructure and provides you high-performance cluster services. You can concurrently launch containers at scale.
-  AI computing is 3 to 5 times better with NUMA BMSs and high-speed InfiniBand network cards.

**Highly Available and Secure**

-  HA: CCE supports three control plane nodes on the cluster management plane. These nodes run in different regions to ensure cluster HA. Nodes and workloads in a cluster can be deployed across AZs. This helps you build a multi-active architecture and ensures that the service system can run properly in the event of node faults, data center interruption, or natural disasters. This mechanism guarantees the stability of production environments and no service breakdowns.


   .. figure:: /_static/images/en-us_image_0000002516197259.png
      :alt: **Figure 1** Achieving cluster HA

      **Figure 1** Achieving cluster HA

-  Secure: Integrated with IAM and Kubernetes RBAC, CCE clusters are under your full control. You can assign different RBAC permissions for IAM users on the console.

   CCE offers secure runtimes so that each pod runs on a separate micro-VM, has its own OS kernel, and is securely isolated at the virtualization layer.

**Open and Compatible**

-  CCE uses Docker to simplify the management of containerized applications. It provides features such as deployment, resource scheduling, service discovery, and auto scaling. These features facilitate the management of containers in large clusters.
-  CCE is developed on Kubernetes. It is fully compatible with the native Kubernetes and Docker community versions, Kubernetes APIs, and kubectl. Updates from Kubernetes and Docker communities are regularly incorporated into CCE.

Comparative Analysis of CCE and On-Premises Kubernetes Clusters
---------------------------------------------------------------

.. table:: **Table 1** CCE clusters versus on-premises Kubernetes clusters

   +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Area of Focus         | On-Premises Kubernetes Cluster                                                                                                                                                                          | CCE Cluster                                                                                                                                                                                                                                                                                                                      |
   +=======================+=========================================================================================================================================================================================================+==================================================================================================================================================================================================================================================================================================================================+
   | Ease of use           | You have to handle all the complexity in deploying and managing Kubernetes clusters. Cluster upgrades are often a heavy burden to O&M personnel.                                                        | **Simplified and easy-to-use cluster management**                                                                                                                                                                                                                                                                                |
   |                       |                                                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                  |
   |                       |                                                                                                                                                                                                         | You can create and update a Kubernetes container cluster in a few clicks. There is no need to set up Docker or Kubernetes environments. CCE automates deployment and O&M of containerized applications throughout their lifecycle.                                                                                               |
   |                       |                                                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                  |
   |                       |                                                                                                                                                                                                         | CCE is deeply integrated with Helm charts to offer out-of-the-box usability.                                                                                                                                                                                                                                                     |
   |                       |                                                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                  |
   |                       |                                                                                                                                                                                                         | You only need to start a cluster and specify the tasks to run. CCE helps you manage the cluster and allows you to focus on developing containerized applications.                                                                                                                                                                |
   +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Scalability           | You have to assess service loads and cluster health before resizing a Kubernetes cluster.                                                                                                               | **Flexible cluster hosting and easy container scaling**                                                                                                                                                                                                                                                                          |
   |                       |                                                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                  |
   |                       |                                                                                                                                                                                                         | CCE automatically scales in or out cluster nodes and workloads based on resource usage. Multiple scaling policies can be flexibly combined to handle burst traffic during peak hours.                                                                                                                                            |
   +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Reliability           | There might be security vulnerabilities or configuration errors may occur in the OS of an on-premises Kubernetes cluster, which may cause security issues such as unauthorized access and data leakage. | **Enterprise-class security and reliability**                                                                                                                                                                                                                                                                                    |
   |                       |                                                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                  |
   |                       |                                                                                                                                                                                                         | CCE offers container-optimized OS images. Based on native Kubernetes clusters and compatible runtime versions, these images undergo additional stability testing and security hardening. They can minimize management overhead and operational risks while enhancing the reliability and security of containerized applications. |
   +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Efficiency            | You have to either build an image repository or turn to a third-party one. Images are pulled in serial.                                                                                                 | **Rapid deployment with images**                                                                                                                                                                                                                                                                                                 |
   |                       |                                                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                  |
   |                       |                                                                                                                                                                                                         | CCE works with SWR to pull images in parallel. This ensures faster image pull in high-concurrency scenarios and greatly improves container build efficiency.                                                                                                                                                                     |
   +-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Why Containerization?
---------------------

Docker is written in Go, a language designed by Google. It provides OS-level virtualization, including Linux cgroups, namespaces, and UnionFS (for example, AUFS), to isolate each software process. The isolated software processes, which are called containers, are independent from each other and from the host.

Docker further encapsulates containers, including file systems, network interconnection, and process isolation. This greatly simplifies container creation and management.

Traditional VM technology uses hypervisors to virtualize and allocate a host machine's hardware resources, such as memory, CPU, network, and disks. These virtualized resources are used to create separate VMs, each of which runs a complete OS independently. Every VM maintains its own system processes. On the contrary, a container does not require hardware resource virtualization or allocation. The application processes run directly in the OS kernel of the host node. This reduces the overhead of system processes, making Docker containers more lightweight and faster than VMs.


.. figure:: /_static/images/en-us_image_0000002483957322.png
   :alt: **Figure 2** Comparison between Docker containers and VMs

   **Figure 2** Comparison between Docker containers and VMs

To sum up, Docker containers have many advantages over VMs.

**More Efficient Use of System Resources**

Unlike VMs, containers do not require hardware resource virtualization or allocation or the overhead of running a complete OS. As a result, Docker offers higher system resource utilization. It enables faster application execution, lower memory overhead, and more efficient file storage operations. This lightweight architecture allows a single host with the same configuration as a traditional VM to run more containerized applications.

**Faster Startup**

It takes several minutes to start an application on a VM. Docker containers run on the host kernel without needing an independent OS. Containers can start in seconds or even milliseconds. Development, testing, and deployment can be much faster.

**Consistent Runtime Environment**

Different development, testing, and production environments sometimes prevent bug discovery before rollout. A Docker container image provides a complete runtime environment except the kernel for applications.

**Continuous Delivery and Deployment**

"Deploy once, run everywhere" would be great for DevOps personnel.

Docker supports continuous integration and continuous delivery/deployment (CI/CD) by allowing you to customize container images. Using Dockerfiles, developers can build images and run integration tests with CI. O&M personnel can easily deploy images in production environments, and even automate the deployment process with CD.

Dockerfiles provide transparency in image builds. They allow the development team to understand the application runtime environments and the O&M team to understand the necessary conditions for application stability in production environments. This ensures smoother deployment of images in production environments.

**Easier Application Migration**

Docker provides a consistent execution environment across many platforms, including physical machines, virtual machines, and laptops. Regardless of what platform Docker is running on, the applications run the same, which makes migrating them much easier. With Docker, you do not have to worry that an application running fine on one platform will fail in a different environment.

**Simpler Application Maintenance and Image Extension**

A Docker image is built up from a series of layers that are stacked. When you create a container, you add a container layer on top of image layers. In this way, duplicate layers are reused, which simplifies application maintenance and updates as well as image extension on base images. In addition, Docker collaborates with open-source project teams to maintain a large number of high-quality official images. You can directly use them in production environments or create your images based on these images. This greatly improves the efficiency in creating images for applications.

.. table:: **Table 2** Containers versus traditional VMs

   ==================== ======================= ===========
   Feature              Container               VM
   ==================== ======================= ===========
   Startup              In seconds              In minutes
   Disk capacity        MiB                     GiB
   Performance          Near-native             Poor
   Per-machine capacity Thousands of containers Tens of VMs
   ==================== ======================= ===========
