:original_name: cce_01_0008.html

.. _cce_01_0008:

Setting Container Startup Commands
==================================

Scenario
--------

When creating a workload or job, you can use an image to specify the processes running in the container.

By default, the image runs the default command. To run a specific command or rewrite the default image value, you must perform the following settings:

-  **Working directory**: working directory of the command.

   If the working directory is not specified in the image or on the console, the default value is **/**.

-  **Command**: command that controls the running of an image.

-  **Args**: parameters transferred to the running command.

.. important::

   After a container is started, do not modify configurations in the container. If configurations in the container are modified (for example, passwords, certificates, and environment variables of a containerized application are added to the container), the configurations will be lost after the container restarts and container services will become abnormal. An example scenario of container restart is pod rescheduling due to node anomalies.

   Configurations must be imported to a container as arguments. Otherwise, configurations will be lost after the container restarts.

Commands and Arguments Used to Run a Container
----------------------------------------------

A Docker image has metadata that stores image information. If lifecycle commands and arguments are not set, CCE runs the default commands and arguments, that is, Docker instructions **ENTRYPOINT** and **CMD**, provided during image creation.

If the commands and arguments used to run a container are set during application creation, the default commands **ENTRYPOINT** and **CMD** are overwritten during image build. The rules are as follows:

.. table:: **Table 1** Commands and parameters used to run a container

   +------------------+--------------+----------------------------+-------------------------+--------------------+
   | Image Entrypoint | Image CMD    | Command to Run a Container | Args to Run a Container | Command Executed   |
   +==================+==============+============================+=========================+====================+
   | [touch]          | [/root/test] | Not set                    | Not set                 | [touch /root/test] |
   +------------------+--------------+----------------------------+-------------------------+--------------------+
   | [touch]          | [/root/test] | [mkdir]                    | Not set                 | [mkdir]            |
   +------------------+--------------+----------------------------+-------------------------+--------------------+
   | [touch]          | [/root/test] | Not set                    | [/opt/test]             | [touch /opt/test]  |
   +------------------+--------------+----------------------------+-------------------------+--------------------+
   | [touch]          | [/root/test] | [mkdir]                    | [/opt/test]             | [mkdir /opt/test]  |
   +------------------+--------------+----------------------------+-------------------------+--------------------+

Setting the Startup Command
---------------------------

#. Log in to the CCE console. Expand **Lifecycle** when adding a container during workload or job creation.

#. Enter the running command and parameters, as shown in :ref:`Table 2 <cce_01_0008__table15533234825>`.

   .. note::

      -  The current startup command is provided as a string array and corresponds to the Entrypoint startup command of Docker. The format is as follows: ["executable", "param1", "param2",..]. For details about how to start Kubernetes containers, click `here <https://kubernetes.io/docs/tasks/inject-data-application/define-command-argument-container/#running-a-command-in-a-shell>`__.
      -  The lifecycle of a container is the same as that of the startup command. That is, the lifecycle of the container ends after the command is executed.

   .. _cce_01_0008__table15533234825:

   .. table:: **Table 2** Container startup command

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
      | Configuration Item                | Procedure                                                                                                                                 |
      +===================================+===========================================================================================================================================+
      | Command                           | Enter an executable command, for example, **/run/server**.                                                                                |
      |                                   |                                                                                                                                           |
      |                                   | If there are multiple commands, separate them with spaces. If the command contains a space, you need to add a quotation mark ("").        |
      |                                   |                                                                                                                                           |
      |                                   | .. note::                                                                                                                                 |
      |                                   |                                                                                                                                           |
      |                                   |    If there are multiple commands, you are advised to run the **/bin/sh** or other shell commands. Other commands are used as parameters. |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
      | Args                              | Enter the argument that controls the container running command, for example, **--port=8080**.                                             |
      |                                   |                                                                                                                                           |
      |                                   | If there are multiple arguments, separate them in different lines.                                                                        |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+

   The following uses Nginx as an example to describe three typical application scenarios of the container startup command:

   Example code:

   .. code-block::

      nginx -c nginx.conf

   -  Scenario 1: Both the **command** and **arguments** are set.


      .. figure:: /_static/images/en-us_image_0000001190302089.png
         :alt: **Figure 1** Setting the startup command and parameters

         **Figure 1** Setting the startup command and parameters

      Example YAML file:

      .. code-block::

                   command:
                     - nginx
                   args:
                     - '-c'
                     - nginx.conf

   -  Scenario 2: Only the **command** is set.


      .. figure:: /_static/images/en-us_image_0000001144342236.png
         :alt: **Figure 2** Setting the startup command

         **Figure 2** Setting the startup command

      .. note::

         A command must be enclosed in double quotes. If no double quotes are added, the command is split into multiple commands based on space character.

      Example YAML file:

      .. code-block::

                   command:
                     - nginx -c nginx.conf
                   args:

   -  Scenario 3: Only **arguments** are set.


      .. figure:: /_static/images/en-us_image_0000001190302091.png
         :alt: **Figure 3** Setting startup arguments

         **Figure 3** Setting startup arguments

      .. note::

         If the container startup command is not added to the system path, run the **/bin/sh** command to execute the container startup command. The container startup command must be enclosed in double quotes.

      Example YAML file:

      .. code-block::

                   command:
                     - /bin/sh
                   args:
                     - '-c'
                     - '"nginx -c nginx.conf"'

#. Check or modify the YAML file.

   -  When creating a workload, in the **Configure Advanced Settings** step, click YAML on the right.


      .. figure:: /_static/images/en-us_image_0000001144342238.png
         :alt: **Figure 4** Checking or editing a YAML file

         **Figure 4** Checking or editing a YAML file

   -  After the workload is created, go to the workload list. In the same row as the workload, choose **More** > **Edit YAML**.

   -  After the workload is created, go to the workload details page. On the displayed page, click **Edit YAML** in the upper right corner.

Example YAML for Setting Container Startup Commands
---------------------------------------------------

This section uses Nginx as an example to describe how to set container startup commands using kubectl.

Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`. See :ref:`Using kubectl to create a Deployment <cce_01_0047__section155246177178>` or :ref:`Using kubectl to create a StatefulSet <cce_01_0048__section113441881214>`. For more details on how to set container startup commands, see `official Kubernetes documentation <https://kubernetes.io/docs/concepts/workloads/pods/init-containers/>`__.

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
         containers:
         - image: nginx
           command:
           - sleep
           - '3600'                        #Startup command
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
