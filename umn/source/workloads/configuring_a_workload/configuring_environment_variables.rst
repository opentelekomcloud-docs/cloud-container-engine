:original_name: cce_10_0113.html

.. _cce_10_0113:

Configuring Environment Variables
=================================

Container environment variables (for example, **DB_HOST=db.example.com**) are configuration parameters dynamically transferred when a container is running. They allow you to flexibly adjust application behaviors and settings without rebuilding the image. They provide:

-  **Dynamic application configuration**: You can inject different variable values (such as **DB_HOST=prod.db.com** or **DB_HOST=test.db.com**) to change application behaviors without rebuilding the image. Even if the container restarts due to scheduling, the configuration information is still valid, including database connection information, API endpoints, and function switches.
-  **Environment isolation configuration**: The same image is used with different environment variables to adapt to development, test, and production environments. For example, **ENV_MODE=production** enables the production environment configuration.
-  **Security management of sensitive data**: Sensitive data, such as passwords and keys, is securely transferred using mechanisms such as secrets. Sensitive data is not hardcoded in the image or code.

You can modify environment variables even after workloads are deployed, increasing flexibility in workload configuration. Configuring environment variables on CCE has the same function as specifying **ENV** in a Dockerfile. For more information, see `Defining Environment Variables for a Container <https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/>`__.

Adding Environment Variables on the Console
-------------------------------------------

Before using a ConfigMap or secret as an environment variable, ensure that the ConfigMap and secret have been created in the cluster. For details about how to create a ConfigMap and secret, see :ref:`Creating a ConfigMap <cce_10_0152>` and :ref:`Creating a Secret <cce_10_0153>`.

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click **Create Workload** in the upper right corner.

#. In **Container Information** (**Container Settings** > **Container Information**), choose **Environment Variables**.

#. Configure environment variables as needed. CCE clusters support multiple types of environment variables. For details, see :ref:`Table 1 <cce_10_0113__table12941439155317>`.

   -  To add environment variables one by one, click **Add Variable** and set **Type**, **Variable Name**, and **Variable Value/Reference**.
   -  To add environment variables in batches, click **Batch Edit Custom Variables**. Then, in the displayed dialog box, enter environment variables in the format of "Variable name=Variable value or variable reference".

   .. _cce_10_0113__fig164568529317:

   .. figure:: /_static/images/en-us_image_0000002434239996.png
      :alt: **Figure 1** Configuring environment variables

      **Figure 1** Configuring environment variables

   .. _cce_10_0113__table12941439155317:

   .. table:: **Table 1** Environment variables

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                          |
      +===================================+======================================================================================================================================================================================================================================================================================+
      | Type                              | Environment variable type. The options are as follows:                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   | -  **Custom**: Enter the environment variable name and value.                                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   | -  **Added from ConfigMap**: Import all key values in a ConfigMap as environment variables.                                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   |    As shown in :ref:`Figure 1 <cce_10_0113__fig164568529317>`, all key values in **configmap-example** are imported as environment variables.                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   | -  **Added from ConfigMap key**: Import a key value in a ConfigMap as the value of an environment variable.                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   |    As shown in :ref:`Figure 1 <cce_10_0113__fig164568529317>`, the value of **configmap_key** in **configmap-example** is imported as the value of the environment variable. If the value of **configmap_key** is **configmap_value**, the value of **key1** is **configmap_value**. |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   | -  **Added from secret**: Import all key values in a secret as environment variables.                                                                                                                                                                                                |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   |    As shown in :ref:`Figure 1 <cce_10_0113__fig164568529317>`, all key values in **secret-example** are imported as environment variables.                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   | -  **Added from secret key**: Import the value of a key in a secret as the value of an environment variable.                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   |    As shown in :ref:`Figure 1 <cce_10_0113__fig164568529317>`, the value of **secret_key** in **secret-example** is imported as the environment variable. If the value of **secret_key** is **secret_value**, the value of **key2** is **secret_value**.                             |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   | -  **Variable Value/Reference**: Use the field defined by a pod as the value of the environment variable.                                                                                                                                                                            |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   |    As shown in :ref:`Figure 1 <cce_10_0113__fig164568529317>`, the pod name is imported as the value of **key3**.                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   | -  **Resource Reference**: The value of **Request** or **Limit** defined by the container is used as the value of the environment variable.                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   |    As shown in :ref:`Figure 1 <cce_10_0113__fig164568529317>`, the CPU limit of **container-1** is imported as the value of **key4**.                                                                                                                                                |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Variable Name                     | Name of the environment variable set in the container.                                                                                                                                                                                                                               |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Variable Value/Reference          | The value of an environment variable or the value of an environment variable obtained from other sources.                                                                                                                                                                            |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Configure other parameters and click **Create Workload** in the lower right corner. After a period of time, the workload status changes to **Running**.

Adding Environment Variables Using kubectl
------------------------------------------

Assume that a ConfigMap and a secret are already available in the cluster. For details about how to create them, see :ref:`Creating a ConfigMap <cce_10_0152>` and :ref:`Creating a Secret <cce_10_0153>`.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Check the ConfigMap in the cluster. In this example, the ConfigMap is named **configmap-example**.

   .. code-block::

      kubectl get configmap configmap-example -oyaml

   Information similar to the following is displayed:

   .. code-block::

      apiVersion: v1
      data:
        configmap_key: configmap_value
      kind: ConfigMap
      ...

#. Check the secret in the cluster. In this example, the secret is named **secret-example**.

   .. code-block::

      kubectl get secret secret-example -oyaml

   Information similar to the following is displayed:

   .. code-block::

      apiVersion: v1
      data:
        secret_key: c2VjcmV0X3ZhbHVl              # c2VjcmV0X3ZhbHVl is the value of secret_value in Base64 mode.
      kind: Secret
      ...

#. Create a file named **nginx-deployment.yaml**. **nginx-deployment.yaml** is an example file name, and you can rename it as needed.

   .. code-block::

      vi nginx-deployment.yaml

   Configure environment variables in the workload YAML file. The following is an example:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: env-example
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: env-example
        template:
          metadata:
            labels:
              app: env-example
          spec:
            containers:
              - name: container-1
                image: nginx:alpine
                imagePullPolicy: Always
                resources:
                  requests:
                    cpu: 250m
                    memory: 512Mi
                  limits:
                    cpu: 250m
                    memory: 512Mi
                env:
                  - name: key                     # Define the environment variable name and value.
                    value: value
                  - name: key1                    # Import the value of a key in the ConfigMap as the value of an environment variable.
                    valueFrom:
                      configMapKeyRef:
                        name: configmap-example
                        key: configmap_key
                  - name: key2                    # Import the value of a key in the secret as the value of an environment variable.
                    valueFrom:
                      secretKeyRef:
                        name: secret-example
                        key: secret_key
                  - name: key3                    # Use the field defined by the pod as the value of an environment variable.
                    valueFrom:
                      fieldRef:
                        apiVersion: v1
                        fieldPath: metadata.name
                  - name: key4                    # Use the value of the resource request or limit defined in the container as the value of an environment variable.
                    valueFrom:
                      resourceFieldRef:
                        containerName: container1
                        resource: limits.cpu
                        divisor: 1
                envFrom:
                  - configMapRef:                 # Import all key values in a ConfigMap as environment variables.
                      name: configmap-example
                  - secretRef:                    # Import all key values in a secret as environment variables.
                      name: secret-example
            imagePullSecrets:
              - name: default-secret

#. Create the Deployment.

   .. code-block::

      kubectl create -f nginx-deployment.yaml

   If information similar to the following is displayed, the workload is being created:

   .. code-block::

      deployment.apps/env-example created

#. Check the pod name.

   .. code-block::

      kubectl get pod

   Information similar to the following is displayed:

   .. code-block::

      NAME                           READY   STATUS    RESTARTS   AGE
      env-example-695b759569-lx9jp   1/1     Running   0          17m

#. View environment variables in the pod.

   .. code-block::

      kubectl exec env-example-695b759569-lx9jp  -- printenv

   If information similar to the following is displayed, the environment variables are configured successfully:

   .. code-block::

      / # env
      key=value                             # Custom environment variable
      key1=configmap_value                  # Added from a ConfigMap key
      key2=secret_value                     # Added from a secret key
      key3=env-example-695b759569-lx9jp     # metadata.name defined by the pod
      key4=1                                # limits.cpu defined by container1. The value is rounded up, in unit of cores.
      configmap_key=configmap_value         # Added from a ConfigMap. The key value in the original ConfigMap key is imported.
      secret_key=secret_value               # Added from a secret. The key value in the original secret is imported.
