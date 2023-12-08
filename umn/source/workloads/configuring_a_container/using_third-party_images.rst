:original_name: cce_10_0009.html

.. _cce_10_0009:

Using Third-Party Images
========================

Scenario
--------

CCE allows you to create workloads using images pulled from third-party image repositories.

Generally, a third-party image repository can be accessed only after authentication (using your account and password). CCE uses the secret-based authentication to pull images. Therefore, create a secret for an image repository before pulling images from the repository.

Prerequisites
-------------

The node where the workload is running is accessible from public networks.

Using the Console
-----------------

#. .. _cce_10_0009__li16481144064414:

   Create a secret for accessing a third-party image repository.

   Click the cluster name to access the cluster console. In the navigation pane, choose **ConfigMaps and Secrets**. On the **Secrets** tab, click **Create Secret** in the upper right corner. Set **Secret Type** to **kubernetes.io/dockerconfigjson**. For details, see :ref:`Creating a Secret <cce_10_0153>`.

   Enter the user name and password used to access the third-party image repository.

#. When creating a workload, you can enter a private image path in the format of *domainname/namespace/imagename:tag* for **Image Name** and select the key created in :ref:`1 <cce_10_0009__li16481144064414>` for **Image Access Credential**.

#. Set other parameters and click **Create Workload**.

Using kubectl
-------------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Use kubectl to create a secret of the kubernetes.io/dockerconfigjson.

   .. code-block::

      kubectl create secret docker-registry myregistrykey  -n default --docker-server=DOCKER_REGISTRY_SERVER --docker-username=DOCKER_USER --docker-password=DOCKER_PASSWORD --docker-email=DOCKER_EMAIL

   In the preceding command, *myregistrykey* indicates the key name, *default* indicates the namespace where the key is located, and other parameters are as follows:

   -  **DOCKER_REGISTRY_SERVER**: address of a third-party image repository, for example, **www.3rdregistry.com** or **10.10.10.10:443**
   -  **DOCKER_USER**: account used for logging in to a third-party image repository
   -  **DOCKER\_PASSWORD**: password used for logging in to a third-party image repository
   -  **DOCKER_EMAIL**: email of a third-party image repository

#. Use a third-party image to create a workload.

   A kubernetes.io/dockerconfigjson secret is used for authentication when you obtain a private image. The following is an example of using the myregistrykey for authentication.

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        name: foo
        namespace: default
      spec:
        containers:
          - name: foo
            image: www.3rdregistry.com/janedoe/awesomeapp:v1
        imagePullSecrets:
          - name: myregistrykey              #Use the created secret.
