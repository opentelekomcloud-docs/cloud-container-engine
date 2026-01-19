:original_name: cce_10_0401.html

.. _cce_10_0401:

Changing Process ID Limits (kernel.pid_max)
===========================================

Context
-------

Process IDs (PIDs) are a fundamental resource on nodes. It is trivial to hit the task limit without hitting any other resource limits, which can then cause instability to a host machine.

You can adjust the PID limit (**kernel.pid_max**) according to service requirements.

kernel.pid_max Defaults
-----------------------

.. table:: **Table 1** kernel.pid_max defaults

   +--------------+------------------------------------+-------------------------------------------------+------------------------------------------+
   | **OS**       | **Clusters of 1.17.9 and Earlier** | **Clusters of 1.17.17 and Later**               |                                          |
   +--------------+------------------------------------+-------------------------------------------------+------------------------------------------+
   |              |                                    | **Nodes Created on or Before January 30, 2022** | **Nodes Created After January 30, 2022** |
   +--------------+------------------------------------+-------------------------------------------------+------------------------------------------+
   | EulerOS 2.9  | N/A                                | 4194304                                         | 4194304                                  |
   +--------------+------------------------------------+-------------------------------------------------+------------------------------------------+
   | HCE OS 2.0   | N/A                                | N/A                                             | 4194304                                  |
   +--------------+------------------------------------+-------------------------------------------------+------------------------------------------+
   | Ubuntu 22.04 | N/A                                | N/A                                             | 4194304                                  |
   +--------------+------------------------------------+-------------------------------------------------+------------------------------------------+

Viewing kernel.pid_max
----------------------

Log in to the node and run the following command to obtain the value of **kernel.pid_max**:

.. code-block::

   sysctl kernel.pid_max

Command output:

.. code-block::

   kernel.pid_max = 32768

Change **kernel.pid_max**, if necessary, as instructed in :ref:`Changing kernel.pid_max of a Node <cce_10_0401__section103357276311>`.

Checking Node PIDs
------------------

Log in to the node and run the following command to check how many PIDs are in use:

.. code-block::

   ps -eflL | wc -l

Command output:

.. code-block::

   691

.. _cce_10_0401__section103357276311:

Changing kernel.pid_max of a Node
---------------------------------

Log in to the node and run the following command. **4194304** indicates the value of **kernel.pid_max** and is used as an example here.

.. code-block::

   echo kernel.pid_max = 4194304 >> /etc/sysctl.conf && sysctl -p

Run the following command to check whether the returned value is the same as that you configured:

.. code-block::

   sysctl kernel.pid_max

Command output:

.. code-block::

   kernel.pid_max = 4194304
