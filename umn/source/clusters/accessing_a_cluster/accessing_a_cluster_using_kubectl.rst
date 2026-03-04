:original_name: cce_10_0107.html

.. _cce_10_0107:

Accessing a Cluster Using kubectl
=================================

kubectl is a command-line tool provided by Kubernetes, enabling you to manage cluster resources, view cluster status, deploy applications, and debug issues through the CLI. To access a CCE cluster using kubectl, you can use either of the following methods:

-  Private access: Clients access the cluster's API server via an intranet IP address, keeping data traffic internal and enhancing security.
-  Public access: The cluster's API server exposes a public API, allowing clients to access the Kubernetes cluster over the Internet. When using the Internet for access, you can choose whether to enable **two-way domain name trust**.

   -  If **two-way domain name trust** is disabled, kubectl and the API server use one-way certificate authentication, which is less secure. In this mode, only kubectl verifies the API server's certificate, while the API server does not verify kubectl's certificate.
   -  If **two-way domain name trust** is enabled, kubectl and the API server use mutual certificate authentication, which is more secure. In this mode, kubectl verifies the API server's certificate, and the API server verifies kubectl's certificate (specified in the **client-certificate-data** field in the kubeconfig file). For more details, see :ref:`Two-Way Domain Name Trust <cce_10_0107__section1559919152711>`.

This section uses a CCE standard cluster as an example to describe how to access a CCE cluster using kubectl.

How It Works
------------

kubectl retrieves cluster information from a kubeconfig file and communicates with the Kubernetes API server. The `kubeconfig <https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/>`__ file is the identity credential for kubectl to access the Kubernetes cluster. It contains the API server address, user authentication credentials, and other configuration details. With these details, kubectl can interact with the Kubernetes cluster to perform management tasks.


.. figure:: /_static/images/en-us_image_0000002516079611.png
   :alt: **Figure 1** Using kubectl to access a cluster

   **Figure 1** Using kubectl to access a cluster

Prerequisites
-------------

-  A client that can access the Internet is available.
-  If private access is used, the client and the cluster to be accessed must be in the same VPC.
-  If public access is used, the cluster to be accessed has an EIP bound. For details about how to bind an EIP, see :ref:`Procedure <cce_10_0864__section128889371044>`.

   .. note::

      In a cluster with an EIP bound, kube-apiserver will be exposed to the Internet and may be attacked. To resolve this issue, you can configure Advanced Anti-DDoS for the EIP of the node on which kube-apiserver runs or :ref:`configure security group rules <cce_faq_00417>`.

Constraints
-----------

A kubeconfig file contains user authentication credentials. When you use this file to access a cluster, kubectl accesses the cluster based on the credentials and permissions specified in the file.

For details about user permissions, see :ref:`Cluster Permissions (IAM-based) and Namespace Permissions (Kubernetes RBAC-based) <cce_10_0187__section1464135853519>`.

.. _cce_10_0107__section37321625113110:

Step 1: Download kubectl
------------------------

Before using kubectl to access a cluster, install kubectl on the client. Run the **kubectl version** command to check whether kubectl is installed. If it is installed, skip this step. This section uses Linux as an example to describe how to install and configure kubectl. For details, see `Installing kubectl <https://kubernetes.io/docs/tasks/tools/#kubectl>`__.

#. Log in to your client and download kubectl. *v1.25.0* specifies the version. Replace it as needed.

   .. code-block::

      cd /home
      curl -LO https://dl.k8s.io/release/v1.25.0/bin/linux/amd64/kubectl

#. Run the following command to install kubectl:

   .. code-block::

      chmod +x kubectl
      mv -f kubectl /usr/local/bin

#. Run the following command to check whether kubectl has been installed:

   .. code-block::

      kubectl version

   If information similar to the following is displayed, kubectl has been installed:

   .. code-block::

      Client Version: xxx
      Kustomize Version: xxx
      Server Version: xxx

.. _cce_10_0107__section1388915135311:

Step 2: Obtain the kubectl Configuration File (kubeconfig)
----------------------------------------------------------

Obtain kubeconfig (the kubectl configuration file) from the cluster for access.

#. .. _cce_10_0107__li752814547369:

   On the **Overview** page of the cluster console, locate the **Connection Information** area, and click **Configure** next to **kubectl**.

#. .. _cce_10_0107__li15257117153716:

   In the window that slides out from the right, locate the **Download the kubeconfig file** area, select **Private access** or **Public access** for **Current data**, and copy the configuration file.

   .. note::

      -  kubeconfig is used for cluster authentication. If the file is leaked, your cluster may be attacked.
      -  The Kubernetes permissions assigned by the configuration file downloaded by IAM users are the same as those assigned to the IAM users on the CCE console.
      -  In Linux, if the KUBECONFIG environment variable is set, kubectl will load it instead of **$home/.kube/config**.
      -  An issued kubeconfig certificate remains valid even if the user who requested it is deleted. To ensure cluster security, manually revoke the user's cluster access credentials. For details, see :ref:`Revoking a Cluster Access Credential <cce_10_0744>`.

Step 3: Configure kubectl
-------------------------

The kubeconfig file is stored on the client, and kubectl uses it to access and interact with the cluster.

#. Log in to your client.

#. Create the **kubeconfig.yaml** file. You can change the file name as needed. The file is used to store the configuration file information obtained in :ref:`2 <cce_10_0107__li15257117153716>`.

   .. code-block::

      vim kubeconfig.yaml

   Copy the configuration file information obtained in :ref:`2 <cce_10_0107__li15257117153716>` to **kubeconfig.yaml** and save the file.

#. Save the **kubeconfig.yaml** file to **$HOME/.kube/config**. kubectl will automatically read from it. If you save the **kubeconfig.yaml** file in a different path, set the KUBECONFIG environment variable to point to that path.

   .. code-block::

      cd /home
      mkdir -p $HOME/.kube
      mv -f ~/kubeconfig.yaml $HOME/.kube/config  # Change kubeconfig.yaml to the file name.

#. Switch the kubectl access mode based on service scenarios.

   -  If private access is used through a VPC, run the following command:

      .. code-block::

         kubectl config use-context internal

   -  If public access is enabled and two-way domain name trust is not required, ensure the cluster is bound to an EIP. Then, run the following command:

      .. code-block::

         kubectl config use-context external

   -  If public access is enabled and two-way domain name trust is required, ensure the cluster is bound to an EIP. Then, run the following command:

      .. code-block::

         kubectl config use-context externalTLSVerify

      For more details, see :ref:`Two-Way Domain Name Trust <cce_10_0107__section1559919152711>`.

#. Run the following command on the client to check whether the client can access the cluster using kubectl:

   .. code-block::

      kubectl cluster-info    # Check the cluster information.

   If the following information is displayed, the client can access the cluster using kubectl:

   .. code-block::

      Kubernetes control plane is running at https://xx.xx.xx.xx:5443
      CoreDNS is running at https://xx.xx.xx.xx:5443/api/v1/namespaces/kube-system/services/coredns:dns/proxy
      To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

.. _cce_10_0107__section1559919152711:

Two-Way Domain Name Trust
-------------------------

Two-way domain name trust is a mutual authentication mechanism that verifies the identities of both the client and server. This mode enhances security between clusters and clients, preventing unauthorized access.

-  After an EIP is bound to an API server, two-way domain name trust is disabled by default if kubectl is used to access the cluster. You can run **kubectl config use-context externalTLSVerify** to enable the two-way domain name trust.

-  When an EIP is bound to or unbound from a cluster, or a custom domain name is configured or updated, the cluster access address (including the EIP bound to the cluster and all custom domain names configured for the cluster) will be added to the cluster server certificate.

-  Asynchronous cluster synchronization takes about 5 to 10 minutes. You can view the synchronization result in **Synchronize Certificate** in **Operation Records**.

-  For a cluster that has an EIP bound, if the authentication fails (x509: certificate is valid) when two-way domain name trust is used, bind the EIP again and download **kubeconfig.yaml** again.

-  If the two-way domain name trust is not supported, **kubeconfig.yaml** contains the **"insecure-skip-tls-verify": true** field, as shown in :ref:`Figure 2 <cce_10_0107__fig1941342411>`. To use two-way domain name trust, download the **kubeconfig.yaml** file again and enable two-way domain name trust.

   .. _cce_10_0107__fig1941342411:

   .. figure:: /_static/images/en-us_image_0000002483959672.png
      :alt: **Figure 2** Two-way trust disabled for domain names

      **Figure 2** Two-way trust disabled for domain names

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

   The cause is that cluster authentication is not configured for the kubectl client. For details, see :ref:`Step 2: Obtain the kubectl Configuration File (kubeconfig) <cce_10_0107__section1388915135311>`.
