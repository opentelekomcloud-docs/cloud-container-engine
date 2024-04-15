:original_name: cce_10_0105.html

.. _cce_10_0105:

Configuring Container Lifecycle Parameters
==========================================

Scenario
--------

CCE provides callback functions for the lifecycle management of containerized applications. For example, if you want a container to perform a certain operation before stopping, you can register a hook function.

CCE provides the following lifecycle callback functions:

-  **Startup Command**: executed to start a container. For details, see :ref:`Startup Commands <cce_10_0105__section54912655316>`.
-  **Post-Start**: executed immediately after a container is started. For details, see :ref:`Post-Start Processing <cce_10_0105__section15243544163715>`.
-  **Pre-Stop**: executed before a container is stopped. The pre-stop processing function helps you ensure that the services running on the pods can be completed in advance in the case of pod upgrade or deletion. For details, see :ref:`Pre-Stop Processing <cce_10_0105__section2334114473712>`.

.. _cce_10_0105__section54912655316:

Startup Commands
----------------

By default, the default command during image start. To run a specific command or rewrite the default image value, you must perform specific settings:

A Docker image has metadata that stores image information. If lifecycle commands and arguments are not set, CCE runs the default commands and arguments, that is, Docker instructions **ENTRYPOINT** and **CMD**, provided during image creation.

If the commands and arguments used to run a container are set during application creation, the default commands **ENTRYPOINT** and **CMD** are overwritten during image build. The rules are as follows:

.. table:: **Table 1** Commands and arguments used to run a container

   +------------------+--------------+----------------------------+-------------------------------+--------------------+
   | Image ENTRYPOINT | Image CMD    | Command to Run a Container | Parameters to Run a Container | Command Executed   |
   +==================+==============+============================+===============================+====================+
   | [touch]          | [/root/test] | Not set                    | Not set                       | [touch /root/test] |
   +------------------+--------------+----------------------------+-------------------------------+--------------------+
   | [touch]          | [/root/test] | [mkdir]                    | Not set                       | [mkdir]            |
   +------------------+--------------+----------------------------+-------------------------------+--------------------+
   | [touch]          | [/root/test] | Not set                    | [/opt/test]                   | [touch /opt/test]  |
   +------------------+--------------+----------------------------+-------------------------------+--------------------+
   | [touch]          | [/root/test] | [mkdir]                    | [/opt/test]                   | [mkdir /opt/test]  |
   +------------------+--------------+----------------------------+-------------------------------+--------------------+

#. Log in to the CCE console. When creating a workload, configure container information and select **Lifecycle**.
#. Enter a command and arguments on the **Startup Command** tab page.

   .. table:: **Table 2** Container startup command

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------+
      | Configuration Item                | Procedure                                                                                                                                   |
      +===================================+=============================================================================================================================================+
      | Command                           | Enter an executable command, for example, **/run/server**.                                                                                  |
      |                                   |                                                                                                                                             |
      |                                   | If there are multiple executable commands, write them in different lines.                                                                   |
      |                                   |                                                                                                                                             |
      |                                   | .. note::                                                                                                                                   |
      |                                   |                                                                                                                                             |
      |                                   |    In the case of multiple commands, you are advised to run **/bin/sh** or other **shell** commands. Other commands are used as parameters. |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------+
      | Args                              | Enter the argument that controls the container running command, for example, **--port=8080**.                                               |
      |                                   |                                                                                                                                             |
      |                                   | If there are multiple arguments, separate them in different lines.                                                                          |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0105__section15243544163715:

Post-Start Processing
---------------------

#. Log in to the CCE console. When creating a workload, configure container information and select **Lifecycle**.
#. Set the post-start processing parameters on the **Post-Start** tab page.

   .. table:: **Table 3** Post-start processing parameters

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
      |                                   | -  **Host**: (optional) requested host IP address. The default value is the IP address of the pod.                                                                                                                                                                                                                                                                                                                                                                       |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0105__section2334114473712:

Pre-Stop Processing
-------------------

#. Log in to the CCE console. When creating a workload, configure container information and select **Lifecycle**.
#. Set the pre-start processing parameters on the **Pre-Stop** tab page.

   .. table:: **Table 4** Pre-stop processing parameters

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
      |                                   | -  **Host**: (optional) requested host IP address. The default value is the IP address of the pod.                                                                                                                                                                                                                                                                               |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Example YAML
------------

This section uses Nginx as an example to describe how to set the container lifecycle.

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
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - image: nginx
           command:
           - sleep 3600                        # Startup command
           imagePullPolicy: Always
           lifecycle:
             postStart:
               exec:
                 command:
                 - /bin/bash
                 - install.sh                  # Post-start command
             preStop:
               exec:
                 command:
                 - /bin/bash
                 - uninstall.sh                 # Pre-stop command
           name: nginx
         imagePullSecrets:
         - name: default-secret
