:original_name: cce_10_0416.html

.. _cce_10_0416:

Cloud Native Log Collection
===========================

Introduction
------------

The Cloud Native Log Collection add-on (formerly log-agent) is developed based on Fluent Bit and OpenTelemetry for collecting logs and Kubernetes events. This add-on supports CRD-based log collection policies. It collects and forwards standard output logs, container file logs, node logs, and Kubernetes event logs in a cluster based on configured policies. It also reports Kubernetes events to AOM for configuring event alarms. By default, all abnormal events and some normal events are reported.

.. note::

   In 1.3.2 and later versions, Cloud Native Log Collection reports all warning events and some normal events to AOM by default. The reported events can be used to configure alarms. If the cluster version is 1.19.16, 1.21.11, 1.23.9, 1.25.4, or later, after Cloud Native Log Collection is installed, events are reported to AOM by this add-on instead of the control plane component. After Cloud Native Log Collection is uninstalled, events will not be reported to AOM.

Log Collection Reliability
--------------------------

The log system's main purpose is to record all stages of data for service components, including startup, initialization, exit, runtime details, and exceptions. It is primarily employed in O&M scenarios for tasks like checking component status and analyzing fault causes.

Standard streams (stdout and stderr) and local log files use non-persistent storage. However, data integrity may be compromised due to the following risks:

-  Log rotation and compression potentially deleting old files
-  Temporary storage volumes being cleared when Kubernetes pods end
-  Automatic OS cleanup triggered by limited node storage space

While the Cloud Native Log Collection add-on employs techniques like multi-level buffering, priority queues, and resumable uploads to enhance log collection reliability, logs could still be lost in the following situations:

-  The service log throughput surpasses the collector's processing capacity.
-  The service pod is abruptly terminated and reclaimed by CCE.
-  The log collector pod experiences exceptions.

The following lists some recommended best practices for cloud native log management. You can review and implement them thoughtfully.

-  Use dedicated, high-reliable streams to record critical service data (for example, financial transactions) and store the data in persistent storage.
-  Avoid storing sensitive information like customer details, payment credentials, and session tokens in logs.

Notes and Constraints
---------------------

This add-on is available only in clusters v1.17 or later.

Add-on Performance
------------------

+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
| Item                              | Description                                                                                                                                                                                           | Remarks                                                               |
+===================================+=======================================================================================================================================================================================================+=======================================================================+
| Size of a log                     | Each individual log must not exceed 512 KB in size. In the case of multi-line logs, the length of each line will be calculated separately.                                                            | None                                                                  |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
| Maximum number of collected files | On a single node, the total number of files that can be listened by all log collection rules is limited to 4,095.                                                                                     | None                                                                  |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
| Log collection rate               | -  If the add-on version is earlier than 1.5.0, in each cluster, no more than 10,000 single-line logs can be collected per second, and no more than 2000 multi-line logs can be collected per second. | Service quality cannot be ensured if any of these limits is exceeded. |
|                                   | -  If the add-on version is 1.5.0 or later, on each node, no more than 20,000 logs or 10 MB of logs can be collected per second.                                                                      |                                                                       |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
| Configuration update              | Configuration updates take effect in 1 to 3 minutes.                                                                                                                                                  | None                                                                  |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------+

Permissions
-----------

The fluent-bit component of the add-on reads and collects the stdout logs on each node, file logs in pods, and node logs based on the collection configuration.

The following permissions are required for running the fluent-bit component:

-  CAP_DAC_OVERRIDE: ignores the discretionary access control (DAC) restrictions on files.
-  CAP_FOWNER: ignores the restrictions that the file owner ID must match the process user ID.
-  DAC_READ_SEARCH: ignores the DAC restrictions on file reading and catalog research.
-  SYS_PTRACE: allows all processes to be traced.

.. caution::

   The Cloud Native Log Collection add-on enhances the log reading capability based on these permissions. However, if log files are stored in an external file system (such as SFS), these permissions may not take effect. To ensure that logs can be collected properly, ensure that the paas user (UID 10000) has the read and execute permissions on the directory where the logs are stored and the read permissions on the files in the directory.

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Add-ons**. Locate **Cloud Native Log Collection** and click **Install**.
#. On the **Install Add-on** page, configure the specifications as needed.

   -  If you selected **Preset**, you can choose between **Small** or **Large** depending on the number of logs on nodes. The system will automatically set the number of add-on pods and resource quotas according to the preset specifications. You can see the configurations on the console.

      You can select the **Small** option for clusters where the logs of a single node are less than 5000/s or 5 MB/s, and select the **Large** option for clusters where the logs on a single node are less than 10000/s or 10 MB/s.

   -  If you selected **Custom**, you can adjust the number of pods and resource quotas as needed. High availability is not possible with a single pod. If an error occurs on the node where the add-on instance runs, the add-on will fail.

#. Configure add-on parameters, which is supported by the add-on of v1.6.1 or later. For details, see `Tail <https://docs.fluentbit.io/manual/pipeline/inputs/tail>`__.

   +-----------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------+---------------------+
   | Parameter                               | Description                                                                                                                                                                                                                            | Default Value       |                     |
   +=========================================+========================================================================================================================================================================================================================================+=====================+=====================+
   |                                         |                                                                                                                                                                                                                                        | Large Specification | Other Specification |
   +-----------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------+---------------------+
   | Initial Buffer Size (Buffer_Chunk_Size) | The size of the initial buffer used to read files.                                                                                                                                                                                     | 256k                | 128k                |
   +-----------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------+---------------------+
   | Maximum Buffer Size (Buffer_Max_Size)   | Limit on the buffer size for each monitored file. When a buffer is added, the number of memory buffers that can be added is restricted by this value. If the limit is exceeded, the file will be removed from the monitored file list. | 1024k               | 512k                |
   +-----------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------+---------------------+
   | Memory Buffer Limit (Mem_Buf_Limit)     | Memory limit when data is appended to the engine. When the limit is reached, the add-on will temporarily stop reading log file data. The reading will resume after the data is refreshed.                                              | 300mb               | 40mb                |
   +-----------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------+---------------------+

   .. note::

      The unit can be case-insensitive k, kb, m, mb, g or gb. If no unit is specified, the default unit of byte will be used.

#. Configure deployment policies for the add-on pods.

   .. note::

      Scheduling policies do not take effect on add-on pods of the DaemonSet type.

   .. table:: **Table 1** Configurations for add-on scheduling

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                     |
      +===================================+=================================================================================================================================================================================================================================================================================================+
      | Multi-AZ Deployment               | -  **Preferred**: Deployment pods of the add-on will be preferentially scheduled to nodes in different AZs. If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to different nodes in that AZ.                                                              |
      |                                   | -  **Forcible**: Deployment pods of the add-on are forcibly scheduled to nodes in different AZs. There can be at most one pod in each AZ. If nodes in a cluster are not in different AZs, some add-on pods cannot run properly. If a node is faulty, add-on pods on it may fail to be migrated. |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Install**.

Components
----------

.. table:: **Table 2** Add-on components

   +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | Component             | Description                                                                                                                                                                                                                                                             | Resource Type         |
   +=======================+=========================================================================================================================================================================================================================================================================+=======================+
   | fluent-bit            | Lightweight log collector and forwarder deployed on each node to collect logs In 1.5.0 and later versions, logs are directly reported to LTS. In versions earlier than 1.5.0, logs are reported to otel-collector.                                                      | DaemonSet             |
   +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | cop-logs              | Used to generate soft links for collected files and run in the same pod as fluent-bit                                                                                                                                                                                   | DaemonSet             |
   +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | log-operator          | Used to generate internal configuration files                                                                                                                                                                                                                           | Deployment            |
   +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | otel-collector        | Used to collect Kubernetes events and report them to LTS and AOM, and receive and report logs to LTS.                                                                                                                                                                   | Deployment            |
   |                       |                                                                                                                                                                                                                                                                         |                       |
   |                       | The extent of log data reporting varies with the add-on version. In versions earlier than 1.5.1, this component reports logs of different applications and services. In 1.5.1 and later versions, this component reports only logs of workloads that are scaled to CCI. |                       |
   +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | otel-collector-event  | Added in v1.7.5 and later versions. It collects Kubernetes events and reports them to LTS and AOM.                                                                                                                                                                      | Deployment            |
   +-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

Add-on Usage
------------

This add-on can collect container standard output logs, container file logs, node logs, and Kubernetes events. You can use LTS or AOM to store the collected logs. These services support different types of logs.

.. table:: **Table 3** Log storage description

   +-----------------------+-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Log Storage Location  | Supported Log Types               | Instructions                                                                                                                                                                                                                                      |
   +=======================+===================================+===================================================================================================================================================================================================================================================+
   | LTS                   | -  Container standard output logs | Go to **Logging** to create a policy. For details, see :ref:`Collecting Container Logs Using the Cloud Native Log Collection Add-on <cce_10_0555>`.                                                                                               |
   |                       | -  Container file logs            |                                                                                                                                                                                                                                                   |
   |                       | -  Node logs                      |                                                                                                                                                                                                                                                   |
   +-----------------------+-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | LTS                   | Kubernetes events                 | :ref:`Collecting Kubernetes Events <cce_10_0793>`                                                                                                                                                                                                 |
   +-----------------------+-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | LTS                   | Control plane component logs      | :ref:`Collecting Control Plane Component Logs <cce_10_0554>`                                                                                                                                                                                      |
   +-----------------------+-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | LTS                   | Kubernetes audit logs             | :ref:`Collecting Audit Logs <cce_10_0792>`                                                                                                                                                                                                        |
   +-----------------------+-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | AOM                   | Kubernetes events                 | If the cluster version is v1.19.16, v1.21.11, v1.23.9, v1.25.4, or later, all abnormal events and some normal events will be reported by default. For details, see :ref:`Reporting Kubernetes Events to AOM <cce_10_0793__section1626024011253>`. |
   +-----------------------+-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Release History
---------------

.. table:: **Table 4** Cloud Native Log Collection add-on

   +-----------------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | Add-on Version        | Supported Cluster Version | New Feature                                                                                                             |
   +=======================+===========================+=========================================================================================================================+
   | 1.7.3                 | v1.21                     | CCE clusters v1.32 are supported.                                                                                       |
   |                       |                           |                                                                                                                         |
   |                       | v1.23                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.25                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.27                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.28                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.29                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.30                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.31                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.32                     |                                                                                                                         |
   +-----------------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | 1.7.2                 | v1.21                     | Logs can be compressed in gzip format and sent to LTS.                                                                  |
   |                       |                           |                                                                                                                         |
   |                       | v1.23                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.25                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.27                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.28                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.29                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.30                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.31                     |                                                                                                                         |
   +-----------------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | 1.6.0                 | v1.21                     | -  Clusters v1.30 are supported.                                                                                        |
   |                       |                           | -  Security hardening: The permissions of the add-on for accessing secrets are limited to the **monitoring** namespace. |
   |                       | v1.23                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.25                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.27                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.28                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.29                     |                                                                                                                         |
   |                       |                           |                                                                                                                         |
   |                       | v1.30                     |                                                                                                                         |
   +-----------------------+---------------------------+-------------------------------------------------------------------------------------------------------------------------+
