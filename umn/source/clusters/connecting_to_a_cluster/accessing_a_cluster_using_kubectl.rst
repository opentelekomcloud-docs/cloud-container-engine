:original_name: cce_10_0107.html

.. _cce_10_0107:

Accessing a Cluster Using kubectl
=================================

kubectl is a command-line tool provided by Kubernetes, enabling you to manage cluster resources, view cluster status, deploy applications, and debug issues through the CLI. To access a CCE cluster using kubectl, you can use either of the following methods:

-  Intranet access: The client connects to the cluster's API server using an intranet IP address. This method keeps data traffic within the internal network, enhancing security by avoiding the Internet.
-  Internet access: The cluster's API server exposes a public API, allowing clients to access the Kubernetes cluster over the Internet.

This section uses a CCE standard cluster as an example to describe how to access a CCE cluster using kubectl.

Prerequisites
-------------

-  Before using intranet access, ensure that the client and the cluster to be accessed are in the same VPC.
-  Before using Internet access, ensure that the client can access the Internet and that an EIP has been bound to the target cluster. For details about how to bind an EIP, see :ref:`Procedure <cce_10_0864__section128889371044>`.

   .. note::

      In a cluster with an EIP bound, kube-apiserver will be exposed to the Internet and may be attacked. To resolve this issue, you can configure Advanced Anti-DDoS for the EIP of the node on which kube-apiserver runs or :ref:`configure security group rules <cce_faq_00417>`.

Notes and Constraints
---------------------

When you access a cluster using kubectl, CCE uses **kubeconfig** generated on the cluster for authentication. This file contains user information, based on which CCE determines which Kubernetes resources can be accessed via kubectl. Since the **kubeconfig** file contains user identity details, the permissions associated with that user are inherited when accessing the cluster via kubectl.

For details about user permissions, see :ref:`Cluster Permissions (IAM-based) and Namespace Permissions (Kubernetes RBAC-based) <cce_10_0187__section1464135853519>`.

.. _cce_10_0107__section37321625113110:

Procedure
---------

Before using kubectl to access a cluster, install kubectl on the client. Then, download the kubectl configuration file from the cluster and copy it to the client. Once configured, the client can access the target cluster. The process is as follows:

#. **Download kubectl.**

   Prepare a computer that can access the public network and install kubectl in CLI mode. You can run the **kubectl version** command to check whether kubectl has been installed. If kubectl has been installed, skip this step.

   This section uses the Linux environment as an example to describe how to install and configure kubectl. For details, see `Installing kubectl <https://kubernetes.io/docs/tasks/tools/#kubectl>`__.

   a. Log in to your client and download kubectl.

      .. code-block::

         cd /home
         curl -LO https://dl.k8s.io/release/{v1.25.0}/bin/linux/amd64/kubectl

      *{v1.25.0}* specifies the version. Replace it as required.

   b. Install kubectl.

      .. code-block::

         chmod +x kubectl
         mv -f kubectl /usr/local/bin

#. .. _cce_10_0107__li34691156151712:

   **Obtain the kubectl configuration file.**

   a. On the **Overview** page, locate the **Connection Information** area, and click **Configure** next to **kubectl**.
   b. In the window that slides out from the right, locate the **Download the kubeconfig file** area, select **Intranet access** or **Public network access** for **Current data**, and download the configuration file.

   .. note::

      -  The kubectl configuration file **kubeconfig** is used for cluster authentication. If the file is leaked, your clusters may be attacked.
      -  The Kubernetes permissions assigned by the configuration file downloaded by IAM users are the same as those assigned to the IAM users on the CCE console.
      -  If the KUBECONFIG environment variable is configured in the Linux OS, kubectl preferentially loads the KUBECONFIG environment variable instead of **$home/.kube/config**.

#. .. _cce_10_0107__li25451059122317:

   Configure kubectl.

   Configure kubectl (A Linux OS is used).

   a. Log in to your client and copy the configuration file (for example, **kubeconfig.yaml**) downloaded in :ref:`2 <cce_10_0107__li34691156151712>` to the **/home** directory on your client.

   b. Configure the kubectl authentication file.

      .. code-block::

         cd /home
         mkdir -p $HOME/.kube
         mv -f kubeconfig.yaml $HOME/.kube/config

   c. Switch the kubectl access mode based on service scenarios.

      -  Run this command to enable intra-VPC access:

         .. code-block::

            kubectl config use-context internal

      -  Run this command to enable public access (EIP required):

         .. code-block::

            kubectl config use-context external

      -  Run this command to enable public access and two-way authentication (EIP required):

         .. code-block::

            kubectl config use-context externalTLSVerify

         For details about the cluster two-way authentication, see :ref:`Two-Way Authentication for Domain Names <cce_10_0107__section1559919152711>`.

#. Run the following command on the client to check whether the client can access the cluster using kubectl:

   .. code-block::

      kubectl cluster-info    # Check the cluster information.

   If the following information is displayed, the client can access the cluster using kubectl:

   .. code-block::

      Kubernetes control plane is running at https://xx.xx.xx.xx:5443
      CoreDNS is running at https://xx.xx.xx.xx:5443/api/v1/namespaces/kube-system/services/coredns:dns/proxy
      To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

.. _cce_10_0107__section1559919152711:

Two-Way Authentication for Domain Names
---------------------------------------

Two-way domain name authentication is a mutual authentication mechanism that verifies the identities of both the client and server. This mode enhances security between clusters and clients, preventing unauthorized access.

-  After an EIP is bound to an API Server, two-way domain name authentication is disabled by default if kubectl is used to access the cluster. You can run **kubectl config use-context externalTLSVerify** to enable the two-way domain name authentication.

-  When an EIP is bound to or unbound from a cluster, or a custom domain name is configured or updated, the cluster server certificate will be added the latest cluster access address (including the EIP bound to the cluster and all custom domain names configured for the cluster).

-  Asynchronous cluster synchronization takes about 5 to 10 minutes. You can view the synchronization result in **Synchronize Certificate** in **Operation Records**.

-  For a cluster that has been bound to an EIP, if the authentication fails (x509: certificate is valid) when two-way authentication is used, bind the EIP again and download **kubeconfig.yaml** again.

-  If the two-way domain name authentication is not supported, **kubeconfig.yaml** contains the **"insecure-skip-tls-verify": true** field, as shown in :ref:`Figure 1 <cce_10_0107__fig1941342411>`. To use two-way authentication, download the **kubeconfig.yaml** file again and enable two-way authentication for the domain names.

   .. _cce_10_0107__fig1941342411:

   .. figure:: /_static/images/en-us_image_0000002218820570.png
      :alt: **Figure 1** Two-way authentication disabled for domain names

      **Figure 1** Two-way authentication disabled for domain names

Common Issues
-------------

-  **Error from server Forbidden**

   When you use kubectl to create or query Kubernetes resources, the following output is returned:

   .. code-block::

      # kubectl get deploy Error from server (Forbidden): deployments.apps is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "deployments" in API group "apps" in the namespace "default"

   The cause is that the user does not have the permissions to operate the Kubernetes resources. For details about how to assign permissions, see :ref:`Namespace Permissions (Kubernetes RBAC-based) <cce_10_0189>`.

-  **The connection to the server localhost:8080 was refused**

   When you use kubectl to create or query Kubernetes resources, the following output is returned:

   .. code-block::

      The connection to the server localhost:8080 was refused - did you specify the right host or port?

   The cause is that cluster authentication is not configured for the kubectl client. For details, see :ref:`3 <cce_10_0107__li25451059122317>`.
