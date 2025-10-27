:original_name: cce_10_0112.html

.. _cce_10_0112:

Configuring Container Health Check
==================================

Scenario
--------

Health check regularly checks the health of containers when the containers are running. If health check is not configured, a pod cannot detect application exceptions or automatically restart the application to recover it. As a result, the pod may be in the **Running** state, but the application is unavailable or abnormal.

Kubernetes provides three types of health check probes to monitor the applications in containers for system stability and high availability.

-  **Liveness probe**: checks whether a container is still alive. It is similar to the **ps** command that checks whether a process exists. If a liveness probe fails, the cluster restarts the container. If the liveness probe is successful, no further action is taken.
-  **Readiness probe**: checks whether the application in a container is ready to receive traffic. In some scenarios, an application has started, but it is not ready to provide services because a large amount of disk data needs to be loaded or external services need to be initialized. In this case, you can use a readiness probe to prevent traffic from being routed to the application. If the readiness probe fails, the CCE cluster temporarily removes the container from the endpoint list of the Service to block external requests. If the readiness probe is successful, the container is considered ready and can receive traffic.
-  **Startup probe**: checks whether the application in a container has started. The cluster starts the liveness and readiness checks only when the startup probe is successful to ensure that the checks do not affect the application startup. This kind of probe works well for containers that take a long time to start. They can effectively prevent containers from being incorrectly considered as abnormal and terminated before the initialization is complete.

For more information, see `Liveness, Readiness, and Startup Probes <https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/>`__ and `Configure Liveness, Readiness and Startup Probes <https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/>`__.


Configuring Container Health Check
----------------------------------

#. Log in to the CCE console.
#. When creating a workload, select **Health Check** in **Container Information**.
#. Select and configure a proper probe based on your requirements. The parameters of the liveness, readiness, and startup probes are basically the same. **A liveness probe is used as an example to describe the parameters. The parameter meanings of other probes are the same.**

   .. table:: **Table 1** Probe parameter

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                        |
      +===================================+====================================================================================================================================================================================================================================================================================================================================================================================+
      | Check Method                      | There are four options. Select one based on your service scenario. For details about the specific parameters of each check method, see "Specific Parameters" in this section. For details about common parameters such as the check period and delay, see :ref:`Common Parameters <cce_10_0112__section2050653544516>`.                                                            |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   | -  :ref:`HTTP <cce_10_0112__li19505918465>`: applies to a container that provides services over HTTP/HTTPS. The cluster will periodically initiate an HTTP/HTTPS GET request to the container. If the HTTP/HTTPS response status code is within 200-399, the probe is successful. Otherwise, the probe fails. You must specify the port that the container listens on.             |
      |                                   | -  :ref:`TCP <cce_10_0112__li92491637166>`: applies to a container that provides services over TCP (such as databases, caches, and custom TCP services). The cluster will periodically establish a TCP connection to the container. If the connection is successful, the probe is successful. Otherwise, the probe fails. You must specify the port that the container listens on. |
      |                                   | -  :ref:`Command <cce_10_0112__li104061647154310>`: You must specify an executable command in a container. The cluster will periodically run the command in the container. If the command output is **0**, the health check is successful. Otherwise, the health check fails.                                                                                                      |
      |                                   | -  :ref:`GRPC Check <cce_10_0112__li198471623132818>`: applies to gRPC applications. You do not need to expose HTTP ports or depend on external executable scripts. The container health check can be implemented through standard gRPC APIs.                                                                                                                                      |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Configure other parameters and click **Create Workload** in the lower right corner. If the workload is in the **Running** state, the startup command is successfully executed.

Specific Parameters
-------------------

-  .. _cce_10_0112__li19505918465:

   **HTTP**

   This option applies to a container that provides services over HTTP/HTTPS. The cluster will periodically send an HTTP/HTTPS GET request to the container and determine the probe results based on HTTP status codes. The details are as follows:

   -  If the response status code is within 200-399, the probe is successful.
   -  If the response status code is not within 200-399, the probe fails.

   The following describes the parameters specific to the HTTP request-based check. The example values in :ref:`Table 2 <cce_10_0112__table173541559105514>` indicate that the cluster periodically sends "GET http://172.16.0.186:80/health-check" to the container to check its health.

   .. _cce_10_0112__table173541559105514:

   .. table:: **Table 2** Parameters specific to the HTTP request-based check

      +--------------+---------------+---------------------------------------------------------------------------------------------------------------+
      | Parameter    | Example Value | Description                                                                                                   |
      +==============+===============+===============================================================================================================+
      | Path         | /health-check | HTTP or HTTPS request path for health check. Use an absolute path starting with a slash (/).                  |
      +--------------+---------------+---------------------------------------------------------------------------------------------------------------+
      | Port         | 80            | (Mandatory) Container listening port, which is used to locate the container in the pod.                       |
      +--------------+---------------+---------------------------------------------------------------------------------------------------------------+
      | Host Address | 172.16.0.186  | IP address of the requested host. If this parameter is not set, the IP address of the pod is used by default. |
      +--------------+---------------+---------------------------------------------------------------------------------------------------------------+
      | Protocol     | HTTP          | Protocol used to send requests. The value must match the service type provided by the container.              |
      +--------------+---------------+---------------------------------------------------------------------------------------------------------------+

-  .. _cce_10_0112__li92491637166:

   **TCP**

   This option applies to a container that provides services over TCP (such as databases, caches, and custom TCP services). The cluster will periodically establish a TCP connection to the container to check the container health. If the connection is successful, the probe is successful. Otherwise, the probe fails. You must specify the port that the container listens on.

   The following describes the parameters specific to the TCP port-based check. Assume that the listening port of an Nginx container is 80. If a TCP probe is configured for the container and the probe port is set to **80**, the cluster will periodically establish a TCP connection to the port. If the connection is successful, the probe is successful. Otherwise, the probe fails.

   .. table:: **Table 3** Parameters specific to the TCP port-based check

      +-----------+---------------+-----------------------------------------------------------------------------------------+
      | Parameter | Example Value | Description                                                                             |
      +===========+===============+=========================================================================================+
      | Port      | 80            | (Mandatory) Container listening port, which is used to locate the container in the pod. |
      +-----------+---------------+-----------------------------------------------------------------------------------------+

-  .. _cce_10_0112__li104061647154310:

   **Command**

   This option is a flexible health check method that allows you to specify the command in a container. The cluster will periodically run the command in the container to check the container status. If the command output is **0**, the health check is successful. Otherwise, the health check fails.

   For TCP port- and HTTP request-based checks, you can also specify commands to achieve similar effects. For example, the example values in :ref:`Table 4 <cce_10_0112__table95424917398>` can achieve the effect of HTTP request-based checks.

   .. _cce_10_0112__table95424917398:

   .. table:: **Table 4** Parameters specific to the command-based check

      +-----------------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter             | Example Value                                            | Description                                                                                                                                                                                                                                                                                                                                                                                                              |
      +=======================+==========================================================+==========================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Command               | /bin/sh                                                  | Command executed in the container to check the container status.                                                                                                                                                                                                                                                                                                                                                         |
      |                       |                                                          |                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                       | -c                                                       | .. note::                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                       |                                                          |                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                       | curl -sf http://172.16.0.186:80/health-check \|\| exit 1 |    -  Before using this method, you must pack the program or tool into the container image. The cluster will directly execute the command in the container, so the file systems of the host machine or other containers cannot be accessed. If the program or tool (such as **curl**, **nc**, or a custom script) that the command depends on is not included in the image, "Command not found" is displayed.            |
      |                       |                                                          |    -  If the command is a shell script, you must specify the script parser. The cluster does not execute probes in an interactive terminal environment. For this reason, you cannot directly execute the script as a command. You must use the script editor to invoke the script. For example, if the script is located in **/data/scripts/health_check.sh**, you need to execute **sh /data/scripts/health_check.sh**. |
      +-----------------------+----------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

-  .. _cce_10_0112__li198471623132818:

   **gRPC Check**

   This option applies to gRPC applications. You do not need to expose HTTP ports or depend on external executable scripts. The container health check can be implemented through standard gRPC APIs. A gRPC check can be used only if the following conditions are met:

   -  The CCE clusters are of v1.25 or later.
   -  Your application supports the `gRPC health check protocol <https://github.com/grpc/grpc/blob/master/doc/health-checking.md>`__.
   -  The port is correct. Similar to HTTP request- and TCP port-based checks, if the port is incorrect, the application does not support the health check protocol, or there are another configuration error, the probe fails.

   .. table:: **Table 5** Parameters specific to a gRPC check

      +-----------+---------------+-----------------------------------------------------------------------------------------+
      | Parameter | Example Value | Description                                                                             |
      +===========+===============+=========================================================================================+
      | Port      | 80            | (Mandatory) Container listening port, which is used to locate the container in the pod. |
      +-----------+---------------+-----------------------------------------------------------------------------------------+

.. _cce_10_0112__section2050653544516:

Common Parameters
-----------------

.. table:: **Table 6** Common parameters

   +--------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                            | Description                                                                                                                                                                                                                                                    | Example YAML                                                                                                                        |
   +======================================+================================================================================================================================================================================================================================================================+=====================================================================================================================================+
   | Period (periodSeconds)               | Probe detection period, in seconds.                                                                                                                                                                                                                            | The following uses a TCP port-based check of a liveness probe as an example. The configurations of other check methods are similar. |
   |                                      |                                                                                                                                                                                                                                                                |                                                                                                                                     |
   |                                      | For example, if this parameter is set to **30**, the probe is performed every 30 seconds.                                                                                                                                                                      | .. code-block::                                                                                                                     |
   |                                      |                                                                                                                                                                                                                                                                |                                                                                                                                     |
   |                                      |                                                                                                                                                                                                                                                                |    ...                                                                                                                              |
   |                                      |                                                                                                                                                                                                                                                                |    livenessProbe:                                                                                                                   |
   |                                      |                                                                                                                                                                                                                                                                |      tcpSocket:                                                                                                                     |
   |                                      |                                                                                                                                                                                                                                                                |        port: 80                                                                                                                     |
   |                                      |                                                                                                                                                                                                                                                                |      initialDelaySeconds: 0                                                                                                         |
   |                                      |                                                                                                                                                                                                                                                                |        timeoutSeconds: 1                                                                                                            |
   |                                      |                                                                                                                                                                                                                                                                |        periodSeconds: 10                                                                                                            |
   |                                      |                                                                                                                                                                                                                                                                |        successThreshold: 1                                                                                                          |
   |                                      |                                                                                                                                                                                                                                                                |        failureThreshold: 3                                                                                                          |
   |                                      |                                                                                                                                                                                                                                                                |    ...                                                                                                                              |
   +--------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------+
   | Delay (initialDelaySeconds)          | Time reserved for service program startup, in seconds.                                                                                                                                                                                                         |                                                                                                                                     |
   |                                      |                                                                                                                                                                                                                                                                |                                                                                                                                     |
   |                                      | For example, if this parameter is set to **30**, the health check starts 30 seconds after the container starts.                                                                                                                                                |                                                                                                                                     |
   +--------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------+
   | Timeout (timeoutSeconds)             | The timeout duration of a health check, in seconds. If you set this parameter to **0** or do not specify any value, the default value (**1**) is used.                                                                                                         |                                                                                                                                     |
   |                                      |                                                                                                                                                                                                                                                                |                                                                                                                                     |
   |                                      | Example: Value **10** indicates that the health check timeout duration is 10s. If the health check takes longer than this value, the check is considered failed.                                                                                               |                                                                                                                                     |
   +--------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------+
   | Success Threshold (successThreshold) | The minimum number of consecutive successes for the container to be considered healthy after a health check failure. The default value is **1**, and the minimum value is **1**. This parameter must be set to **1** for both the liveness and startup probes. |                                                                                                                                     |
   |                                      |                                                                                                                                                                                                                                                                |                                                                                                                                     |
   |                                      | For example, if this parameter is set to **1**, the workload will be restored to the normal state if the health check succeeds for one time after a failure.                                                                                                   |                                                                                                                                     |
   +--------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------+
   | Failure Threshold (failureThreshold) | The number of consecutive failures allowed before the container is considered unhealthy. The default value is **3**, and the minimum value is **1**.                                                                                                           |                                                                                                                                     |
   |                                      |                                                                                                                                                                                                                                                                |                                                                                                                                     |
   |                                      | -  For a liveness probe, if the number of consecutive failures reaches the threshold, the container is marked as unhealthy, and kubelet restarts the container.                                                                                                |                                                                                                                                     |
   |                                      | -  For a readiness probe, if the number of consecutive failures reaches the threshold, the pod is marked as not ready, removed from the endpoint list of the Service. In this case, the pod stops receiving new traffic, and its containers are not restarted. |                                                                                                                                     |
   +--------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------+

Example YAML
------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file for configuring a pod. In this example, the file name is **health_check.yaml**. You can change it as needed.

   .. code-block::

      vim health_check.yaml

   The file content is as follows:

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        labels:
          test: liveness
        name: liveness-http
      spec:
        containers:
        - name: liveness
          image: <image_address>
          args:
          - /server
          livenessProbe:                 # Liveness probe
            httpGet:                     # An HTTP request is used to check the containers.
              path: /healthz             # The HTTP check path is /healthz.
              port: 80                   # The health check port is 80.
              httpHeaders:               # (Optional) The request header name is Custom-Header and the value is Awesome.
              - name: Custom-Header
                value: Awesome
            initialDelaySeconds: 3
            periodSeconds: 3
          readinessProbe:                # Readiness probe
            exec:                        # A command is used to check the containers.
              command:                   # Command to be executed
                - cat
                - /tmp/healthy
            initialDelaySeconds: 5
            periodSeconds: 5
          startupProbe:                  # Startup probe
            httpGet:                     # An HTTP request is used to check the containers.
              path: /healthz             # The HTTP check path is /healthz.
              port: 80                   # The health check port is 80.
            failureThreshold: 30
            periodSeconds: 10

#. Create the pod.

   .. code-block::

      kubectl create -f health_check.yaml

   If information similar to the following is displayed, the pod is being created:

   .. code-block::

      pod/liveness-http created

#. Check the pod status.

   .. code-block::

      kubectl get deployment

   If the pod status is **Running** in the command output, the pod has been created.

   .. code-block::

      NAME               READY      STATUS       RESTARTS    AGE
      liveness-http      1/1        Running      1           4m59s
