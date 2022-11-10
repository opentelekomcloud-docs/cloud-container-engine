:original_name: cce_01_0105.html

.. _cce_01_0105:

Setting Container Lifecycle Parameters
======================================

Scenario
--------

CCE provides callback functions for the lifecycle management of containerized applications. For example, if you want a container to perform a certain operation before stopping, you can register a hook function.

CCE provides the following lifecycle callback functions:

-  **Start Command**: executed to start a container. For details, see :ref:`Setting Container Startup Commands <cce_01_0008>`.
-  **Post-Start**: executed immediately after a container is started. For details, see :ref:`Post-Start Processing <cce_01_0105__section15243544163715>`.
-  **Pre-Stop**: executed before a container is stopped. The pre-stop processing function helps you ensure that the services running on the pods can be completed in advance in the case of pod upgrade or deletion. For details, see :ref:`Pre-Stop Processing <cce_01_0105__section2334114473712>`.

Commands and Parameters Used to Run a Container
-----------------------------------------------

A Docker image has metadata that stores image information. If lifecycle commands and arguments are not set, CCE runs the default commands and arguments, that is, Docker instructions **ENTRYPOINT** and **CMD**, provided during image creation.

If the commands and arguments used to run a container are set during application creation, the default commands **ENTRYPOINT** and **CMD** are overwritten during image build. The rules are as follows:

.. table:: **Table 1** Commands and parameters used to run a container

   +------------------+--------------+----------------------------+-------------------------------+--------------------+
   | Image Entrypoint | Image CMD    | Command to Run a Container | Parameters to Run a Container | Command Executed   |
   +==================+==============+============================+===============================+====================+
   | [touch]          | [/root/test] | Not set                    | Not set                       | [touch /root/test] |
   +------------------+--------------+----------------------------+-------------------------------+--------------------+
   | [touch]          | [/root/test] | [mkdir]                    | Not set                       | [mkdir]            |
   +------------------+--------------+----------------------------+-------------------------------+--------------------+
   | [touch]          | [/root/test] | Not set                    | [/opt/test]                   | [touch /opt/test]  |
   +------------------+--------------+----------------------------+-------------------------------+--------------------+
   | [touch]          | [/root/test] | [mkdir]                    | [/opt/test]                   | [mkdir /opt/test]  |
   +------------------+--------------+----------------------------+-------------------------------+--------------------+

Startup Commands
----------------

By default, the default command during image start. To run a specific command or rewrite the default image value, you must perform specific settings: For details, see :ref:`Setting Container Startup Commands <cce_01_0008>`.

.. _cce_01_0105__section15243544163715:

Post-Start Processing
---------------------

#. Log in to the CCE console. Expand **Lifecycle** when adding a container during workload creation.

#. Set the post-start processing parameters, as listed in :ref:`Table 2 <cce_01_0105__table823614643810>`.

   .. _cce_01_0105__table823614643810:

   .. table:: **Table 2** Post-start processing parameters

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      +===================================+==========================================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | CLI                               | Set commands to be executed in the container for post-start processing. The command format is **Command Args[1] Args[2]...**. **Command** is a system command or a user-defined executable program. If no path is specified, an executable program in the default path will be selected. If multiple commands need to be executed, write the commands into a script for execution. **Commands that are executed in the background or asynchronously are not supported.** |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   | Example command:                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   | .. code-block::                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   |    exec:                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
      |                                   |      command:                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
      |                                   |      - /install.sh                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                                   |      - install_agent                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   | Enter **/install install_agent** in the script. This command indicates that **install.sh** will be executed after the container is created successfully.                                                                                                                                                                                                                                                                                                                 |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | HTTP request                      | Send an HTTP request for post-start processing. The related parameters are described as follows:                                                                                                                                                                                                                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   | -  **Path**: (optional) request URL.                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | -  **Port**: (mandatory) request port.                                                                                                                                                                                                                                                                                                                                                                                                                                   |
      |                                   | -  **Host Address**: (optional) IP address of the request. The default value is the IP address of the node where the container resides.                                                                                                                                                                                                                                                                                                                                  |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_01_0105__section2334114473712:

Pre-Stop Processing
-------------------

#. When creating a workload and adding a container, expand **Lifecycle**.
#. Set **pre-stop** parameters, as shown in :ref:`Table 2 <cce_01_0105__table823614643810>`.

   .. table:: **Table 3** Pre-stop parameters

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                      |
      +===================================+==================================================================================================================================================================================================================================================================================================================================================================================+
      | CLI                               | Set commands to be executed in the container for pre-stop processing. The command format is **Command Args[1] Args[2]...**. **Command** is a system command or a user-defined executable program. If no path is specified, an executable program in the default path will be selected. If multiple commands need to be executed, write the commands into a script for execution. |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   | Example command:                                                                                                                                                                                                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   | .. code-block::                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   |    exec:                                                                                                                                                                                                                                                                                                                                                                         |
      |                                   |      command:                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   |      - /uninstall.sh                                                                                                                                                                                                                                                                                                                                                             |
      |                                   |      - uninstall_agent                                                                                                                                                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   | Enter **/uninstall uninstall_agent** in the script. This command indicates that the **uninstall.sh** script will be executed before the container completes its execution and stops running.                                                                                                                                                                                     |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | HTTP request                      | Send an HTTP request for pre-stop processing. The related parameters are described as follows:                                                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   | -  **Path**: (optional) request URL.                                                                                                                                                                                                                                                                                                                                             |
      |                                   | -  **Port**: (mandatory) request port.                                                                                                                                                                                                                                                                                                                                           |
      |                                   | -  **Host Address**: (optional) IP address of the request. The default value is the IP address of the node where the container resides.                                                                                                                                                                                                                                          |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Container Restart Policy
------------------------

The **restartPolicy** field is used to specify the pod restart policy. The restart policy type can be **Always**, **OnFailure**, or **Never**. The default value is **Always**.

When **restartPolicy** is used, containers are restarted only through kubelet on the same node.

+----------------+------------------------------------------------------------------------------------------------------------+
| Restart Policy | Description                                                                                                |
+================+============================================================================================================+
| Always         | When a container fails, kubelet automatically restarts the container.                                      |
+----------------+------------------------------------------------------------------------------------------------------------+
| OnFailure      | When the container stops running and the exit code is not 0, kubelet automatically restarts the container. |
+----------------+------------------------------------------------------------------------------------------------------------+
| Never          | kubelet does not restart the container regardless of the container running status.                         |
+----------------+------------------------------------------------------------------------------------------------------------+

.. note::

   Controllers that can manage pods include ReplicaSet Controllers, jobs, DaemonSets, and kubelet (static pod).

   -  ReplicaSet Controller and DaemonSet: The policy must be set to **Always** to ensure that containers run continuously.
   -  Job: The policy can be set to **OnFailure** or **Never** to ensure that containers are not restarted after being executed.
   -  kubelet will restart a pod whenever it fails, regardless of the value of **restartPolicy**. In addition, no health check is performed on the pod.

.. _cce_01_0105__section151181981167:

Example YAML for Setting the Container Lifecycle
------------------------------------------------

This section uses Nginx as an example to describe how to set the container lifecycle.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

#. Create and edit the **nginx-deployment.yaml** file. **nginx-deployment.yaml** is an example file name, and you can change it as required.

   **vi nginx-deployment.yaml**

   In the following configuration file, the **postStart** command is defined to run the **install.sh** command in the **/bin/bash** directory. **preStop** is defined to run the **uninstall.sh** command.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: nginx
        strategy:
          type: RollingUpdate
        template:
          metadata:
            labels:
              app: nginx
          spec:
            restartPolicy: Always               #Restart policy
            containers:
            - image: nginx
              command:
              - sleep 3600                        #Startup command
              imagePullPolicy: Always
              lifecycle:
                postStart:
                  exec:
                    command:
                    - /bin/bash
                    - install.sh                  #Post-start command
                preStop:
                  exec:
                    command:
                    - /bin/bash
                    - uninstall.sh                 #Pre-stop command
              name: nginx
            imagePullSecrets:
            - name: default-secret
