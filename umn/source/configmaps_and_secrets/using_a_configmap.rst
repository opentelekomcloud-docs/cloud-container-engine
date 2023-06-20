:original_name: cce_10_0015.html

.. _cce_10_0015:

Using a ConfigMap
=================

-  :ref:`Setting Workload Environment Variables <cce_10_0015__section1737733192813>`
-  :ref:`Setting Command Line Parameters <cce_10_0015__section17930105710189>`
-  :ref:`Attaching a ConfigMap to the Workload Data Volume <cce_10_0015__section1490261161916>`

The following example shows how to use a ConfigMap.

.. code-block::

   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: cce-configmap
   data:
     SPECIAL_LEVEL: Hello
     SPECIAL_TYPE: CCE

.. important::

   -  When a ConfigMap is used in a workload, the workload and ConfigMap must be in the same cluster and namespace.

   -  When a ConfigMap is mounted as a data volume and is updated, Kubernetes updates the data in the data volume at the same time.

      When a ConfigMap data volume mounted in `subPath <https://kubernetes.io/docs/concepts/storage/volumes/#using-subpath>`__ mode is updated, Kubernetes cannot automatically update the data in the data volume.

   -  When a ConfigMap is used as an environment variable, data can not be automatically updated when the ConfigMap is updated. To update the data, you need to restart the pod.

.. _cce_10_0015__section1737733192813:

Setting Workload Environment Variables
--------------------------------------

**Using the console**

#. Log in to the CCE console and access the cluster console.

#. In the navigation pane, choose **Workloads**. Then, click **Create Workload**.

   When creating a workload, click **Environment Variables** in the **Container Settings** area, and click |image1|.

   -  **Added from ConfigMap**: Select a ConfigMap to import all of its keys as environment variables.

   -  **Added from ConfigMap key**: Import a key in a ConfigMap as the value of an environment variable.

      -  **Variable Name**: name of an environment variable in the workload. The name can be customized and is set to the key name selected in the ConfigMap by default.
      -  **Variable Value/Reference**: Select a ConfigMap and the key to be imported. The corresponding value is imported as a workload environment variable.

      For example, after you import the value **Hello** of **SPECIAL_LEVEL** in ConfigMap **cce-configmap** as the value of workload environment variable **SPECIAL_LEVEL**, an environment variable named **SPECIAL_LEVEL** with its value **Hello** exists in the container.

#. Configure other workload parameters and click **Create Workload**.

   After the workload runs properly, :ref:`access the container <cce_10_00356>` and run the following command to check whether the ConfigMap has been set as an environment variable of the workload:

   .. code-block::

      printenv SPECIAL_LEVEL

   The example output is as follows:

   .. code-block::

      Hello

**Using kubectl**

#. According to :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`, configure the **kubectl** command to connect an ECS to the cluster.

#. Create a file named **nginx-configmap.yaml** and edit it.

   **vi nginx-configmap.yaml**

   Content of the YAML file:

   -  **Added from ConfigMap**: To add all data in a ConfigMap to environment variables, use the **envFrom** parameter. The keys in the ConfigMap will become names of environment variables in a pod.

      .. code-block::

         apiVersion: apps/v1
         kind: Deployment
         metadata:
           name: nginx-configmap
         spec:
           replicas: 1
           selector:
             matchLabels:
               app: nginx-configmap
           template:
             metadata:
               labels:
                 app: nginx-configmap
             spec:
               containers:
               - name: container-1
                 image: nginx:latest
                 envFrom:                      # Use envFrom to specify a ConfigMap to be referenced by environment variables.
                 - configMapRef:
                     name: cce-configmap       # Name of the referenced ConfigMap.
               imagePullSecrets:
               - name: default-secret

   -  **Added from a ConfigMap key**: When creating a workload, you can use a ConfigMap to set environment variables and use the **valueFrom** parameter to reference the key-value pair in the ConfigMap separately.

      .. code-block::

         apiVersion: apps/v1
         kind: Deployment
         metadata:
           name: nginx-configmap
         spec:
           replicas: 1
           selector:
             matchLabels:
               app: nginx-configmap
           template:
             metadata:
               labels:
                 app: nginx-configmap
             spec:
               containers:
               - name: container-1
                 image: nginx:latest
                 env:                             # Set environment variables in the workload.
                 - name: SPECIAL_LEVEL           # Name of the environment variable in the workload.
                   valueFrom:                    # Use valueFrom to specify an environment variable to reference a ConfigMap.
                     configMapKeyRef:
                       name: cce-configmap       # Name of the referenced ConfigMap.
                       key: SPECIAL_LEVEL        # Key in the referenced ConfigMap.
                 - name: SPECIAL_TYPE            # Add multiple environment variables. Multiple environment variables can be imported at the same time.
                   valueFrom:
                     configMapKeyRef:
                       name: cce-configmap
                       key: SPECIAL_TYPE
               imagePullSecrets:
               - name: default-secret

#. Create a workload.

   **kubectl apply -f nginx-configmap.yaml**

#. View the environment variables in the pod.

   a. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep nginx-configmap

      Expected output:

      .. code-block::

         nginx-configmap-***   1/1     Running   0              2m18s

   b. Run the following command to view the environment variables in the pod:

      .. code-block::

         kubectl exec nginx-configmap-*** -- printenv SPECIAL_LEVEL SPECIAL_TYPE

      Expected output:

      .. code-block::

         Hello
         CCE

      The ConfigMap has been set as an environment variable of the workload.

.. _cce_10_0015__section17930105710189:

Setting Command Line Parameters
-------------------------------

You can use a ConfigMap as an environment variable to set commands or parameter values for a container by using the environment variable substitution syntax $VAR_NAME.

**Using the console**

#. Log in to the CCE console and access the cluster console.

#. In the navigation pane, choose **Workloads**. Then, click **Create Workload**.

   When creating a workload, click **Environment Variables** in the **Container Settings** area, and click |image2|. In this example, select **Added from ConfigMap**.

   -  **Added from ConfigMap**: Select a ConfigMap to import all of its keys as environment variables.

#. Click **Lifecycle** in the **Container Settings** area, click the **Post-Start** tab on the right, and set the following parameters:

   -  **Processing Method**: **CLI**

   -  **Command**: Enter the following three command lines. *SPECIAL_LEVEL* and *SPECIAL_TYPE* are the environment variable names in the workload, that is, the key names in the **cce-configmap** ConfigMap.

      .. code-block::

         /bin/bash
         -c
         echo $SPECIAL_LEVEL $SPECIAL_TYPE > /usr/share/nginx/html/index.html

#. Configure other workload parameters and click **Create Workload**.

   After the workload runs properly, :ref:`access the container <cce_10_00356>` and run the following command to check whether the ConfigMap has been set as an environment variable of the workload:

   .. code-block::

      cat /usr/share/nginx/html/index.html

   The example output is as follows:

   .. code-block::

      Hello CCE

**Using kubectl**

#. According to :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`, configure the **kubectl** command to connect an ECS to the cluster.

#. Create a file named **nginx-configmap.yaml** and edit it.

   **vi nginx-configmap.yaml**

   As shown in the following example, the **cce-configmap** ConfigMap is imported to the workload. **SPECIAL_LEVEL** and **SPECIAL_TYPE** are environment variable names, that is, key names in the **cce-configmap** ConfigMap.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx-configmap
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: nginx-configmap
        template:
          metadata:
            labels:
              app: nginx-configmap
          spec:
            containers:
            - name: container-1
              image: nginx:latest
              lifecycle:
                postStart:
                  exec:
                    command: [ "/bin/sh", "-c", "echo $SPECIAL_LEVEL $SPECIAL_TYPE > /usr/share/nginx/html/index.html" ]
              envFrom:                      # Use envFrom to specify a ConfigMap to be referenced by environment variables.
              - configMapRef:
                  name: cce-configmap       # Name of the referenced ConfigMap.
            imagePullSecrets:
              - name: default-secret

#. Create a workload.

   **kubectl apply -f nginx-configmap.yaml**

#. After the workload runs properly, the following content is entered into the **/usr/share/nginx/html/index.html** file in the container:

   a. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep nginx-configmap

      Expected output:

      .. code-block::

         nginx-configmap-***   1/1     Running   0              2m18s

   b. Run the following command to view the environment variables in the pod:

      .. code-block::

         kubectl exec nginx-configmap-*** -- cat /usr/share/nginx/html/index.html

      Expected output:

      .. code-block::

         Hello CCE

.. _cce_10_0015__section1490261161916:

Attaching a ConfigMap to the Workload Data Volume
-------------------------------------------------

The data stored in a ConfigMap can be referenced in a volume of type ConfigMap. You can mount such a volume to a specified container path. The platform supports the separation of workload codes and configuration files. ConfigMap volumes are used to store workload configuration parameters. Before that, you need to create ConfigMaps in advance. For details, see :ref:`Creating a ConfigMap <cce_10_0152>`.

**Using the console**

#. Log in to the CCE console and access the cluster console.

#. In the navigation pane, choose **Workloads**. Then, click **Create Workload**.

   When creating a workload, click **Data Storage** in the **Container Settings** area. Click **Add Volume** and select **ConfigMap** from the drop-down list.

#. Set the local volume type to **ConfigMap** and set parameters for adding a local volume, as shown in :ref:`Table 1 <cce_10_0015__table1776324831114>`.

   .. _cce_10_0015__table1776324831114:

   .. table:: **Table 1** Mounting a ConfigMap volume

      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
      +===================================+=====================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Option                            | Select the desired ConfigMap name.                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | A ConfigMap must be created in advance. For details, see :ref:`Creating a ConfigMap <cce_10_0152>`.                                                                                                                                                                                                                                                                                                                                                                                                 |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Add Container Path                | Configure the following parameters:                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | a. **Container Path**: Enter the path of the container, for example, **/tmp**.                                                                                                                                                                                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |    This parameter indicates the container path to which a data volume will be mounted. Do not mount the volume to a system directory such as **/** or **/var/run**; this action may cause container errors. You are advised to mount the container to an empty directory. If the directory is not empty, ensure that there are no files affecting container startup in the directory. Otherwise, such files will be replaced, resulting in failures to start the container and create the workload. |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |    .. important::                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |       NOTICE:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                                   |       When the container is mounted to a high-risk directory, you are advised to use an account with minimum permissions to start the container; otherwise, high-risk files on the host machine may be damaged.                                                                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | b. **subPath**: Enter a subpath, for example, **tmp**.                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |    -  A subpath is used to mount a local volume so that the same data volume is used in a single pod.                                                                                                                                                                                                                                                                                                                                                                                               |
      |                                   |    -  The subpath can be the key and value of a ConfigMap or secret. If the subpath is a key-value pair that does not exist, the data import does not take effect.                                                                                                                                                                                                                                                                                                                                  |
      |                                   |    -  The data imported by specifying a subpath will not be updated along with the ConfigMap/secret updates.                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | c. Set the permission to **Read-only**. Data volumes in the path are read-only.                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | You can click |image3| to add multiple paths and subpaths.                                                                                                                                                                                                                                                                                                                                                                                                                                          |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

**Using kubectl**

#. According to :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`, configure the **kubectl** command to connect an ECS to the cluster.

#. Create a file named **nginx-configmap.yaml** and edit it.

   **vi nginx-configmap.yaml**

   As shown in the following example, after the ConfigMap volume is mounted, a configuration file with the key as the file name and value as the file content is generated in the **/etc/config** directory of the container.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx-configmap
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: nginx-configmap
        template:
          metadata:
            labels:
              app: nginx-configmap
          spec:
            containers:
            - name: container-1
              image: nginx:latest
              volumeMounts:
              - name: config-volume
                mountPath: /etc/config            # Mount to the /etc/config directory.
                readOnly: true
          volumes:
          - name: config-volume
            configMap:
              name: cce-configmap                 # Name of the referenced ConfigMap.

#. Create a workload.

   **kubectl apply -f nginx-configmap.yaml**

#. After the workload runs properly, the **SPECIAL_LEVEL** and **SPECIAL_TYPE** files are generated in the **/etc/config** directory. The contents of the files are **Hello** and **CCE**, respectively.

   a. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep nginx-configmap

      Expected output:

      .. code-block::

         nginx-configmap-***   1/1     Running   0              2m18s

   b. Run the following command to view the **SPECIAL_LEVEL** or **SPECIAL_TYPE** file in the pod:

      .. code-block::

         kubectl exec nginx-configmap-*** -- /etc/config/SPECIAL_LEVEL

      Expected output:

      .. code-block::

         Hello

.. |image1| image:: /_static/images/en-us_image_0000001568822917.png
.. |image2| image:: /_static/images/en-us_image_0000001568902649.png
.. |image3| image:: /_static/images/en-us_image_0000001569023025.png
