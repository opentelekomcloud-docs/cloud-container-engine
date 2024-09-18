:original_name: cce_10_0112.html

.. _cce_10_0112:

Configuring Container Health Check
==================================

Scenario
--------

Health check regularly checks the health status of containers during container running. If the health check function is not configured, a pod cannot detect application exceptions or automatically restart the application to restore it. This will result in a situation where the pod status is normal but the application in the pod is abnormal.

Kubernetes provides the following health check probes:

-  **Liveness probe** (livenessProbe): checks whether a container is still alive. It is similar to the **ps** command that checks whether a process exists. If the liveness check of a container fails, the cluster restarts the container. If the liveness check is successful, no operation is executed.
-  **Readiness probe** (readinessProbe): checks whether a container is ready to process user requests. Upon that the container is detected unready, service traffic will not be directed to the container. It may take a long time for some applications to start up before they can provide services. This is because that they need to load disk data or rely on startup of an external module. In this case, although the application process has started, the application cannot provide services. To address this issue, this health check probe is used. If the container readiness check fails, the cluster masks all requests sent to the container. If the container readiness check is successful, the container can be accessed.
-  **Startup probe** (startupProbe): checks when a containerized application has started. If such a probe is configured, it disables liveness and readiness checks until it succeeds, ensuring that those probes do not interfere with the application startup. This can be used to adopt liveness checks on slow starting containers, avoiding them getting terminated by the kubelet before they are started.

Check Method
------------

-  **HTTP request**

   This health check mode applies to containers that provide HTTP/HTTPS services. The cluster periodically initiates an HTTP/HTTPS GET request to such containers. If the return code of the HTTP/HTTPS response is within 200-399, the probe is successful. Otherwise, the probe fails. In this health check mode, you must specify a container listening port and an HTTP/HTTPS request path.

   For example, for a container that provides HTTP services, the HTTP check path is **/health-check**, the port is 80, and the host address is optional (which defaults to the container IP address). Here, 172.16.0.186 is used as an example, and we can get such a request: GET http://172.16.0.186:80/health-check. The cluster periodically initiates this request to the container. You can also add one or more headers to an HTTP request. For example, set the request header name to **Custom-Header** and the corresponding value to **example**.

-  **TCP port**

   For a container that provides TCP communication services, the cluster periodically establishes a TCP connection to the container. If the connection is successful, the probe is successful. Otherwise, the probe fails. In this health check mode, you must specify a container listening port.

   For example, if you have an Nginx container with service port 80, after you specify TCP port 80 for container listening, the cluster will periodically initiate a TCP connection to port 80 of the container. If the connection is successful, the probe is successful. Otherwise, the probe fails.

-  **CLI**

   CLI is an efficient tool for health check. When using the CLI, you must specify an executable command in a container. The cluster periodically runs the command in the container. If the command output is 0, the health check is successful. Otherwise, the health check fails.

   The CLI mode can be used to replace the HTTP request-based and TCP port-based health check.

   -  For a TCP port, you can use a program script to connect to a container port. If the connection is successful, the script returns **0**. Otherwise, the script returns **-1**.

   -  For an HTTP request, you can use the script command to run the **wget** command to detect the container.

      **wget http://127.0.0.1:80/health-check**

      Check the return code of the response. If the return code is within 200-399, the script returns **0**. Otherwise, the script returns **-1**.

      .. important::

         -  Put the program to be executed in the container image so that the program can be executed.
         -  If the command to be executed is a shell script, do not directly specify the script as the command, but add a script parser. For example, if the script is **/data/scripts/health_check.sh**, you must specify **sh/data/scripts/health_check.sh** for command execution.

-  **gRPC check**

   gRPC checks can configure startup, liveness, and readiness probes for your gRPC application without exposing any HTTP endpoint, nor do you need an executable. Kubernetes can connect to your workload via gRPC and obtain its status.

   .. important::

      -  The gRPC check is supported only in CCE clusters of v1.25 or later.
      -  To use gRPC for check, your application must support the `gRPC health checking protocol <https://github.com/grpc/grpc/blob/master/doc/health-checking.md>`__.
      -  Similar to HTTP and TCP probes, if the port is incorrect or the application does not support the health checking protocol, the check fails.

Common Parameters
-----------------

.. table:: **Table 1** Common parameters

   +--------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                            | Description                                                                                                                                                                                                                                                                |
   +======================================+============================================================================================================================================================================================================================================================================+
   | Period (periodSeconds)               | Indicates the probe detection period, in seconds.                                                                                                                                                                                                                          |
   |                                      |                                                                                                                                                                                                                                                                            |
   |                                      | For example, if this parameter is set to **30**, the detection is performed every 30 seconds.                                                                                                                                                                              |
   +--------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Delay (initialDelaySeconds)          | Check delay time in seconds. Set this parameter according to the normal startup time of services.                                                                                                                                                                          |
   |                                      |                                                                                                                                                                                                                                                                            |
   |                                      | For example, if this parameter is set to **30**, the health check will be started 30 seconds after the container is started. The time is reserved for containerized services to start.                                                                                     |
   +--------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Timeout (timeoutSeconds)             | Number of seconds after which the probe times out. Unit: second.                                                                                                                                                                                                           |
   |                                      |                                                                                                                                                                                                                                                                            |
   |                                      | For example, if this parameter is set to **10**, the timeout wait time for performing a health check is 10s. If the wait time elapses, the health check is regarded as a failure. If the parameter is left blank or set to **0**, the default timeout time is 1s.          |
   +--------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Success Threshold (successThreshold) | Minimum consecutive successes for the probe to be considered successful after having failed. For example, if this parameter is set to **1**, the workload status is normal only when the health check is successful for one consecutive time after the health check fails. |
   |                                      |                                                                                                                                                                                                                                                                            |
   |                                      | The default value is **1**, which is also the minimum value.                                                                                                                                                                                                               |
   |                                      |                                                                                                                                                                                                                                                                            |
   |                                      | The value of this parameter is fixed to **1** in **Liveness Probe** and **Startup Probe**.                                                                                                                                                                                 |
   +--------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Failure Threshold (failureThreshold) | Number of retry times when the detection fails.                                                                                                                                                                                                                            |
   |                                      |                                                                                                                                                                                                                                                                            |
   |                                      | Giving up in case of liveness probe means to restart the container. In case of readiness probe the pod will be marked Unready.                                                                                                                                             |
   |                                      |                                                                                                                                                                                                                                                                            |
   |                                      | The default value is **3**, and the minimum value is **1**.                                                                                                                                                                                                                |
   +--------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

YAML Example
------------

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
         httpGet:                     # Checking an HTTP request is used as an example.
           path: /healthz             # The HTTP check path is /healthz.
           port: 80                   # The check port number is 80.
           httpHeaders:               # (Optional) The request header name is Custom-Header and the value is Awesome.
           - name: Custom-Header
             value: Awesome
         initialDelaySeconds: 3
         periodSeconds: 3
       readinessProbe:                # Readiness probe
         exec:                        # Checking an execution command is used as an example.
           command:                   # Command to be executed
             - cat
             - /tmp/healthy
         initialDelaySeconds: 5
         periodSeconds: 5
       startupProbe:                  # Startup probe
         httpGet:                     # Checking an HTTP request is used as an example.
           path: /healthz             # The HTTP check path is /healthz.
           port: 80                   # The check port number is 80.
         failureThreshold: 30
         periodSeconds: 10
