:original_name: cce_10_0018.html

.. _cce_10_0018:

Using ICAgent to Collect Container Logs
=======================================

CCE allows you to use ICAgent to collect container logs of workloads. When a node is created, ICAgent (DaemonSet named **icagent** in the **kube-system** namespace of the cluster) is installed by default. You can use ICAgent to collect logs and report them to storage locations described in the following table.

+---------------------------------------------------------------------+-------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Log Storage Location                                                | Content to Be Collected | Configuration Method                                                                                                                                                                         |
+=====================================================================+=========================+==============================================================================================================================================================================================+
| AOM (This mode is not recommended because it is no longer evolved.) | - Container stdout logs | -  Container file logs: Create a collection policy in the workload. For details, see :ref:`Using ICAgent to Collect Container Logs and Report Them to AOM <cce_10_0018__section1951732710>`. |
|                                                                     |                         | -  Container stdout logs: Enable or disable **Send stdout Logs to AOM 1.0** in :ref:`Monitoring <cce_10_0836>`. You are advised to disable it.                                               |
|                                                                     | - Container file logs   |                                                                                                                                                                                              |
+---------------------------------------------------------------------+-------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| LTS                                                                 | - Container stdout logs | Create a collection policy in LTS.                                                                                                                                                           |
|                                                                     |                         |                                                                                                                                                                                              |
|                                                                     | - Container file logs   |                                                                                                                                                                                              |
|                                                                     |                         |                                                                                                                                                                                              |
|                                                                     | - Node logs             |                                                                                                                                                                                              |
|                                                                     |                         |                                                                                                                                                                                              |
|                                                                     | - Kubernetes events     |                                                                                                                                                                                              |
+---------------------------------------------------------------------+-------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Constraints
-----------

ICAgent only collects text log files in .log, .trace, and .out formats.

.. _cce_10_0018__section1951732710:

Using the Console
-----------------

#. When :ref:`creating a workload <cce_10_0047>`, choose **Logging** in **Container Information**.

#. Click |image1| to add a log policy.

   The following uses Nginx as an example. Log policies vary depending on workloads.

#. Set **Volume Type** to **hostPath** or **emptyDir**.

   .. table:: **Table 1** Parameters for configuring a log policy

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                        |
      +===================================+====================================================================================================================================================================================================================================================================================================================================================================+
      | Volume Type                       | -  **hostPath**: A host path is mounted to the specified container path (mount path). After it is mounted, you can view the container logs that have been saved there.                                                                                                                                                                                             |
      |                                   | -  **emptyDir**: A temporary path of the node is mounted to the specified path (mount path). After it is mounted, any logs in the temporary path that are not reported to AOM by the collector will be lost if the pod is deleted.                                                                                                                                 |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | hostPath                          | Enter a host path, for example, **/var/paas/sys/log/nginx**.                                                                                                                                                                                                                                                                                                       |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Mount Path                        | Container path (for example, **/tmp**) that the storage resources will be mounted to.                                                                                                                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   | .. important::                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   |    -  Do not mount a volume to a system directory such as **/** or **/var/run**, or an exception occurs. Mount the volume to an empty directory. If the directory is not empty, ensure that there are no files that affect container startup. If there are such files, they will be replaced, which will lead to a container startup or workload creation failure. |
      |                                   |    -  If the container is mounted to a high-risk directory, use an account with minimum permissions to start the container. Otherwise, high-risk files on the host may be damaged.                                                                                                                                                                                 |
      |                                   |    -  AOM collects only the first 20 logs that have been modified recently. It collects logs from 2 levels of subdirectories by default.                                                                                                                                                                                                                           |
      |                                   |    -  AOM only collects text log files in .log, .trace, and .out formats in the mount paths.                                                                                                                                                                                                                                                                       |
      |                                   |    -  For details about how to set permissions for mount points in a container, see `Configure a Security Context for a Pod or Container <https://kubernetes.io/docs/tasks/configure-pod-container/security-context/>`__.                                                                                                                                          |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Extended Host Path                | This parameter is mandatory only if **Volume Type** is set to **hostPath**.                                                                                                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   | Extended host paths contain pod IDs or container names to distinguish different containers into which the host path is mounted.                                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   | A level-3 directory is added to the original volume directory/subdirectory. You can easily obtain the files output by a single Pod.                                                                                                                                                                                                                                |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   | -  **None**: No extended path is configured.                                                                                                                                                                                                                                                                                                                       |
      |                                   | -  **PodUID**: ID of a pod.                                                                                                                                                                                                                                                                                                                                        |
      |                                   | -  **PodName**: name of a pod.                                                                                                                                                                                                                                                                                                                                     |
      |                                   | -  **PodUID/ContainerName**: ID of a pod or name of a container.                                                                                                                                                                                                                                                                                                   |
      |                                   | -  **PodName/ContainerName**: name of a pod or container.                                                                                                                                                                                                                                                                                                          |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Collection Path                   | A collection path narrows down the scope of collection to specified logs.                                                                                                                                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   | -  If no collection path is specified, log files in .log, .trace, and .out formats will be collected from the current path.                                                                                                                                                                                                                                        |
      |                                   | -  Entering \*\* indicates that all log files in .log, .trace, and .out formats will be recursively collected from the specified path and all subdirectories of five levels deep.                                                                                                                                                                                  |
      |                                   | -  Entering \* indicates fuzzy matching.                                                                                                                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   | Example: The collection path **/tmp/**/test*.log** indicates that all .log files prefixed with **test** will be collected from **/tmp** and subdirectories of five levels deep.                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   | .. caution::                                                                                                                                                                                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   |    CAUTION:                                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    Ensure that the version of ICAgent is 5.12.22 or later.                                                                                                                                                                                                                                                                                                         |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Log Dump                          | Log dump refers to rotating log files on a local host.                                                                                                                                                                                                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   | -  **Enable**: AOM scans log files every minute. When a log file exceeds 50 MB, it is dumped immediately. A new .zip file is generated in the directory where the log file resides. For a log file, AOM stores only the latest 20 .zip files. When the number of .zip files exceeds 20, earlier .zip files will be deleted.                                        |
      |                                   | -  **Disable**: AOM does not dump log files.                                                                                                                                                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   | .. note::                                                                                                                                                                                                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   |    -  AOM rotates log files using copytruncate. Before enabling log dumping, ensure that log files are written in the append mode. Otherwise, file holes may occur.                                                                                                                                                                                                |
      |                                   |    -  Currently, mainstream log components such as Log4j and Logback support log file rotation. If you have already set rotation for log files, skip the configuration. Otherwise, conflicts may occur.                                                                                                                                                            |
      |                                   |    -  You are advised to configure log file rotation for your own services to flexibly control the size and number of rolled files.                                                                                                                                                                                                                                |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**.

Example YAML
------------

You can set the container log storage path by defining a YAML file.

As shown below, an emptyDir volume is mounted to **/var/log/nginx**. In this way, the ICAgent collects logs in **/var/log/nginx**. **policy** is a custom field of CCE and allows the ICAgent to identify and collect logs.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: testlog
     namespace: default
   spec:
     selector:
       matchLabels:
         app: testlog
     template:
       replicas: 1
       metadata:
         labels:
           app: testlog
       spec:
         containers:
           - image: 'nginx:alpine'
             name: container-0
             resources:
               requests:
                 cpu: 250m
                 memory: 512Mi
               limits:
                 cpu: 250m
                 memory: 512Mi
             volumeMounts:
               - name: vol-log
                 mountPath: /var/log/nginx
                 policy:
                   logs:
                     rotate: ''
         volumes:
           - emptyDir: {}
             name: vol-log
         imagePullSecrets:
           - name: default-secret

The following shows how to use a hostPath volume. Compared with emptyDir, the type of **volumes** is changed to **hostPath**, and the path on the host needs to be configured for this hostPath volume. In the following example, **/tmp/log** on the host is mounted to **/var/log/nginx**. In this way, the ICAgent can collect logs in **/var/log/nginx**, and the logs are still stored in **/tmp/log**.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: testlog
     namespace: default
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: testlog
     template:
       metadata:
         labels:
           app: testlog
       spec:
         containers:
           - image: 'nginx:alpine'
             name: container-0
             resources:
               requests:
                 cpu: 250m
                 memory: 512Mi
               limits:
                 cpu: 250m
                 memory: 512Mi
             volumeMounts:
               - name: vol-log
                 mountPath: /var/log/nginx
                 readOnly: false
                 extendPathMode: PodUID
                 policy:
                   logs:
                     rotate: Hourly
                     annotations:
                       pathPattern: '**'

         volumes:
           - hostPath:
               path: /tmp/log
             name: vol-log
         imagePullSecrets:
           - name: default-secret

.. table:: **Table 2** Parameter description

   +-------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                           | Description           | Remarks                                                                                                                                                                                                                                                                                                                                                                                      |
   +=====================================+=======================+==============================================================================================================================================================================================================================================================================================================================================================================================+
   | extendPathMode                      | Extended host path    | Extended host paths contain pod IDs or container names to distinguish different containers into which the host path is mounted.                                                                                                                                                                                                                                                              |
   |                                     |                       |                                                                                                                                                                                                                                                                                                                                                                                              |
   |                                     |                       | A level-3 directory is added to the original volume directory/subdirectory. You can easily obtain the files output by a single Pod.                                                                                                                                                                                                                                                          |
   |                                     |                       |                                                                                                                                                                                                                                                                                                                                                                                              |
   |                                     |                       | -  **None**: No extended path is configured.                                                                                                                                                                                                                                                                                                                                                 |
   |                                     |                       | -  **PodUID**: ID of a pod.                                                                                                                                                                                                                                                                                                                                                                  |
   |                                     |                       | -  **PodName**: name of a pod.                                                                                                                                                                                                                                                                                                                                                               |
   |                                     |                       | -  **PodUID/ContainerName**: ID of a pod or name of a container.                                                                                                                                                                                                                                                                                                                             |
   |                                     |                       | -  **PodName/ContainerName**: name of a pod or container.                                                                                                                                                                                                                                                                                                                                    |
   +-------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | policy.logs.rotate                  | Log dump              | Log dump refers to rotating log files on a local host.                                                                                                                                                                                                                                                                                                                                       |
   |                                     |                       |                                                                                                                                                                                                                                                                                                                                                                                              |
   |                                     |                       | -  **Enable**: AOM scans log files every minute. When a log file exceeds 50 MB, it is dumped immediately. A new .zip file is generated in the directory where the log file resides. For a log file, AOM stores only the latest 20 .zip files. When the number of .zip files exceeds 20, earlier .zip files will be deleted. After the dump is complete, the log file in AOM will be cleared. |
   |                                     |                       | -  **Disable**: AOM does not dump log files.                                                                                                                                                                                                                                                                                                                                                 |
   |                                     |                       |                                                                                                                                                                                                                                                                                                                                                                                              |
   |                                     |                       | .. note::                                                                                                                                                                                                                                                                                                                                                                                    |
   |                                     |                       |                                                                                                                                                                                                                                                                                                                                                                                              |
   |                                     |                       |    -  AOM rotates log files using copytruncate. Before enabling log dumping, ensure that log files are written in the append mode. Otherwise, file holes may occur.                                                                                                                                                                                                                          |
   |                                     |                       |    -  Currently, mainstream log components such as Log4j and Logback support log file rotation. If you have already set rotation for log files, skip the configuration. Otherwise, conflicts may occur.                                                                                                                                                                                      |
   |                                     |                       |    -  You are advised to configure log file rotation for your own services to flexibly control the size and number of rolled files.                                                                                                                                                                                                                                                          |
   +-------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | policy.logs.annotations.pathPattern | Collection path       | A collection path narrows down the scope of collection to specified logs.                                                                                                                                                                                                                                                                                                                    |
   |                                     |                       |                                                                                                                                                                                                                                                                                                                                                                                              |
   |                                     |                       | -  If no collection path is specified, log files in .log, .trace, and .out formats will be collected from the current path.                                                                                                                                                                                                                                                                  |
   |                                     |                       | -  Entering \*\* indicates that all log files in .log, .trace, and .out formats will be recursively collected from the specified path and all subdirectories of five levels deep.                                                                                                                                                                                                            |
   |                                     |                       | -  Entering \* indicates fuzzy matching.                                                                                                                                                                                                                                                                                                                                                     |
   |                                     |                       |                                                                                                                                                                                                                                                                                                                                                                                              |
   |                                     |                       | Example: The collection path **/tmp/**/test*.log** indicates that all .log files prefixed with **test** will be collected from **/tmp** and subdirectories of five levels deep.                                                                                                                                                                                                              |
   |                                     |                       |                                                                                                                                                                                                                                                                                                                                                                                              |
   |                                     |                       | .. caution::                                                                                                                                                                                                                                                                                                                                                                                 |
   |                                     |                       |                                                                                                                                                                                                                                                                                                                                                                                              |
   |                                     |                       |    CAUTION:                                                                                                                                                                                                                                                                                                                                                                                  |
   |                                     |                       |    Ensure that the version of ICAgent is 5.12.22 or later.                                                                                                                                                                                                                                                                                                                                   |
   +-------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Viewing Logs
------------

After a log collection path is configured and the workload is created, the ICAgent collects log files from the configured path. The collection takes about 1 minute.

After the log collection is complete, go to the workload details page and click **Logs** in the upper right corner to view logs.

You can also view logs on the AOM console.

You can also run the **kubectl logs** command to view the container stdout.

-  View the logs of a specified pod.

   .. code-block::

      kubectl logs <pod_name> -n <namespace>

-  View the logs of a specified pod in real time.

   .. code-block::

      kubectl logs -f <pod_name> -n <namespace>

-  View the logs of a specified container in a specified pod.

   .. code-block::

      kubectl logs <pod_name> -c <container_name> -n <namespace>

-  View the logs of a specified container in a specified pod in real time.

   .. code-block::

      kubectl logs -f <pod_name> -c <container_name> -n <namespace>

.. |image1| image:: /_static/images/en-us_image_0000002631690632.png
