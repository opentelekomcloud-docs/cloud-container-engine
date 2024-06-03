:original_name: cce_10_0144.html

.. _cce_10_0144:

Deploying an Application Through the Helm v3 Client
===================================================

Prerequisites
-------------

-  The Kubernetes cluster created on CCE has been connected to kubectl. For details, see :ref:`Using kubectl <cce_10_0107__section37321625113110>`.
-  To pull a public image when deploying Helm, ensure an EIP has been bound to the node.

.. _cce_10_0144__en-us_topic_0226102212_en-us_topic_0179003017_section3719193213815:

Installing Helm v3
------------------

This section uses Helm v3.3.0 as an example.

For other versions, visit https://github.com/helm/helm/releases.

#. Download the Helm client from the VM connected to the cluster.

   .. code-block::

      wget https://get.helm.sh/helm-v3.3.0-linux-amd64.tar.gz

#. Decompress the Helm package.

   .. code-block::

      tar -xzvf helm-v3.3.0-linux-amd64.tar.gz

#. Copy Helm to the system path, for example, **/usr/local/bin/helm**.

   .. code-block::

      mv linux-amd64/helm /usr/local/bin/helm

#. Query the Helm version.

   .. code-block::

      helm version
      version.BuildInfo{Version:"v3.3.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}

Installing the Helm Chart
-------------------------

You can use Helm to install a chart. Before using Helm, you may need to understand the following concepts to better use Helm:

-  Chart: contains resource definitions and a large number of configuration files of Kubernetes applications.
-  Repository: stores shared charts. You can download charts from the repository to a local path for installation or install them online.
-  Release: running result of after a chart is installed in a Kubernetes cluster using Helm. A chart can be installed multiple times in a cluster. A new release will be created for each installation. A MySQL chart is used as an example. To run two databases in a cluster, install the chart twice. Each database has its own release and release name.

For more details, see `Using Helm <https://helm.sh/docs/intro/using_helm/>`__.

#. .. _cce_10_0144__li125132594918:

   Search for a chart from the `Artifact Hub <https://artifacthub.io/packages/search?kind=0>`__ repository recommended by Helm and configure the Helm repository.

   .. code-block::

      helm repo add {repo_name} {repo_addr}

   The following uses the `WordPress chart <https://artifacthub.io/packages/helm/bitnami/wordpress>`__ as an example:

   .. code-block::

      helm repo add bitnami https://charts.bitnami.com/bitnami

#. Run the **helm install** command to install the chart.

   .. code-block::

      helm install {release_name} {chart_name} --set key1=val1

   For example, to install WordPress, the WordPress chart added in :ref:`1 <cce_10_0144__li125132594918>` is **bitnami/wordpress**, the release name is **my-wordpress**, and mandatory parameters have been configured.

   .. code-block::

      helm install my-wordpress bitnami/wordpress \
           --set mariadb.primary.persistence.enabled=true \
           --set mariadb.primary.persistence.storageClass=csi-disk \
           --set mariadb.primary.persistence.size=10Gi \
           --set persistence.enabled=false

   Run the **helm show values** *{chart_name}* command to view the configurable options of the chart. For example, to view the configurable items of WordPress, run the following command:

   .. code-block::

      helm show values bitnami/wordpress

#. View the installed chart release.

   .. code-block::

      helm list

Common Issues
-------------

-  The following error message is displayed after the **helm version** command is run:

   .. code-block::

      Client:
      &version.Version{SemVer:"v3.3.0",
      GitCommit:"012cb0ac1a1b2f888144ef5a67b8dab6c2d45be6", GitTreeState:"clean"}
      E0718 11:46:10.132102    7023 portforward.go:332] an error occurred
      forwarding 41458 -> 44134: error forwarding port 44134 to pod
      d566b78f997eea6c4b1c0322b34ce8052c6c2001e8edff243647748464cd7919, uid : unable
      to do port forwarding: socat not found.
      Error: cannot connect to Tiller

   The preceding information is displayed because the socat is not installed. Run the following command to install the socat:

   .. code-block::

      yum install socat -y

-  When you run the **yum install socat -y** command on a node running EulerOS 2.9, the following error message is displayed:

   .. code-block::

      No match for argument: socat
      Error: Unable to find a match: socat

   The node image does not contain socat. In this case, manually download the RPM chart and run the following command to install it (replace the RPM chart name with the actual one):

   .. code-block::

      rpm -i socat-1.7.3.2-8.oe1.x86_64.rpm

-  When the socat has been installed and the following error message is displayed after the **helm version** command is run:

   .. code-block::

      $ helm version
      Client: &version.Version{SemVer:"v3.3.0", GitCommit:"021cb0ac1a1b2f888144ef5a67b8dab6c2d45be6", GitTreeState:"clean"}
      Error: cannot connect to Tiller

   The Helm chart reads the configuration certificate in **.Kube/config** to communicate with Kubernetes. The preceding error indicates that the kubectl configuration is incorrect. In this case, reconnect the cluster to kubectl. For details, see :ref:`Using kubectl <cce_10_0107__section37321625113110>`.

-  Storage fails to be created after you have connected to cloud storage services.

   This issue may be caused by the **annotation** field in the created PVC. Change the chart name and install the chart again.

-  If kubectl is not properly configured, the following error message is displayed after the **helm install** command is run:

   .. code-block::

      # helm install prometheus/ --generate-name
      WARNING: This chart is deprecated
      Error: Kubernetes cluster unreachable: Get "http://localhost:8080/version?timeout=32s": dial tcp [::1]:8080: connect: connection refused

   **Solution**: Configure kubeconfig for the node. For details, see :ref:`Using kubectl <cce_10_0107__section37321625113110>`.
