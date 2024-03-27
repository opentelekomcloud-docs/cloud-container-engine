:original_name: cce_10_0016.html

.. _cce_10_0016:

Using a Secret
==============

.. important::

   Do not perform any operation on the following secrets. For details, see :ref:`Cluster Secrets <cce_10_0388>`.

   -  Do not operate secrets under kube-system.
   -  Do not operate default-secret and paas.elb in any of the namespaces. The default-secret is used to pull the private image of SWR, and the paas.elb is used to connect the service in the namespace to the ELB service.

-  :ref:`Setting Environment Variables of a Workload <cce_10_0016__section207271352141216>`
-  :ref:`Configuring the Data Volume of a Workload <cce_10_0016__section472505211214>`

The following example shows how to use a secret.

.. code-block::

   apiVersion: v1
   kind: Secret
   metadata:
     name: mysecret
   type: Opaque
   data:
     username: ******  #The value must be Base64-encoded.
     password: ******  #The value must be encoded using Base64.

.. important::

   -  When a secret is used in a pod, the pod and secret must be in the same cluster and namespace.

   -  When a secret is updated, Kubernetes updates the data in the data volume at the same time.

      However, when a secret data volume mounted in `subPath <https://kubernetes.io/docs/concepts/storage/volumes/#using-subpath>`__ mode is updated, Kubernetes cannot automatically update the data in the data volume.

.. _cce_10_0016__section207271352141216:

Setting Environment Variables of a Workload
-------------------------------------------

**Using the console**

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Workloads**. Then, click **Create Workload**.

   When creating a workload, click **Environment Variables** in the **Container Settings** area, and click |image1|.

   -  **Added from secret**: Select a secret and import all keys in the secret as environment variables.

   -  **Added from secret key**: Import the value of a key in a secret as the value of an environment variable.

      -  **Variable Name**: name of an environment variable in the workload. The name can be customized and is set to the key name selected in the secret by default.
      -  **Variable Value/Reference**: Select a secret and the key to be imported. The corresponding value is imported as a workload environment variable.

      For example, after you import the value of **username** in secret **mysecret** as the value of workload environment variable **username**, an environment variable named **username** exists in the container.

#. Set other workload parameters and click **Create Workload**.

   After the workload runs properly, :ref:`log in to the container <cce_10_00356>` and run the following statement to check whether the secret has been set as an environment variable of the workload:

   .. code-block::

      printenv username

   If the output is the same as the content in the secret, the secret has been set as an environment variable of the workload.

**Using kubectl**

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a file named **nginx-secret.yaml** and edit it.

   **vi nginx-secret.yaml**

   Content of the YAML file:

   -  **Added from a secret**: To add all data in a secret to environment variables, use the **envFrom** parameter. The keys in the secret will become names of environment variables in a workload.

      .. code-block::

         apiVersion: apps/v1
         kind: Deployment
         metadata:
           name: nginx-secret
         spec:
           replicas: 1
           selector:
             matchLabels:
               app: nginx-secret
           template:
             metadata:
               labels:
                 app: nginx-secret
             spec:
               containers:
               - name: container-1
                 image: nginx:latest
                 envFrom:                 # Use envFrom to specify a secret to be referenced by environment variables.
                 - secretRef:
                     name: mysecret       # Name of the referenced secret.
               imagePullSecrets:
               - name: default-secret

   -  **Added from a secret key**: When creating a workload, you can use a secret to set environment variables and use the **valueFrom** parameter to reference the key-value pair in the secret separately.

      .. code-block::

         apiVersion: apps/v1
         kind: Deployment
         metadata:
           name: nginx-secret
         spec:
           replicas: 1
           selector:
             matchLabels:
               app: nginx-secret
           template:
             metadata:
               labels:
                 app: nginx-secret
             spec:
               containers:
               - name: container-1
                 image: nginx:latest
                 env:                             # Set the environment variable in the workload.
                 - name: SECRET_USERNAME           # Name of the environment variable in the workload.
                   valueFrom:                    # Use valueFrom to specify a secret to be referenced by environment variables.
                     secretKeyRef:
                       name: mysecret       # Name of the referenced secret.
                       key: username        # Key in the referenced secret.
                 - name: SECRET_PASSWORD            # Add multiple environment variables to import them at the same time.
                   valueFrom:
                     secretKeyRef:
                       name: mysecret
                       key: password
               imagePullSecrets:
               - name: default-secret

#. Create a workload.

   **kubectl apply -f nginx-secret.yaml**

#. View the environment variables in the pod.

   a. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep nginx-secret

      Expected output:

      .. code-block::

         nginx-secret-***   1/1     Running   0              2m18s

   b. Run the following command to view the environment variables in the pod:

      .. code-block::

         kubectl exec nginx-secret-*** -- printenv SPECIAL_USERNAME SPECIAL_PASSWORD

      If the output is the same as the content in the secret, the secret has been set as an environment variable of the workload.

.. _cce_10_0016__section472505211214:

Configuring the Data Volume of a Workload
-----------------------------------------

You can mount a secret as a volume to the specified container path. Contents in a secret are user-defined. Before that, create a secret. For details, see :ref:`Creating a Secret <cce_10_0153>`.

**Using the console**

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane on the left, click **Workloads**. In the right pane, click the **Deployments** tab. Click **Create Workload** in the upper right corner.

   When creating a workload, click **Data Storage** in the **Container Settings** area. Click **Add Volume** and select **Secret** from the drop-down list.

#. Configure the parameters.

   .. table:: **Table 1** Mounting a Secret volume

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      +===================================+==============================================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Secret                            | Select the desired secret.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   | A secret must be created in advance. For details, see :ref:`Creating a Secret <cce_10_0153>`.                                                                                                                                                                                                                                                                                                                                                                                |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Add Container Path                | Configure the following parameters:                                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   | a. **Mount Path**: Enter a path of the container, for example, **/tmp**.                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |    This parameter indicates the container path to which a data volume will be mounted. Do not mount the volume to a system directory such as **/** or **/var/run**; this action may cause container errors. You are advised to mount the volume to an empty directory. If the directory is not empty, ensure that there are no files that affect container startup. Otherwise, the files will be replaced, causing container startup failures or workload creation failures. |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |    .. important::                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |       NOTICE:                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   |       If a volume is mounted to a high-risk directory, use an account with minimum permissions to start the container. Otherwise, high-risk files on the host machine may be damaged.                                                                                                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   | b. **Subpath**: Enter a subpath, for example, **tmp**.                                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |    -  A subpath is used to mount a local volume so that the same data volume is used in a single pod. If this parameter is left blank, the root path is used by default.                                                                                                                                                                                                                                                                                                     |
      |                                   |    -  The subpath can be the key and value of a ConfigMap or secret. If the subpath is a key-value pair that does not exist, the data import does not take effect.                                                                                                                                                                                                                                                                                                           |
      |                                   |    -  The data imported by specifying a subpath will not be updated along with the ConfigMap/secret updates.                                                                                                                                                                                                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   | c. Set the permission to **Read-only**. Data volumes in the path are read-only.                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   | You can click |image2| to add multiple paths and subpaths.                                                                                                                                                                                                                                                                                                                                                                                                                   |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

**Using kubectl**

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a file named **nginx-secret.yaml** and edit it.

   **vi nginx-secret.yaml**

   In the following example, the username and password in the **mysecret** secret are saved in the **/etc/foo** directory as files.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx-secret
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: nginx-secret
        template:
          metadata:
            labels:
              app: nginx-secret
          spec:
            containers:
            - name: container-1
              image: nginx:latest
              volumeMounts:
             - name: foo
               mountPath: /etc/foo          # Mount to the /etc/foo directory.
               readOnly: true
          volumes:
          - name: foo
            secret:
              secretName: mysecret      # Name of the referenced secret.

   You can also use the **items** field to control the mapping path of secret keys. For example, store username in the **/etc/foo/my-group/my-username** directory in the container.

   .. note::

      -  If you use the **items** field to specify the mapping path of the secret keys, the keys that are not specified will not be created as files. For example, if the **password** key in the following example is not specified, the file will not be created.
      -  If you want to use all keys in a secret, you must list all keys in the **items** field.
      -  All keys listed in the **items** field must exist in the corresponding secret. Otherwise, the volume is not created.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx-secret
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: nginx-secret
        template:
          metadata:
            labels:
              app: nginx-secret
          spec:
            containers:
            - name: container-1
              image: nginx:latest
              volumeMounts:
             - name: foo
               mountPath: /etc/foo          # Mount to the /etc/foo directory.
               readOnly: true
          volumes:
          - name: foo
            secret:
              secretName: mysecret      # Name of the referenced secret.
              items:
              - key: username      # Name of the referenced key.
                path: my-group/my-username    # Mapping path of the secret key

#. Create a workload.

   **kubectl apply -f nginx-secret.yaml**

#. After the workload runs properly, the **username** and **password** files are generated in the **/etc/foo** directory.

   a. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep nginx-secret

      Expected output:

      .. code-block::

         nginx-secret-***   1/1     Running   0              2m18s

   b. Run the following command to view the **username** or **password** file in the pod:

      .. code-block::

         kubectl exec nginx-secret-*** -- /etc/foo/username

      The expected output is the same as the content in the secret.

.. |image1| image:: /_static/images/en-us_image_0000001647417524.png
.. |image2| image:: /_static/images/en-us_image_0000001647576792.png
