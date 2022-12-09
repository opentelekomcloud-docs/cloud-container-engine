:original_name: cce_01_0107.html

.. _cce_01_0107:

Connecting to a Cluster Using kubectl
=====================================

Scenario
--------

This section uses a CCE cluster as an example to describe how to connect to a CCE cluster using kubectl.

Permission Description
----------------------

When you access a cluster using kubectl, CCE uses the **kubeconfig.json** file generated on the cluster for authentication. This file contains user information, based on which CCE determines which Kubernetes resources can be accessed by kubectl. The permissions recorded in a **kubeconfig.json** file vary from user to user.

For details about user permissions, see :ref:`Cluster Permissions (IAM-based) and Namespace Permissions (Kubernetes RBAC-based) <cce_01_0187__section1464135853519>`.

Using kubectl
-------------

**Background**

To connect a client to a Kubernetes cluster, you can use kubectl. For details, see `Install Tools <https://kubernetes.io/docs/tasks/tools/>`__.

**Prerequisites**

CCE allows you to access a cluster through a **VPC network** or a **public network**.

-  VPC internal access: Clusters in the same VPC can access each other.
-  Public network access: You need to prepare an ECS that can connect to a public network.

.. important::

   If public network access is used, the kube-apiserver of the cluster will be exposed to the public network and may be attacked. You are advised to configure Advanced Anti-DDoS for the EIP of the node where the kube-apiserver is located.

**Downloading kubectl**

You need to download kubectl and configuration file, copy the file to your client, and configure kubectl. After the configuration is complete, you can use kubectl to access your Kubernetes clusters.

On the `Kubernetes release <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/README.md>`__ page, click the corresponding link based on the cluster version, click **Client Binaries**, and download the corresponding platform software package.


.. figure:: /_static/images/en-us_image_0000001283755568.png
   :alt: **Figure 1** Downloading kubectl

   **Figure 1** Downloading kubectl

**Installing and configuring kubectl**

#. Log in to the CCE console, click **Resource Management** > **Clusters**, and choose **Command Line Tool** > **Kubectl** under the cluster to be connected.
#. On the **Kubectl** tab page of the cluster details page, connect to the cluster as prompted.

   .. note::

      -  You can download the kubectl configuration file (**kubeconfig.json**) on the **kubectl** tab page. This file is used for user cluster authentication. If the file is leaked, your clusters may be attacked.
      -  If two-way authentication is enabled for the current cluster and an EIP has been bound to the cluster, when the authentication fails (x509: certificate is valid), you need to bind the EIP and download the **kubeconfig.json** file again.
      -  By default, two-way authentication is disabled for domain names in the current cluster. You can run the **kubectl config use-context externalTLSVerify** command to enable two-way authentication. For details, see :ref:`Two-Way Authentication for Domain Names <cce_01_0107__section1559919152711>`. For a cluster that has been bound to an EIP, if the authentication fails (x509: certificate is valid) when two-way authentication is used, you need to bind the EIP again and download **kubeconfig.json** again.
      -  The Kubernetes permissions assigned by the configuration file downloaded by IAM users are the same as those assigned to the IAM users on the CCE console.
      -  If the KUBECONFIG environment variable is configured in the Linux OS, kubectl preferentially loads the KUBECONFIG environment variable instead of **$home/.kube/config**.

.. _cce_01_0107__section1559919152711:

Two-Way Authentication for Domain Names
---------------------------------------

Currently, CCE supports two-way authentication for domain names.

-  Two-way authentication is disabled for domain names by default. You can run the **kubectl config use-context externalTLSVerify** command to switch to the externalTLSVerify context to enable it.

-  When an EIP is bound to or unbound from a cluster, or a custom domain name is configured or updated, the cluster server certificate will be added the latest cluster access address (including the EIP bound to the cluster and all custom domain names configured for the cluster).

-  Asynchronous cluster synchronization takes about 5 to 10 minutes.

-  For a cluster that has been bound to an EIP, if the authentication fails (x509: certificate is valid) when two-way authentication is used, you need to bind the EIP again and download **kubeconfig.json** again.

-  If the domain name two-way authentication is not supported, **kubeconfig.json** contains the **"insecure-skip-tls-verify": true** field, as shown in :ref:`Figure 2 <cce_01_0107__fig1941342411>`. To use two-way authentication, you can download the **kubeconfig.json** file again and enable two-way authentication for the domain names.

   .. _cce_01_0107__fig1941342411:

   .. figure:: /_static/images/en-us_image_0000001243407853.png
      :alt: **Figure 2** Two-way authentication disabled for domain names

      **Figure 2** Two-way authentication disabled for domain names

Common Issue (Error from server Forbidden)
------------------------------------------

When you use kubectl to create or query Kubernetes resources, the following output is returned:

# kubectl get deploy Error from server (Forbidden): deployments.apps is forbidden: User "0c97ac3cb280f4d91fa7c0096739e1f8" cannot list resource "deployments" in API group "apps" in the namespace "default"

The cause is that the user does not have the permissions to operate the Kubernetes resources. For details about how to assign permissions, see :ref:`Namespace Permissions (Kubernetes RBAC-based) <cce_01_0189>`.
