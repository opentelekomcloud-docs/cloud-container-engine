:original_name: cce_10_0411.html

.. _cce_10_0411:

Changing the Maximum Number of File Handles
===========================================

The maximum number of file handles is the maximum number of files that can be opened. In Linux, there are two file handle restrictions. One is a system-level restriction, where the maximum number of files that can be opened by all user processes at the same time. The other is a user-level restriction, where the maximum number of files that can be opened by a single user process. Containers have a third file handle restriction, which is the maximum number of file handles of a single process in the container.

.. important::

   The commands for modifying node system parameters are valid only when public images are used. The commands provided in this document are for reference only when private images are used.

.. _cce_10_0411__section156844286910:

Changing the Maximum Number of System-Level File Handles on a Node
------------------------------------------------------------------

#. Log in to the node and check the **/etc/sysctl.conf** file.

   .. code-block::

      cat /etc/sysctl.conf

#. Modify the **fs.file-max** parameter. **fs.file-max=1048576** indicates the kernel parameter name and recommended value.

   -  If the value of **fs.file-max** has been set in the **sysctl.conf** file, run the following command to change the value:

      .. code-block::

         sed -i "s/fs.file-max=[0-9]*$/fs.file-max=1048576/g" /etc/sysctl.conf && sysctl -p

   -  If **fs.file-max** is not set in the **sysctl.conf** file, run the following command to add it:

      .. code-block::

         echo fs.file-max=1048576 >> /etc/sysctl.conf && sysctl -p

#. Run the following command to check whether the change is successful (whether the returned value is the same as that you configured).

   .. code-block::

      sysctl fs.file-max

   Information similar to the following is displayed:

   .. code-block::

      fs.file-max = 1048576

.. _cce_10_0411__section57532291011:

Changing the Maximum Number of File Handles for a Single Process on a Node
--------------------------------------------------------------------------

#. Log in to the node and view the **/etc/security/limits.conf** file.

   .. code-block::

      cat /etc/security/limits.conf

   The maximum number of file handles for a single process of a node is specified by the following parameters:

   .. code-block::

      ...
      root soft nofile 65535
      root hard nofile 65535
      * soft nofile 65535
      * hard nofile 65535

#. Run the **sed** command to change the maximum number of file handles. In the command, **65535** is the recommended maximum number of file handles. The **/etc/security/limits.conf** file on the EulerOS 2.3 node does not contain the default configuration related to nofile. Therefore, you cannot run the **sed** command to modify the configuration.

   .. code-block::

      sed -i "s/nofile.[0-9]*$/nofile 65535/g" /etc/security/limits.conf

#. Log in to the node again and run the following command to check whether the modification is successful. If the returned value is the same as the modified value, the modification is successful.

   .. code-block::

      ulimit -n

   Information similar to the following is displayed:

   .. code-block::

      65535

.. _cce_10_0411__section1732602025518:

Changing the Maximum Number of File Handles for a Single Container Process
--------------------------------------------------------------------------

#. Log in to the node and check the maximum number of file handles for a single container process.

   -  CentOS/EulerOS:

      -  Docker nodes:

         .. code-block::

            cat /usr/lib/systemd/system/docker.service

      -  containerd nodes:

         .. code-block::

            cat /usr/lib/systemd/system/containerd.service

   -  Ubuntu:

      -  Docker nodes:

         .. code-block::

            cat /lib/systemd/system/docker.service

      -  containerd nodes:

         .. code-block::

            cat /lib/systemd/system/containerd.service

   Parameter settings:

   .. code-block::

      ...
      LimitNOFILE=1048576
      LimitNPROC=1048576
      ...

   -  **LimitNOFILE** specifies the maximum number of file handles a single process can open. If it is set to **infinity**, there is a maximum of 1,048,576 files handles per process in a container.
   -  **LimitNPROC** specifies the maximum number of processes a single user can run. If it is set to **infinity**, there is no limit on the number of processes.

#. Run the command below to modify the two parameters. In the command, **1048576** is the recommended value of the maximum number of file handles.

   .. important::

      Changing the maximum number of file handles of a container will restart the docker/containerd process.

   -  CentOS/EulerOS:

      -  Docker nodes:

         .. code-block::

            sed -i "s/LimitNOFILE=[0-9a-Z]*$/LimitNOFILE=1048576/g" /usr/lib/systemd/system/docker.service;sed -i "s/LimitNPROC=[0-9a-Z]*$/LimitNPROC=1048576/g" /usr/lib/systemd/system/docker.service && systemctl daemon-reload && systemctl restart docker

      -  containerd nodes:

         .. code-block::

            sed -i "s/LimitNOFILE=[0-9a-Z]*$/LimitNOFILE=1048576/g" /usr/lib/systemd/system/containerd.service;sed -i "s/LimitNPROC=[0-9a-Z]*$/LimitNPROC=1048576/g" /usr/lib/systemd/system/containerd.service && systemctl daemon-reload && systemctl restart containerd

   -  Ubuntu:

      -  Docker nodes:

         .. code-block::

            sed -i "s/LimitNOFILE=[0-9a-Z]*$/LimitNOFILE=1048576/g" /lib/systemd/system/docker.service;sed -i "s/LimitNPROC=[0-9a-Z]*$/LimitNPROC=1048576/g" /lib/systemd/system/docker.service && systemctl daemon-reload && systemctl restart docker

      -  containerd nodes:

         .. code-block::

            sed -i "s/LimitNOFILE=[0-9a-Z]*$/LimitNOFILE=1048576/g" /usr/lib/systemd/system/containerd.service;sed -i "s/LimitNPROC=[0-9a-Z]*$/LimitNPROC=1048576/g" /usr/lib/systemd/system/containerd.service && systemctl daemon-reload && systemctl restart containerd

#. Check the maximum number of file handles of a single process in the container. If the returned value is the same as the modified value, the modification is successful.

   -  Docker nodes:

      .. code-block::

         cat /proc/`pidof dockerd`/limits | grep files

      Information similar to the following is displayed:

      .. code-block::

         Max open files            1048576              1048576              files

   -  containerd nodes:

      .. code-block::

         cat /proc/`pidof containerd`/limits | grep files

      Information similar to the following is displayed:

      .. code-block::

         Max open files            1048576              1048576              files

Automatically Configuring the Maximum Number of File Handles When Creating a Node or Node Pool
----------------------------------------------------------------------------------------------

You can set the script to be executed after a node or node pool is created. When creating a node or node pool, you can use the script to configure the maximum number of file handles.

#. Confirm the OS of the node or node pool to be created.
#. Manually test the script commands on nodes in the same cluster and running the same OS.

   -  :ref:`Changing the Maximum Number of System-Level File Handles on a Node <cce_10_0411__section156844286910>`
   -  :ref:`Changing the Maximum Number of File Handles for a Single Process on a Node <cce_10_0411__section57532291011>`
   -  :ref:`Changing the Maximum Number of File Handles for a Single Container Process <cce_10_0411__section1732602025518>`

#. When creating a node or node pool, choose **Advanced Settings** > **Post-installation Command** to add commands. (The following commands must be configured after they are verified to be executable.)

   -  Change the maximum number of system-level file handles on a node.

      -  Log in to the node and check the **/etc/sysctl.conf** file. If the value of **fs.file-max** has been set in the file, run the following command to change it:

         .. code-block::

            sed -i "s/fs.file-max=[0-9]*$/fs.file-max=1048576/g" /etc/sysctl.conf && sysctl -p

      -  Log in to the node and check the **/etc/sysctl.conf** file. If the value of **fs.file-max** is not set in the file, run the following command to add it:

         .. code-block::

            echo fs.file-max=1048576 >> /etc/sysctl.conf && sysctl -p

      In this command, **fs.file-max=1048576** indicates the kernel parameter name and recommended value.

   -  Run the following command to change the maximum number of file handles for a single process on a node:

      .. code-block::

         sed -i "s/nofile.[0-9]*$/nofile 65535/g" /etc/security/limits.conf

      In the preceding command, **65535** is the recommended maximum number of file handles.

   -  Change the maximum number of file handles for a single process of a container.

      -  CentOS/EulerOS:

         .. code-block::

            sed -i "s/LimitNOFILE=[0-9a-Z]*$/LimitNOFILE=1048576/g" /usr/lib/systemd/system/docker.service;sed -i "s/LimitNPROC=[0-9a-Z]*$/LimitNPROC=1048576/g" /usr/lib/systemd/system/docker.service && systemctl daemon-reload && systemctl restart docker

      -  Ubuntu:

         .. code-block::

            sed -i "s/LimitNOFILE=[0-9a-Z]*$/LimitNOFILE=1048576/g" /lib/systemd/system/docker.service;sed -i "s/LimitNPROC=[0-9a-Z]*$/LimitNPROC=1048576/g" /lib/systemd/system/docker.service && systemctl daemon-reload && systemctl restart docker

      In the preceding command, **1048576** is the recommended maximum number of file handles.

#. After the node is created, log in to the node to check whether the parameter is successfully modified.
