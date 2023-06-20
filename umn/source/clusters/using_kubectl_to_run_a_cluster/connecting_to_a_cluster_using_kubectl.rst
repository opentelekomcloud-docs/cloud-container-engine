:original_name: cce_10_0107.html

.. _cce_10_0107:

Connecting to a Cluster Using kubectl
=====================================

Scenario
--------

This section uses a CCE cluster as an example to describe how to connect to a CCE cluster using kubectl.

Permission Description
----------------------

When you access a cluster using kubectl, CCE uses the **kubeconfig.json** file generated on the cluster for authentication. This file contains user information, based on which CCE determines which Kubernetes resources can be accessed by kubectl. The permissions recorded in a **kubeconfig.json** file vary from user to user.

For details about user permissions, see :ref:`Cluster Permissions (IAM-based) and Namespace Permissions (Kubernetes RBAC-based) <cce_10_0187__section1464135853519>`.

Using kubectl
-------------

To connect to a Kubernetes cluster from a PC, you can use kubectl, a Kubernetes command line tool. You can log in to the CCE console, click the name of the cluster to be connected, and view the access address and kubectl connection procedure on the cluster details page.

CCE allows you to access a cluster through a **VPC network** or a **public network**.

-  **Intra-VPC access**: The client that accesses the cluster must be in the same VPC as the cluster.
-  **Public access**:The client that accesses the cluster must be able to access public networks and the cluster has been bound with a public network IP.

   .. important::

      To bind a public IP (EIP) to the cluster, go to the cluster details page and click **Bind** next to **EIP** in the **Connection Information** pane. In a cluster with an EIP bound, kube-apiserver will be exposed to public networks and may be attacked. You are advised to configure Advanced Anti-DDoS (AAD) for the EIP of the node where kube-apiserver resides.

Download kubectl and the configuration file. Copy the file to your client, and configure kubectl. After the configuration is complete, you can access your Kubernetes clusters. Procedure:

#. Download kubectl.

   Prepare a computer that can access the public network and install kubectl in CLI mode. You can run the **kubectl version** command to check whether kubectl has been installed. If kubectl has been installed, skip this step.

   This section uses the Linux environment as an example to describe how to install and configure kubectl. For details, see `Installing kubectl <https://kubernetes.io/docs/tasks/tools/#kubectl>`__.

   a. Log in to your client and download kubectl.

      .. code-block::

         cd /home
         curl -LO https://dl.k8s.io/release/{v1.25.0}/bin/linux/amd64/kubectl

      **{v1.25.0}** specifies the version number. Replace it as required.

   b. Install kubectl.

      .. code-block::

         chmod +x kubectl
         mv -f kubectl /usr/local/bin

#. .. _cce_10_0107__li34691156151712:

   Obtain the kubectl configuration file (kubeconfig).

   On the **Connection Information** pane on the cluster details page, click **Learn more** next to **kubectl**. On the window displayed, download the configuration file.

   .. note::

      -  The kubectl configuration file **kubeconfig.json** is used for cluster authentication. If the file is leaked, your clusters may be attacked.
      -  By default, two-way authentication is disabled for domain names in the current cluster. You can run the **kubectl config use-context externalTLSVerify** command to enable two-way authentication. For details, see :ref:`Two-Way Authentication for Domain Names <cce_10_0107__section1559919152711>`. For a cluster that has been bound to an EIP, if the authentication fails (x509: certificate is valid) when two-way authentication is used, you need to bind the EIP again and download **kubeconfig.json** again.
      -  The Kubernetes permissions assigned by the configuration file downloaded by IAM users are the same as those assigned to the IAM users on the CCE console.
      -  If the KUBECONFIG environment variable is configured in the Linux OS, kubectl preferentially loads the KUBECONFIG environment variable instead of **$home/.kube/config**.

#. .. _cce_10_0107__li25451059122317:

   Configure kubectl.

   Configure kubectl (A Linux OS is used).

   a. Log in to your client and copy the kubeconfig.json configuration file downloaded in :ref:`2 <cce_10_0107__li34691156151712>` to the **/home** directory on your client.

   b. Configure the kubectl authentication file.

      .. code-block::

         cd /home
         mkdir -p $HOME/.kube
         mv -f kubeconfig.json $HOME/.kube/config

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

.. _cce_10_0107__section1559919152711:

Two-Way Authentication for Domain Names
---------------------------------------

Currently, CCE supports two-way authentication for domain names.

-  Two-way authentication is disabled for domain names by default. You can run the **kubectl config use-context externalTLSVerify** command to switch to the externalTLSVerify context to enable it.

-  When an EIP is bound to or unbound from a cluster, or a custom domain name is configured or updated, the cluster server certificate will be added the latest cluster access address (including the EIP bound to the cluster and all custom domain names configured for the cluster).

-  Asynchronous cluster synchronization takes about 5 to 10 minutes. You can view the synchronization result in **Synchronize Certificate** in **Operation Records**.

-  For a cluster that has been bound to an EIP, if the authentication fails (x509: certificate is valid) when two-way authentication is used, you need to bind the EIP again and download **kubeconfig.json** again.

-  If the domain name two-way authentication is not supported, **kubeconfig.json** contains the **"insecure-skip-tls-verify": true** field, as shown in :ref:`Figure 1 <cce_10_0107__fig1941342411>`. To use two-way authentication, you can download the **kubeconfig.json** file again and enable two-way authentication for the domain names.

   .. _cce_10_0107__fig1941342411:

   .. figure:: /_static/images/en-us_image_0000001568822965.png
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
