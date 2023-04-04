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

   When a ConfigMap is used in a pod, the pod and ConfigMap must be in the same cluster and namespace.

.. _cce_10_0015__section1737733192813:

Setting Workload Environment Variables
--------------------------------------

When creating a workload, you can use a ConfigMap to set environment variables. The **valueFrom** parameter indicates the key-value pair to be referenced.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: configmap-pod-1
   spec:
     containers:
       - name: test-container
         image: busybox
         command: [ "/bin/sh", "-c", "env" ]
         env:
           - name: SPECIAL_LEVEL_KEY
             valueFrom:                             ## Use valueFrom to specify the value of the env that refers to the ConfigMap.
               configMapKeyRef:
                 name: cce-configmap                ## Name of the referenced configuration file.
                 key: SPECIAL_LEVEL                 ## Key of the referenced ConfigMap.
     restartPolicy: Never

If you need to define the values of multiple ConfigMaps as the environment variables of the pods, add multiple environment variable parameters to the pods.

.. code-block::

   env:
   - name: SPECIAL_LEVEL_KEY
     valueFrom:
       configMapKeyRef:
             name: cce-configmap
             key: SPECIAL_LEVEL
   - name: SPECIAL_TYPE_KEY
     valueFrom:
       configMapKeyRef:
             name: cce-configmap
             key: SPECIAL_TYPE

To add all data in a ConfigMap to environment variables, use the **envFrom** parameter. The keys in the ConfigMap will become names of environment variables in a pod.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: configmap-pod-2
   spec:
     containers:
       - name: test-container
         image: busybox
         command: [ "/bin/sh", "-c", "env" ]
         envFrom:
         - configMapRef:
             name: cce-configmap
     restartPolicy: Never

.. _cce_10_0015__section17930105710189:

Setting Command Line Parameters
-------------------------------

You can use a ConfigMap to set commands or parameter values for a container by using the environment variable substitution syntax $(VAR_NAME). The following shows an example.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: configmap-pod-3
   spec:
     containers:
       - name: test-container
         image: busybox
         command: [ "/bin/sh", "-c", "echo $(SPECIAL_LEVEL_KEY) $(SPECIAL_TYPE_KEY)" ]
         env:
           - name: SPECIAL_LEVEL_KEY
             valueFrom:
               configMapKeyRef:
                 name: cce-configmap
                 key: SPECIAL_LEVEL
           - name: SPECIAL_TYPE_KEY
             valueFrom:
               configMapKeyRef:
                 name: cce-configmap
                 key: SPECIAL_TYPE
     restartPolicy: Never

After the pod runs, the following information is displayed:

.. code-block::

   Hello CCE

.. _cce_10_0015__section1490261161916:

Attaching a ConfigMap to the Workload Data Volume
-------------------------------------------------

A ConfigMap can also be used in the data volume. You only need to attach the ConfigMap to the workload when creating the workload. After the mounting is complete, a configuration file with key as the file name and value as the file content is generated.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: configmap-pod-4
   spec:
     containers:
       - name: test-container
         image: busybox
         command: [ "/bin/sh", "-c", "ls /etc/config/" ]   ## Lists the file names in the directory.
         volumeMounts:
         - name: config-volume
           mountPath: /etc/config                          ## Attaches to the /etc/config directory.
     volumes:
       - name: config-volume
         configMap:
           name: cce-configmap
     restartPolicy: Never

After the pod is run, the **SPECIAL_LEVEL** and **SPECIAL_TYPE** files are generated in the **/etc/config** directory. The contents of the files are Hello and CCE, respectively. Also, the following file names will be displayed.

.. code-block::

   SPECIAL_TYPE
   SPECIAL_LEVEL

To mount a ConfigMap to a data volume, you can also perform operations on the CCE console. When creating a workload, set advanced settings for the container, choose **Data Storage** > **Local Volume**, click **Add Local Volume**, and select **ConfigMap**. For details, see :ref:`ConfigMap <cce_10_0377__section18638191594712>`.
