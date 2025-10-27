:original_name: cce_10_0009.html

.. _cce_10_0009:

Using Third-Party Images
========================

Third-party images are container images provided by organizations or individuals other than the official image repository and SWR image repository. These images typically contain custom applications, tools, or specific versions of OSs to meet particular service requirements. In a CCE standard or Turbo cluster, you can create workloads using third-party images pulled through secret authentication. For more information, see `Images \| Kubernetes <https://kubernetes.io/docs/concepts/containers/images/?spm=a2c4g.11186623.2.1.XVyfik#using-a-private-registry>`__.

Prerequisites
-------------

If third-party images are used, the CCE standard or Turbo cluster must be able to access the network environment where the third-party image repository is deployed. The network access types include:

-  **Intranet**: If the third-party image repository supports intranet access, consider the following scenarios:

   -  If the third-party image repository is in the same VPC as the cluster, pods can directly pull images over the intranet without additional configurations.
   -  If the third-party image repository and the cluster are in different VPCs in the same region, you can use a VPC peering connection to enable network connectivity between the two VPCs. Pods can then directly pull images over the intranet.

-  **Internet**: If the third-party image repository is accessible over the Internet, pods can pull images directly. In this case, ensure the **pods can access the Internet** using one of the following methods:

   .. note::

      Ensure sufficient bandwidth when pulling images over the Internet. Insufficient bandwidth may cause slow or failed image pulls.

   -  In a standard or Turbo cluster, configure an SNAT rule to enable all pods in the cluster to access the Internet. After the configuration, pods can pull images directly over the Internet. For details, see :ref:`Accessing the Internet from a Container <cce_10_0400>`.
   -  In a standard cluster, bind an EIP to the node running the workload. This allows all workloads on that node to access the Internet.

-  **Direct Connect or VPN**: To use images from an on-premises image repository, use Direct Connect or VPN to connect the on-premises network to the cluster's VPC. Once the network environment is connected to the VPC, no additional configuration is needed.

Using the Console
-----------------

To create a secret on the console and pull a third-party image to create a workload, follow these steps.

#. .. _cce_10_0009__li6594823114717:

   Create a cluster secret to authenticate and pull images from a third-party repository if authentication is required.

   a. Log in to the CCE console.
   b. Click the name of the target cluster to access the cluster.
   c. In the navigation pane, choose **ConfigMaps and Secrets**. On the **Secrets** tab, click **Create Secret**.
   d. In the **Create Secret** dialog box, configure parameters based on :ref:`Table 1 <cce_10_0009__en-us_topic_0000001708838110_table167902189273>`. For more details, see :ref:`Creating a Secret <cce_10_0153>`.

   .. _cce_10_0009__en-us_topic_0000001708838110_table167902189273:

   .. table:: **Table 1** Parameters for creating a secret

      +-----------------------+-----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter             | Example Value                                 | Description                                                                                                                                                 |
      +=======================+===============================================+=============================================================================================================================================================+
      | Name                  | test                                          | Name for the secret.                                                                                                                                        |
      |                       |                                               |                                                                                                                                                             |
      |                       |                                               | Enter up to 253 characters, starting and ending with a lowercase letter or digit. Only lowercase letters, digits, hyphens (-), and periods (.) are allowed. |
      +-----------------------+-----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Secret Type           | kubernetes.io/dockerconfigjson                | Type of the secret.                                                                                                                                         |
      |                       |                                               |                                                                                                                                                             |
      |                       |                                               | The value is fixed at **kubernetes.io/dockerconfigjson**, indicating that the secret is used for authentication when third-party images are pulled.         |
      +-----------------------+-----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Data                  | Image Repository Address: **www.example.com** | -  **Image Repository Address**: Enter the address of the third-party image repository.                                                                     |
      |                       |                                               | -  **Username**: Enter the username for accessing the third-party image repository.                                                                         |
      |                       | Username: **ssl**                             | -  **Password**: Enter the password for accessing the third-party image repository.                                                                         |
      |                       |                                               |                                                                                                                                                             |
      |                       | Password: *xxx*                               |                                                                                                                                                             |
      +-----------------------+-----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. In the navigation pane, choose **Workloads**. In the upper right corner of the displayed page, click **Create Workload**. On the **Create Workload** page, set **Image Name** (**Container Settings** > **Container Information** > **Basic Info**) to the third-party image path. Select the secret created in :ref:`1 <cce_10_0009__li6594823114717>` for **Image Access Credential**.

   .. caution::

      If you do not specify a domain name when entering a third-party image address, for example, **nginx:latest**, the image will be pulled from docker.io by default. To specify the default image pull address, configure the **Modify Image Repository Configuration** parameter in the :ref:`container engine configuration <cce_10_0652__section942261815116>` of the node pool.

#. Configure other parameters and click **Create Workload**. If the workload status is **Running**, the third-party image has been pulled.

Using kubectl
-------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.
#. If the third-party image repository has an account and password, you need to create a secret in the cluster as the identity credential for pulling images.

   a. Create a secret. The secret type is **kubernetes.io/dockerconfigjson** by default, which indicates that the secret is used for authentication when third-party images are pulled.

      .. code-block::

         kubectl create secret docker-registry test  -n default --docker-server=www.example.com --docker-username=ssl --docker-password=xxx --docker-email=example@123.com

      In the command, *test* indicates the secret name, *default* indicates the namespace where the secret is located, and other parameters are described in the following table.

      .. table:: **Table 2** Secret parameters

         +-----------------+-----------------+--------------------------------------------------------------------------------+
         | Parameter       | Example Value   | Description                                                                    |
         +=================+=================+================================================================================+
         | docker-server   | www.example.com | Enter the address of the third image repository.                               |
         +-----------------+-----------------+--------------------------------------------------------------------------------+
         | docker-username | ssl             | Enter the username for accessing the third-party image repository.             |
         +-----------------+-----------------+--------------------------------------------------------------------------------+
         | docker-password | xxx             | Enter the password for accessing the third-party image repository.             |
         +-----------------+-----------------+--------------------------------------------------------------------------------+
         | docker-email    | example@123.com | Email address of the third-party image repository. This parameter is optional. |
         +-----------------+-----------------+--------------------------------------------------------------------------------+

      Information similar to the following is displayed:

      .. code-block::

         secret/test created

   b. Check whether the secret has been created.

      .. code-block::

         kubectl get secret

      If the following information is displayed, the secret has been created:

      .. code-block::

         NAME             TYPE                             DATA   AGE
         default-secret   kubernetes.io/dockerconfigjson   1      41h
         paas.elb         cfe/secure-opaque                1      41h
         test             kubernetes.io/dockerconfigjson   1      16s

#. Create a workload using a third-party image.

   a. Create a YAML file for creating a workload. In this example, the file name is **deployment.yaml**. You can change it as needed.

      .. code-block::

         vim deployment.yaml

      The file content is as follows:

      .. code-block::

         apiVersion: apps/v1
         kind: Deployment
         metadata:
           name: foo
           namespace: default
         spec:
           replicas: 1
           selector:
             matchLabels:
               app: foo
           strategy:
             type: RollingUpdate
           template:
             metadata:
               labels:
                 app: foo
             spec:
               containers:
               - image: www.example.com/janedoe/awesomeapp:v1    # Third-party image path
                 imagePullPolicy: Always
                 name: foo
               imagePullSecrets:
               - name: test    # Use the created secret for identity authentication when images are pulled.

      .. caution::

         If you do not specify a domain name when entering a third-party image address, for example, **nginx:latest**, the image will be pulled from docker.io by default. To specify the default image pull address, configure the **Modify Image Repository Configuration** parameter in the :ref:`container engine configuration <cce_10_0652__section942261815116>` of the node pool.

   b. Create the workload.

      .. code-block::

         kubectl create -f deployment.yaml

      If information similar to the following is displayed, the workload is being created:

      .. code-block::

         deployment.apps/foo created

   c. Check the workload status.

      .. code-block::

         kubectl get deployment

      If all the pods of the workload are available, the workload has been created.

      .. code-block::

         NAME     READY   UP-TO-DATE   AVAILABLE   AGE
         foo      1/1     1            1           4m59s
