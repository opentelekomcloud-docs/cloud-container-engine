:original_name: cce_01_0009.html

.. _cce_01_0009:

Using a Third-Party Image
=========================

Scenario
--------

CCE allows you to create workloads using images pulled from third-party image repositories.

Generally, a third-party image repository can be accessed only after authentication (using your account and password). CCE uses the secret-based authentication to pull images. Therefore, you need to create a secret for an image repository before pulling images from the repository.

Prerequisites
-------------

The node where the workload is running is accessible from public networks. You can access public networks through :ref:`LoadBalancer <cce_01_0014>`.

Using the Console
-----------------

#. .. _cce_01_0009__li16481144064414:

   Create a secret for accessing a third-party image repository.

   In the navigation pane, choose **Configuration Center** > **Secret**, and click **Create Secret**. **Type** must be set to **kubernetes.io/dockerconfigjson**. For details, see :ref:`Creating a Secret <cce_01_0153>`.

   Enter the user name and password used to access the third-party image repository.

#. Create a workload. For details, see :ref:`Creating a Deployment <cce_01_0047>` or :ref:`Creating a StatefulSet <cce_01_0048>`. If the workload will be created from a third-party image, set the image parameters as follows:

   a. Set **Secret Authentication** to **Yes**.
   b. Select the secret created in step :ref:`1 <cce_01_0009__li16481144064414>`.
   c. Enter the image address.

#. Click **Create**.

Using kubectl
-------------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

#. Create a secret of the dockercfg type using kubectl.

   .. code-block::

      kubectl create secret docker-registry myregistrykey --docker-server=DOCKER_REGISTRY_SERVER --docker-username=DOCKER_USER --docker-password=DOCKER_PASSWORD --docker-email=DOCKER_EMAIL

   In the preceding commands, **myregistrykey** indicates the secret name, and other parameters are described as follows:

   -  **DOCKER_REGISTRY_SERVER**: address of a third-party image repository, for example, **www.3rdregistry.com** or **10.10.10.10:443**
   -  **DOCKER_USER**: account used for logging in to a third-party image repository
   -  **DOCKER\_PASSWORD**: password used for logging in to a third-party image repository
   -  **DOCKER_EMAIL**: email of a third-party image repository

#. Use a third-party image to create a workload.

   A dockecfg secret is used for authentication when you obtain a private image. The following is an example of using the myregistrykey for authentication.

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
