:original_name: cce_01_0048.html

.. _cce_01_0048:

Creating a StatefulSet
======================

Scenario
--------

StatefulSets are a type of workloads whose data or status is stored while they are running. For example, MySQL is a StatefulSet because it needs to store new data.

A container can be migrated between different hosts, but data is not stored on the hosts. To store StatefulSet data persistently, attach HA storage volumes provided by CCE to the container.

Prerequisites
-------------

-  Before creating a workload, you must have an available cluster. For details on how to create a cluster, see :ref:`Creating a CCE Cluster <cce_01_0028>`.
-  To enable public access to a workload, ensure that an EIP or load balancer has been bound to at least one node in the cluster.

   .. note::

      If a pod has multiple containers, ensure that the ports used by the containers do not conflict with each other. Otherwise, creating the StatefulSet will fail.

Using the CCE Console
---------------------

CCE provides multiple methods for creating a workload. You can use any of the following methods:

#. Use an image in Third-Party Images. You do not need to upload any image before using it.
#. Use an image that you have uploaded to SWR.
#. Use a shared image to create a workload. Specifically, other tenants share an image with you by using the SWR service.
#. Use a YAML file to create a workload. You can click **Create YAML** on the right of the **Create StatefulSet** page. For details about YAML, see :ref:`Using kubectl <cce_01_0048__section113441881214>`. After the YAML file is written, click **Create** to create a workload.

   .. note::

      Settings in the YAML file are synchronized with those on the console. You can edit the YAML file on the console to create a workload. For example:

      -  If you enter a workload name on the console, the name will automatically appear in the YAML file.
      -  If you add an image on the console, the image will be automatically added to the YAML file.

      When you click **Create YAML** on the right of the console, do not create multiple YAML files in the YAML definition pane displayed. You need to create them one by one. Otherwise, an error will be reported during the creation.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **StatefulSets**. On the displayed page, click **Create StatefulSet**. Set basic workload parameters as described in :ref:`Table 1 <cce_01_0048__table12741447488>`. The parameters marked with an asterisk (*) are mandatory.

   .. _cce_01_0048__table12741447488:

   .. table:: **Table 1** Basic workload parameters

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                  |
      +===================================+==============================================================================================================================================================================================================================================================================================+
      | \* Workload Name                  | Name of a workload, which must be unique.                                                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                              |
      |                                   | Enter 4 to 52 characters starting with a lowercase letter and ending with a letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.                                                                                                                                    |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Cluster Name                   | Cluster to which the workload belongs.                                                                                                                                                                                                                                                       |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Namespace                      | In a single cluster, data in different namespaces is isolated from each other. This enables applications to share the services of the same cluster without interfering each other. If no namespace is set, the **default** namespace is used.                                                |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Instances                      | Number of pods in a workload. A workload can have one or more pods. The default value is **2**. You can customize the value, for example, setting it to **1**.                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                              |
      |                                   | Each workload pod consists of the same containers. You can configure multiple pods for a workload to ensure high reliability. For such a workload, if one pod is faulty, the workload can still run properly. If only one pod is used, a node or pod exception may cause service exceptions. |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Time Zone Synchronization         | If this parameter is enabled, the container and the node use the same time zone.                                                                                                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                                                              |
      |                                   | .. important::                                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                              |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                                                   |
      |                                   |    After time zone synchronization is enabled, disks of the hostPath type will be automatically added and listed in the **Data Storage** > **Local Volume** area. Do not modify or delete the disks.                                                                                         |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Description                       | Description of the workload.                                                                                                                                                                                                                                                                 |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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

         You can add data storage volumes only when creating a StatefulSet.

   g. **Security Context**: Container permissions can be configured to protect CCE and other containers from being affected.

      Enter the user ID to set container permissions and prevent systems and other containers from being affected.

   h. **Log Policies**: Log collection policies and log directory can be configured to collect container logs for unified management and analysis. For details, see :ref:`Container Logs <cce_01_0018>`.

#. Click **Next: Set Application Access** and set **Headless Service** and workload access type.

   :ref:`Table 3 <cce_01_0048__table2293204814496>` describes the parameters in the **Headless Service** area.

   .. _cce_01_0048__table2293204814496:

   .. table:: **Table 3** Parameter description

      +----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter      | Description                                                                                                                                                                                              |
      +================+==========================================================================================================================================================================================================+
      | Service Name   | Name of the Service corresponding to the workload for mutual access between pods. This Service is used for internal discovery of pods, and does not require an independent IP address or load balancing. |
      +----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Port Name      | Name of the container port. You are advised to enter a name that indicates the function of the port.                                                                                                     |
      +----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Container Port | Listening port inside the container.                                                                                                                                                                     |
      +----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   Click **Add Service** and set the workload access type.

   If your workload will be reachable to other workloads or public networks, add a Service to define the workload access type.

   The workload access type determines the network attributes of the workload. Workloads with different access types can provide different network capabilities. For details, see :ref:`Overview <cce_01_0010>`.

#. Click **Next: Configure Advanced Settings**.

   -  **Upgrade Policy**: Only **Rolling upgrade** is supported.

      During a rolling upgrade, old pods are gradually replaced with new ones, and service traffic is evenly distributed to both pods to ensure service continuity.

   -  **Pod Management Policy**: There are two types of policies: ordered and parallel.

      **Ordered**: The StatefulSet will deploy, delete, or scale pods in order and one by one (the StatefulSet waits until each pod is ready before continuing). This is the default policy.

      **Parallel**: The StatefulSet will create pods in parallel to match the desired scale without waiting, and will delete all pods at once.

   -  **Graceful Deletion**: A time window can be set for workload deletion and reserved for executing commands in the pre-stop phase in the lifecycle. If workload processes are not terminated after the time window elapses, the workload will be forcibly deleted.

      -  **Graceful Time Window (s)**: Set a time window (0-9999s) for pre-stop commands to finish execution before a workload is deleted. The default value is 30s.
      -  **Scale Order**: Choose **Prioritize new pods** or **Prioritize old pods** based on service requirements. **Prioritize new pods** indicates that new pods will be first deleted when a scale-in is triggered.

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

#. Click **Create** and then **Back to StatefulSet List**. If the workload is in the **Running** state, it has been successfully created. If the workload status is not updated, click |image1| in the upper right corner or press **F5** to refresh the page.

   .. note::

      -  When a node is unavailable, pods become **Unready**. In this case, you need to manually delete the pods of the StatefulSet so that the pods can be migrated to a normal node.
      -  If the workload list contains more than 500 records, the Kubernetes pagination mechanism will be used. Specifically, you can only go to the first page or the next page, but cannot go to the previous page. In addition, if resources are divided into discrete pages, the total number of resources displayed is the maximum number of resources that can be queried at a time, not the actual total number of resources.

.. _cce_01_0048__section113441881214:

Using kubectl
-------------

The following procedure uses an etcd workload as an example to describe how to create a workload using kubectl.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

#. Create and edit the **etcd-statefulset.yaml** file.

   **etcd-statefulset.yaml** is an example file name, and you can change it as required.

   **vi etcd-statefulset.yaml**

   The following provides an example of the file contents. For more information on StatefulSet, see the `Kubernetes documentation <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/>`__.

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: etcd
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: etcd
        serviceName: etcd-svc
        template:
          metadata:
            labels:
              app: etcd
          spec:
            containers:
            - env:
              - name: PAAS_APP_NAME
                value: tesyhhj
              - name: PAAS_NAMESPACE
                value: default
              - name: PAAS_PROJECT_ID
                value: 9632fae707ce4416a0ab1e3e121fe555
              image: etcd # If you use an image in My Images, obtain the image path from SWR.
              imagePullPolicy: IfNotPresent
              name: container-0
        updateStrategy:
          type: RollingUpdate

   **vi etcd-headless.yaml**

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        labels:
          app: etcd
        name: etcd-svc
      spec:
        clusterIP: None
        ports:
        - name: etcd-svc
          port: 3120
          protocol: TCP
          targetPort: 3120
        selector:
          app: etcd
        sessionAffinity: None
        type: ClusterIP

#. Create a workload and the corresponding headless service.

   **kubectl create -f etcd-statefulset.yaml**

   If the following information is displayed, the StatefulSet has been successfully created.

   .. code-block::

      statefulset.apps/etcd created

   **kubectl create -f etcd-headless.yaml**

   If the following information is displayed, the headless service has been successfully created.

   .. code-block::

      service/etcd-svc created

#. If the workload will be accessed through a ClusterIP or NodePort Service, set the corresponding workload access type. For details, see :ref:`Networking <cce_01_0020>`.

.. |image1| image:: /_static/images/en-us_image_0300973777.png
