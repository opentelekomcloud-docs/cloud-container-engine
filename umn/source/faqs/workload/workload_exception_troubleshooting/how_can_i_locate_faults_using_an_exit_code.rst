:original_name: cce_faq_00404.html

.. _cce_faq_00404:

How Can I Locate Faults Using an Exit Code?
===========================================

When a container fails to be started or terminated, the exit code is recorded by Kubernetes events to report the cause. This section describes how to locate faults using an exit code.

Viewing an Exit Code
--------------------

You can use kubectl to connect to the cluster and run the following command to check the pod:

.. code-block::

   kubectl describe pod {pod name}

In the command output, the **Exit Code** field indicates the status code of the last program exit. If the value is not **0**, the program exits abnormally. You can further analyze the cause through this code.

.. code-block::

   Containers:
     container-1:
       Container ID: ...
       Image: ...
       Image ID: ...
       Ports: ...
       Host Ports: ...
       Args: ...
       State: Running
         Started: Sat, 28 Jan 2023 09:06:53 +0000
       Last State: Terminated
         Reason: Error
         Exit Code: 255
         Started: Sat, 28 Jan 2023 09:01:33 +0000
         Finished: Sat, 28 Jan 2023 09:05:11 +0000
       Ready: True
       Restart Count: 1

Description
-----------

The exit code ranges from 0 to 255.

-  If the exit code is 0, the container exits normally.

-  Generally, if the abnormal exit is caused by the program, the exit code ranges from 1 to 128. In special scenarios, the exit code ranges from 129 to 255.

-  When a program exits due to external interrupts, the exit code ranges from 129 to 255. When the operating system sends :ref:`an interrupt signal <cce_faq_00404__section8671210185117>` to the program, the exist code is the interrupt signal value plus 128. For example, if the interrupt signal value of **SIGKILL** is 9, the exit status code is 137 (9 + 128).

-  If the exist code is not in the range of 0 to 255, for example, exit(-1), the exit code is automatically converted to a value that is within this range.

   If the exist code is a positive number, the conversion formula is as follows:

   .. code-block::

      code % 256

   If the exit code is a negative number, the conversion formula is as follows:

   .. code-block::

      256 - (|code| % 256)

For details, see `Exit Codes With Special Meanings <https://tldp.org/LDP/abs/html/exitcodes.html>`__.

Common Exit Codes
-----------------

.. table:: **Table 1** Common exit codes

   +-----------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Exit Code             | Name                                   | Description                                                                                                                                                                                                                       |
   +=======================+========================================+===================================================================================================================================================================================================================================+
   | 0                     | Normal exit                            | The container exits normally. This status code does not always indicate that an exception occurs. When there is no process in the container, it may also be displayed.                                                            |
   +-----------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 1                     | Common program error                   | There are many causes for this exception, most of which are caused by the program. You need to further locate the cause through container logs. For example, this error occurs when an x86 image is running on an Arm node.       |
   +-----------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 125                   | The container is not running.          | The possible causes are as follows:                                                                                                                                                                                               |
   |                       |                                        |                                                                                                                                                                                                                                   |
   |                       |                                        | -  An undefined flag is used in the command, for example, **docker run --abcd**.                                                                                                                                                  |
   |                       |                                        | -  The user-defined command in the image has insufficient permissions on the local host.                                                                                                                                          |
   |                       |                                        | -  The container engine is incompatible with the host OS or hardware.                                                                                                                                                             |
   +-----------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 126                   | Command calling error                  | The command called in the image cannot be executed. For example, the file permission is insufficient or the file cannot be executed.                                                                                              |
   +-----------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 127                   | The file or directory cannot be found. | The file or directory specified in the image cannot be found.                                                                                                                                                                     |
   +-----------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 128                   | Invalid exit parameter                 | The container exits but no valid exit code is provided. There are multiple possible causes. You need to further locate the cause. For example, an application running on the containerd node attempts to call the docker command. |
   +-----------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 137                   | Immediate termination (SIGKILL)        | The program is terminated by the **SIGKILL** signal. The common causes are as follows:                                                                                                                                            |
   |                       |                                        |                                                                                                                                                                                                                                   |
   |                       |                                        | -  The memory usage of the container in the pod reaches the resource limit. For example, OOM causes cgroup to forcibly stop the container.                                                                                        |
   |                       |                                        | -  If OOM occurs, the kernel of the node stops some processes to release the memory. As a result, the container may be terminated.                                                                                                |
   |                       |                                        | -  If the container health check fails, kubelet stops the container.                                                                                                                                                              |
   |                       |                                        | -  Other external processes, such as malicious scripts, forcibly stop the container.                                                                                                                                              |
   +-----------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 139                   | Segmentation error (SIGSEGV)           | The container receives the **SIGSEGV** signal from the OS because the container attempts to access an unauthorized memory location.                                                                                               |
   +-----------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 143                   | Graceful termination (SIGTERM)         | The container is correctly closed as instructed by the host. Generally, this exit code 143 does not require troubleshooting.                                                                                                      |
   +-----------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 255                   | The exit code is out of range.         | The container exit code is out of range. For example, exit(-1) may be used for abnormal exit, and -1 is automatically converted to 255.                                                                                           |
   |                       |                                        |                                                                                                                                                                                                                                   |
   |                       |                                        | Further troubleshooting is required.                                                                                                                                                                                              |
   +-----------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_faq_00404__section8671210185117:

Linux Standard Interrupt Signals
--------------------------------

You can run the **kill -l** command to view the signals and corresponding values in the Linux OS.

.. table:: **Table 2** Common Linux standard interrupt signals

   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | Signal  | Value | Action | Commit                                                                                                                              |
   +=========+=======+========+=====================================================================================================================================+
   | SIGHUP  | 1     | Term   | Sent when the user terminal connection (normal or abnormal) ends.                                                                   |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGINT  | 2     | Term   | Program termination signal, which is sent by the terminal by pressing **Ctrl+C**.                                                   |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGQUIT | 3     | Core   | Similar to **SIGINT**, the exit command is sent by the terminal. Generally, the exit command is controlled by pressing **Ctrl+\\**. |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGILL  | 4     | Core   | Invalid instruction, usually because an error occurs in the executable file.                                                        |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGABRT | 6     | Core   | Signal generated when the abort function is invoked. The process ends abnormally.                                                   |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGFPE  | 8     | Core   | A floating-point arithmetic error occurs. Other arithmetic errors such as divisor 0 also occur.                                     |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGKILL | 9     | Term   | Any process is terminated.                                                                                                          |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGSEGV | 11    | Core   | Attempt to access an unauthorized memory location.                                                                                  |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGPIPE | 13    | Term   | The pipe is disconnected.                                                                                                           |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGALRM | 14    | Term   | Indicates clock timing.                                                                                                             |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGTERM | 15    | Term   | Process end signal, which is usually the normal exit of the program.                                                                |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGUSR1 | 10    | Term   | This is a user-defined signal in applications.                                                                                      |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGUSR2 | 12    | Term   | This is a user-defined signal in applications.                                                                                      |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGCHLD | 17    | Ign    | This signal is generated when a subprocess ends or is interrupted.                                                                  |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGCONT | 18    | Cont   | Resume a stopped process.                                                                                                           |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGSTOP | 19    | Stop   | Suspend the execution of a process.                                                                                                 |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGTSTP | 20    | Stop   | Stop a process.                                                                                                                     |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGTTIN | 21    | Stop   | The background process reads the input value from the terminal.                                                                     |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
   | SIGTTOU | 22    | Stop   | The background process reads the output value from the terminal.                                                                    |
   +---------+-------+--------+-------------------------------------------------------------------------------------------------------------------------------------+
