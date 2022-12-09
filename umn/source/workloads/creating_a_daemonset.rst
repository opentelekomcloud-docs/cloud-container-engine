:original_name: cce_01_0216.html

.. _cce_01_0216:

Creating a DaemonSet
====================

Scenario
--------

CCE provides deployment and management capabilities for multiple types of containers and supports features of container workloads, including creation, configuration, monitoring, scaling, upgrade, uninstall, service discovery, and load balancing.

DaemonSet ensures that only one pod runs on all or some nodes. When a node is added to a cluster, a new pod is also added for the node. When a node is removed from a cluster, the pod is also reclaimed. If a DaemonSet is deleted, all pods created by it will be deleted.

The typical application scenarios of a DaemonSet are as follows:

-  Run the cluster storage daemon, such as glusterd or Ceph, on each node.
-  Run the log collection daemon, such as Fluentd or Logstash, on each node.
-  Run the monitoring daemon, such as Prometheus Node Exporter, collectd, Datadog agent, New Relic agent, or Ganglia (gmond), on each node.

You can deploy a DaemonSet for each type of daemons on all nodes, or deploy multiple DaemonSets for the same type of daemons. In the second case, DaemonSets have different flags and different requirements on memory and CPU for different hardware types.

Prerequisites
-------------

You must have one cluster available before creating a DaemonSet. For details on how to create a cluster, see :ref:`Creating a CCE Cluster <cce_01_0028>`.

Procedure
---------

#. Log in to the CCE console.

#. In the navigation pane on the left, choose **Workloads** > **DaemonSets**. Click **Create DaemonSet** in the upper right corner of the page. Set basic workload parameters as described in :ref:`Table 1 <cce_01_0216__table18511927357>`. The parameters marked with an asterisk (*) are mandatory.

   .. _cce_01_0216__table18511927357:

   .. table:: **Table 1** Basic workload parameters

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                               |
      +===================================+===========================================================================================================================================================================================================================================+
      | \* Workload Name                  | Name of the containerized workload to be created. The name must be unique.                                                                                                                                                                |
      |                                   |                                                                                                                                                                                                                                           |
      |                                   | Enter 4 to 63 characters starting with a letter and ending with a letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.                                                                                           |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Cluster Name                   | Cluster to which the workload belongs.                                                                                                                                                                                                    |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Namespace                      | In a single cluster, data in different namespaces is isolated from each other. This enables applications to share the services of the same cluster without interfering each other. If no namespace is set, the default namespace is used. |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Time Zone Synchronization         | If this parameter is enabled, the container and the node use the same time zone.                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                           |
      |                                   | .. important::                                                                                                                                                                                                                            |
      |                                   |                                                                                                                                                                                                                                           |
      |                                   |    NOTICE:                                                                                                                                                                                                                                |
      |                                   |    After time zone synchronization is enabled, disks of the hostPath type will be automatically added and listed in the **Data Storage** > **Local Volume** area. Do not modify or delete the disks.                                      |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Description                       | Description of the workload.                                                                                                                                                                                                              |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Next: Add Container**.

   a. Click **Add Container** and select the image to be deployed.

      -  **My Images**: Create a workload using an image in the image repository you created.
      -  **Third-Party Images**: Create a workload using an image from any third-party image repository. When you create a workload using a third-party image, ensure that the node where the workload is running can access public networks. For details on how to create a workload using a third-party image, see :ref:`Using a Third-Party Image <cce_01_0009>`.

         -  If your image repository does not require authentication, set **Secret Authentication** to **No**, enter an image pull address, and then click **OK**.
         -  If your image repository must be authenticated (account and password), you need to create a secret and then use a third-party image. For details, see :ref:`Using a Third-Party Image <cce_01_0009>`.

      -  **Shared Images**: Create a workload using an image shared by another tenant through the SWR service.

   b. Configure basic image information.

      A workload is an abstract model of a group of pods. One pod can encapsulate one or more containers. You can click **Add Container** in the upper right corner to add multiple container images and set them separately.

      .. table:: **Table 2** Image parameters

         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                  |
         +===================================+==============================================================================================================================================================================================================================================================================================================+
         | Image Name                        | Name of the image. You can click **Change Image** to update it.                                                                                                                                                                                                                                              |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | \*Image Version                   | Select the image tag to be deployed.                                                                                                                                                                                                                                                                         |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | \*Container Name                  | Name of the container. You can modify it.                                                                                                                                                                                                                                                                    |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Privileged Container              | Programs in a privileged container have certain privileges.                                                                                                                                                                                                                                                  |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | If **Privileged Container** is **On**, the container is granted superuser permissions. For example, privileged containers can manipulate network devices on the host machine and modify kernel parameters.                                                                                                   |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Container Resources               | **CPU**                                                                                                                                                                                                                                                                                                      |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | -  **Request**: minimum number of CPU cores required by a container. The default value is 0.25 cores.                                                                                                                                                                                                        |
         |                                   | -  **Limit**: maximum number of CPU cores available for a container. Do not leave **Limit** unspecified. Otherwise, intensive use of container resources will occur and your workload may exhibit unexpected behavior.                                                                                       |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | **Memory**                                                                                                                                                                                                                                                                                                   |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | -  **Request**: minimum amount of memory required by a container. The default value is 512 MiB.                                                                                                                                                                                                              |
         |                                   | -  **Limit**: maximum amount of memory available for a container. When memory usage exceeds the specified memory limit, the container will be terminated.                                                                                                                                                    |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | For more information about **Request** and **Limit**, see :ref:`Setting Container Specifications <cce_01_0163>`.                                                                                                                                                                                             |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | **GPU**: configurable only when the cluster contains GPU nodes.                                                                                                                                                                                                                                              |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | It indicates the percentage of GPU resources reserved for a container. Select **Use** and set the percentage. For example, if this parameter is set to 10%, the container is allowed to use 10% of GPU resources. If you do not select **Use** or set this parameter to **0**, no GPU resources can be used. |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | **GPU/Graphics Card**: The workload's pods will be scheduled to the node with the specified GPU.                                                                                                                                                                                                             |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | If **Any GPU type** is selected, the container uses a random GPU in the node. If you select a specific GPU, the container uses this GPU accordingly.                                                                                                                                                         |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   c. **Lifecycle**: Commands for starting and running containers can be set.

      -  **Start Command**: executed when the workload is started. For details, see :ref:`Setting Container Startup Commands <cce_01_0008>`.
      -  **Post-Start**: executed after the workload runs successfully. For more information, see :ref:`Setting Container Lifecycle Parameters <cce_01_0105>`.
      -  **Pre-Stop**: executed to delete logs or temporary files before the workload ends. For more information, see :ref:`Setting Container Lifecycle Parameters <cce_01_0105>`.

   d. **Health Check**: CCE provides two types of probes: liveness probe and readiness probe. They are used to determine whether containers and user services are running properly. For more information, see :ref:`Setting Health Check for a Container <cce_01_0112>`.

      -  **Liveness Probe**: used to restart the unhealthy container.
      -  **Readiness Probe**: used to change the container to the unready state when detecting that the container is unhealthy. In this way, service traffic will not be directed to the container.

   e. **Environment Variables**: Environment variables can be added to a container. In general, environment variables are used to set parameters.

      On the **Environment Variables** tab page, click **Add Environment Variable**. Currently, three types of environment variables are supported:

      -  **Added manually**: Set **Variable Name** and **Variable Value/Reference**.
      -  **Added from Secret**: Set **Variable Name** and select the desired secret name and data. A secret must be created in advance. For details, see :ref:`Creating a Secret <cce_01_0153>`.
      -  **Added from ConfigMap**: Set **Variable Name** and select the desired ConfigMap name and data. A ConfigMap must be created in advance. For details, see :ref:`Creating a ConfigMap <cce_01_0152>`.

         .. note::

            To edit an environment variable that has been set, click **Edit**. To delete an environment variable that has been set, click **Delete**.

   f. **Data Storage**: Data storage can be mounted to containers for persistent storage and high disk I/O. Local volume and cloud storage are supported. For details, see :ref:`Storage (CSI) <cce_01_0042>`.

      .. note::

         Currently, cloud storage cannot be mounted to secure (Kata) containers in a CCE Turbo cluster.

   g. **Security Context**: Container permissions can be configured to protect CCE and other containers from being affected.

      Enter the user ID to set container permissions and prevent systems and other containers from being affected.

   h. **Log Policies**: Log collection policies and log directory can be configured to collect container logs for unified management and analysis. For details, see :ref:`Container Logs <cce_01_0018>`.

#. Click **Next: Set Application Access**. Then, click **Add Service** and set the workload access type.

   If your workload will be reachable to other workloads or public networks, add a Service to define the workload access type.

   The workload access type determines the network attributes of the workload. Workloads with different access types can provide different network capabilities. For details, see :ref:`Overview <cce_01_0010>`.

#. Click **Next: Configure Advanced Settings** to configure advanced policies.

   -  **Upgrade Policy**:

      -  **Upgrade Mode**: Only **Rolling upgrade** is supported. During a rolling upgrade, old pods are gradually replaced with new ones. During the upgrade, service traffic is evenly distributed to both pods to ensure service continuity.
      -  **Maximum Number of Unavailable Pods**: Maximum number of unavailable pods allowed in a rolling upgrade. If the number is equal to the total number of pods, services may be interrupted. Minimum number of alive pods = Total pods - Maximum number of unavailable pods

   -  **Graceful Deletion**:

      **Graceful Time Window**: Enter the time. The graceful scale-in policy provides a time window for workload deletion and is reserved for executing commands in the PreStop phase in the lifecycle. If workload processes are not terminated after the time window elapses, the workload will be forcibly deleted.

   -  **Scheduling Policies**: You can combine static global scheduling policies or dynamic runtime scheduling policies as required. For details, see :ref:`Scheduling Policy Overview <cce_01_0051>`.

   -  **Advanced Pod Settings**

      -  **Pod Label**: The built-in **app** label is specified when the workload is created. It is used to set affinity and anti-affinity scheduling and cannot be modified. You can click **Add Label** to add labels.


      .. figure:: /_static/images/en-us_image_0220765374.png
         :alt: **Figure 1** Advanced pod settings

         **Figure 1** Advanced pod settings

   -  **Client DNS Configuration**: A CCE cluster has a built-in DNS add-on (CoreDNS) to provide domain name resolution for workloads in the cluster.

      -  **DNS Policy**

         -  **ClusterFirst**: The default DNS configuration overrides the **Nameserver** and **DNS Search Domain** configurations of the client.
         -  **None**: Only the **Nameserver** and **DNS Search Domain** configurations are used for domain name resolution.
         -  **Default**: The pod inherits the DNS configuration from the node on which the pod runs.

      -  **Nameserver**: You can configure a domain name server for a user-defined domain name. The value is one or a group of DNS IP addresses, for example, 1.2.3.4.
      -  **DNS Search Domain**: a search list for host-name lookup. When a domain name cannot be resolved, DNS queries will be attempted combining the domain name with each domain in the search list in turn until a match is found or all domains in the search list are tried.
      -  **Timeout (s)**: amount of time the resolver will wait for a response from a remote name server before retrying the query on a different name server. Set it based on the site requirements.
      -  **ndots**: threshold for the number of dots that must appear in a domain name before an initial absolute query will be made. If a domain name has **ndots** or more than **ndots** dots, the name is a fully qualified domain name (FQDN) and will be tried first as an absolute name. If a domain name has less than **ndots** dots, the operating system will look up the name in a list of search domain names.

#. After the preceding configurations are complete, click **Create**. On the page displayed, click **Return to Workload List** to view the workload status.

   If the workload is in the **Running** state, it has been successfully created.

   Workload status is not updated in real time. Click |image1| in the upper right corner or press **F5** to refresh the page.

.. |image1| image:: /_static/images/en-us_image_0183674977.png
