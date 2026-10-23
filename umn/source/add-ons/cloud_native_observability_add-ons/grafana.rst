:original_name: cce_10_0828.html

.. _cce_10_0828:

Grafana
=======

Introduction
------------

Grafana is an open-source visualized data monitoring platform. It provides you with various charts and panels for real-time monitoring, analysis, and visualization of various metrics and data sources.

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. Locate **Grafana** on the right and click **Install**.

#. Configure **Specifications** of the add-on pods. You can adjust their CPU and memory quotas as needed.

#. Configure the add-on parameters.

   .. table:: **Table 1** Add-on parameters

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                      |
      +===================================+==================================================================================================================================================================================================================================================================================================================================================================+
      | Open-Source Community Version     | Select the open-source community version to be installed. This parameter is supported by Grafana 1.3.1 and later. After the add-on is installed, the open-source community version cannot be changed. To change this version, uninstall the add-on and then reinstall it.                                                                                        |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PVC Type                          | Before installing Grafana, create a storage volume to store local data. When Grafana is uninstalled, this storage volume will not be deleted.                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   | -  If you selected **EVS**, you need to select an EVS disk type. The EVS disk types supported by different regions may vary. You can see the available disk types on the console.                                                                                                                                                                                |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   |    EVS disks are charged by storage capacity and occupy your EVS disk quotas.                                                                                                                                                                                                                                                                                    |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Capacity (GiB)                    | Expand the disk capacity after the disk is created. The EVS disk size is 5 GiB by default. For details, see :ref:`Related Operations <cce_10_0614__section16505832153318>`.                                                                                                                                                                                      |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Interconnect with AOM             | Enable this function to report Prometheus data to AOM. After this function is enabled, you can select the corresponding AOM instance. The collected basic metrics are free of charge. Custom metrics are charged by AOM. To interconnect with AOM, you must have certain permissions. Only **users in the** **admin** **user group** can perform this operation. |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Public Access                     | This option is available in Grafana 1.2.1 or later versions. After it is enabled, you need to select a load balancer as the Grafana service entry. Only load balancers in the VPC to which the cluster belongs can be selected. If a dedicated load balancer is used, you must configure network specifications for it.                                          |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   | .. important::                                                                                                                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                                                       |
      |                                   |    If you allow public access, Grafana services will be exposed to the Internet. It is recommended that you assess the security risks and establish access control policies.                                                                                                                                                                                     |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Configure deployment policies for the add-on pods.

   .. table:: **Table 2** Configurations for add-on scheduling

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                            |
      +===================================+========================================================================================================================================================================================================================================================================+
      | Node Affinity                     | -  **Not configured**: Node affinity is disabled for the add-on pods.                                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                        |
      |                                   | -  **Specify node**: Specify the nodes where the add-on pods are deployed. If you do not specify the nodes, the add-on pods will be randomly scheduled based on the default cluster scheduling policy.                                                                 |
      |                                   |                                                                                                                                                                                                                                                                        |
      |                                   | -  **Specify node pool**: Specify the node pool where the add-on pods are deployed. If you do not specify the node pools, the add-on pods will be randomly scheduled based on the default cluster scheduling policy.                                                   |
      |                                   |                                                                                                                                                                                                                                                                        |
      |                                   | -  **Customize affinity**: Enter the labels of the nodes where the add-on pods are to be deployed for more flexible scheduling policies. If you do not specify node labels, the add-on pods will be randomly scheduled based on the default cluster scheduling policy. |
      |                                   |                                                                                                                                                                                                                                                                        |
      |                                   |    If multiple custom affinity policies are configured, ensure that there are nodes that meet all the affinity policies in the cluster. Otherwise, the add-on pods cannot run.                                                                                         |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Toleration                        | Using both taints and tolerations enables (but does not require) the add-on's Deployment pods to be scheduled on nodes with matching taints, and allows control over pod eviction policies when host nodes are tainted.                                                |
      |                                   |                                                                                                                                                                                                                                                                        |
      |                                   | The add-on applies default toleration policies for the **node.kubernetes.io/not-ready** and **node.kubernetes.io/unreachable** taints on pods. The tolerance time window is 60s.                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                        |
      |                                   | For details, see :ref:`Configuring Tolerance Policies <cce_10_0728>`.                                                                                                                                                                                                  |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Install**.

   After the add-on is installed, select the cluster and choose **Add-ons** in the navigation pane. On the displayed page, select **Installed add-ons** to view the add-on.

Components
----------

.. table:: **Table 3** Add-on components

   ========= =================================== =============
   Component Description                         Resource Type
   ========= =================================== =============
   grafana   Provide Grafana data visualization. Deployment
   ========= =================================== =============

How to Use the Add-on
---------------------

To access Grafana through the Internet, enable **Public Access** and access a load balancer to Grafana.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Add-ons**. Locate **Grafana** on the right and click **Edit**.
#. Enable **Public Access** and select a load balancer. Only load balancers in the same VPC as the cluster are supported. The selected load balancer must have an EIP bound.

   .. caution::

      If you enabled **Public Access**, Grafana services will be exposed to the Internet. It is advised to assess the security risks and establish access control policies.

#. Click **OK**. After the update, the **Access** button is displayed. Click it to access Grafana.
#. Log in to Grafana to use its functions. You can use the default username and password to log in to Grafana for the first time. For details, see `Sign in to Grafana <https://grafana.com/docs/grafana/latest/setup-grafana/sign-in-to-grafana/>`__. After the login is successful, the system prompts you to change the password.
#. After the login, select a dashboard on Grafana to obtain the needed aggregation content.

Release History
---------------

.. table:: **Table 4** Grafana updates

   +-----------------------+---------------------------+----------------------------------------------------------+
   | Add-on Version        | Supported Cluster Version | New Feature                                              |
   +=======================+===========================+==========================================================+
   | 1.3.5                 | v1.17                     | -  Supported clusters v1.35.                             |
   |                       |                           |                                                          |
   |                       | v1.19                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.21                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.23                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.25                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.27                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.28                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.29                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.30                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.31                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.32                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.33                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.34                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.35                     |                                                          |
   +-----------------------+---------------------------+----------------------------------------------------------+
   | 1.3.4                 | v1.17                     | -  Supported clusters v1.34.                             |
   |                       |                           |                                                          |
   |                       | v1.19                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.21                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.23                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.25                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.27                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.28                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.29                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.30                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.31                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.32                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.33                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.34                     |                                                          |
   +-----------------------+---------------------------+----------------------------------------------------------+
   | 1.3.3                 | v1.17                     | -  Supported clusters v1.33.                             |
   |                       |                           | -  Upgraded the Grafana version to v10.4.18+security-01. |
   |                       | v1.19                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.21                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.23                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.25                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.27                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.28                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.29                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.30                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.31                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.32                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.33                     |                                                          |
   +-----------------------+---------------------------+----------------------------------------------------------+
   | 1.3.2                 | v1.17                     | Supported clusters v1.32.                                |
   |                       |                           |                                                          |
   |                       | v1.19                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.21                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.23                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.25                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.27                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.28                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.29                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.30                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.31                     |                                                          |
   |                       |                           |                                                          |
   |                       | v1.32                     |                                                          |
   +-----------------------+---------------------------+----------------------------------------------------------+
