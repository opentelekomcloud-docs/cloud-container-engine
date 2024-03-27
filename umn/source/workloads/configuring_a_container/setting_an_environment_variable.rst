:original_name: cce_10_0113.html

.. _cce_10_0113:

Setting an Environment Variable
===============================

Scenario
--------

An environment variable is a variable whose value can affect the way a running container will behave. You can modify environment variables even after workloads are deployed, increasing flexibility in workload configuration.

The function of setting environment variables on CCE is the same as that of specifying **ENV** in a Dockerfile.

.. important::

   After a container is started, do not modify configurations in the container. If configurations in the container are modified (for example, passwords, certificates, and environment variables of a containerized application are added to the container), the configurations will be lost after the container restarts and container services will become abnormal. An example scenario of container restart is pod rescheduling due to node anomalies.

   Configurations must be imported to a container as arguments. Otherwise, configurations will be lost after the container restarts.

Environment variables can be set in the following modes:

-  **Custom**: Enter the environment variable name and parameter value.
-  **Added from ConfigMap key**: Import all keys in a ConfigMap as environment variables.
-  **Added from ConfigMap**: Import a key in a ConfigMap as the value of an environment variable. As shown in :ref:`Figure 1 <cce_10_0113__fig164568529317>`, if you import **configmap_value** of **configmap_key** in a ConfigMap as the value of environment variable **key1**, an environment variable named **key1** whose value is **configmap_value** exists in the container.
-  **Added from secret**: Import all keys in a secret as environment variables.
-  **Added from secret key**: Import the value of a key in a secret as the value of an environment variable. As shown in :ref:`Figure 1 <cce_10_0113__fig164568529317>`, if you import **secret_value** of **secret_key** in secret **secret-example** as the value of environment variable **key2**, an environment variable named **key2** whose value is **secret_value** exists in the container.
-  **Variable value/reference**: Use the field defined by a pod as the value of the environment variable. As shown in :ref:`Figure 1 <cce_10_0113__fig164568529317>`, if the pod name is imported as the value of environment variable **key3**, an environment variable named **key3** exists in the container and its value is the pod name.
-  **Resource Reference**: The value of **Request** or **Limit** defined by the container is used as the value of the environment variable. As shown in :ref:`Figure 1 <cce_10_0113__fig164568529317>`, if you import the CPU limit of **container-1** as the value of environment variable **key4**, an environment variable named **key4** exists in the container and its value is the CPU limit of **container-1**.

Adding Environment Variables
----------------------------

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. When creating a workload, modify the container information in the **Container Settings** area and click the **Environment Variables** tab.

#. Configure environment variables.

   .. _cce_10_0113__fig164568529317:

   .. figure:: /_static/images/en-us_image_0000001695896581.png
      :alt: **Figure 1** Configuring environment variables

      **Figure 1** Configuring environment variables

YAML Example
------------

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
               - name: key                     # Custom
                 value: value
               - name: key1                    # Added from ConfigMap key
                 valueFrom:
                   configMapKeyRef:
                     name: configmap-example
                     key: key1
               - name: key2                    # Added from secret key
                 valueFrom:
                   secretKeyRef:
                     name: secret-example
                     key: key2
               - name: key3                    # Variable reference, which uses the field defined by a pod as the value of the environment variable.
                 valueFrom:
                   fieldRef:
                     apiVersion: v1
                     fieldPath: metadata.name
               - name: key4                    # Resource reference, which uses the field defined by a container as the value of the environment variable.
                 valueFrom:
                   resourceFieldRef:
                     containerName: container1
                     resource: limits.cpu
                     divisor: 1
             envFrom:
               - configMapRef:                 # Added from ConfigMap
                   name: configmap-example
               - secretRef:                    # Added from secret
                   name: secret-example
         imagePullSecrets:
           - name: default-secret

Viewing Environment Variables
-----------------------------

If the contents of **configmap-example** and **secret-example** are as follows:

.. code-block::

   $ kubectl get configmap configmap-example -oyaml
   apiVersion: v1
   data:
     configmap_key: configmap_value
   kind: ConfigMap
   ...

   $ kubectl get secret secret-example -oyaml
   apiVersion: v1
   data:
     secret_key: c2VjcmV0X3ZhbHVl              # c2VjcmV0X3ZhbHVl is the value of secret_value in Base64 mode.
   kind: Secret
   ...

The environment variables in the pod are as follows:

.. code-block::

   $ kubectl get pod
   NAME                           READY   STATUS    RESTARTS   AGE
   env-example-695b759569-lx9jp   1/1     Running   0          17m

   $ kubectl exec env-example-695b759569-lx9jp  -- printenv
   / # env
   key=value                             # Custom environment variable
   ey1=configmap_value                  # Added from ConfigMap key
   key2=secret_value                     # Added from secret key
   key3=env-example-695b759569-lx9jp     # metadata.name defined by the pod
   key4=1                                # limits.cpu defined by container1. The value is rounded up, in unit of cores.
   configmap_key=configmap_value         # Added from ConfigMap. The key value in the original ConfigMap key is directly imported.
   secret_key=secret_value               # Added from key. The key value in the original secret is directly imported.
