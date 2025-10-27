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
      | Open-Source Community Version     | Select the open-source community version to be installed. This parameter is supported by Grafana v1.3.1 and later versions. After the add-on is installed, the open-source community version cannot be changed. To change this version, uninstall the add-on and then reinstall it.                                                                              |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PVC Type                          | To install Grafana, create a storage volume to store local data. When Grafana is uninstalled, this storage volume will not be deleted.                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   | -  If you selected **EVS**, you need to select an EVS disk type. The EVS disk types supported by different regions may vary. You can select one disk type on the management console.                                                                                                                                                                             |
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
      |                                   |    If you allow public access, Grafana services will be exposed to the public network. It is recommended that you assess the security risks and establish access control policies.                                                                                                                                                                               |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Configure deployment policies for the add-on pods.

   .. table:: **Table 2** Configurations for add-on scheduling

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                 |
      +===================================+=============================================================================================================================================================================================================================================================+
      | Node Affinity                     | -  **Not configured**: Node affinity is disabled for the add-on.                                                                                                                                                                                            |
      |                                   |                                                                                                                                                                                                                                                             |
      |                                   | -  **Specify node**: Specify the nodes where the add-on is deployed. If you do not specify the nodes, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                 |
      |                                   |                                                                                                                                                                                                                                                             |
      |                                   | -  **Specify node pool**: Specify the node pool where the add-on is deployed. If you do not specify the node pools, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                   |
      |                                   |                                                                                                                                                                                                                                                             |
      |                                   | -  **Customize affinity**: Enter the labels of the nodes where the add-on is to be deployed for more flexible scheduling policies. If you do not specify node labels, the add-on will be randomly scheduled based on the default cluster scheduling policy. |
      |                                   |                                                                                                                                                                                                                                                             |
      |                                   |    If multiple custom affinity policies are configured, ensure that there are nodes that meet all the affinity policies in the cluster. Otherwise, the add-on cannot run.                                                                                   |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Toleration                        | Using both taints and tolerations allows (not forcibly) the add-on Deployment to be scheduled to a node with the matching taints, and controls the Deployment eviction policies after the node where the Deployment is located is tainted.                  |
      |                                   |                                                                                                                                                                                                                                                             |
      |                                   | The add-on adds the default tolerance policy for the **node.kubernetes.io/not-ready** and **node.kubernetes.io/unreachable** taints, respectively. The tolerance time window is 60s.                                                                        |
      |                                   |                                                                                                                                                                                                                                                             |
      |                                   | For details, see :ref:`Configuring Tolerance Policies <cce_10_0728>`.                                                                                                                                                                                       |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Install**.

   After the add-on is installed, select the cluster and choose **Add-ons** in the navigation pane. On the displayed page, view the add-on in the **Add-ons Installed** area.

Components
----------

.. table:: **Table 3** Add-on components

   +-----------+------------------------------------------------------------------------+---------------+
   | Component | Description                                                            | Resource Type |
   +===========+========================================================================+===============+
   | grafana   | This component provides the data visualization capability for Grafana. | Deployment    |
   +-----------+------------------------------------------------------------------------+---------------+

How to Use
----------

To access Grafana charts through a public network, you need to bind a LoadBalancer service to the Grafana container.

#. Log in to the CCE console and click the name of the cluster with the Grafana add-on installed to access the cluster console. In the navigation pane, choose **Services & Ingresses**.

#. Click **Create from YAML** in the upper right corner to create a public network LoadBalancer Service for Grafana.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: grafana-lb     # Service name, which is customizable
        namespace: monitoring
        labels:
          app: grafana
        annotations:
          kubernetes.io/elb.id: 038ff***     # Replace it with the ID of the public network load balancer in the VPC that the cluster belongs to.
      spec:
        ports:
          - name: cce-service-0
            protocol: TCP
            port: 80     #Service port, which can be customized.
            targetPort: 3000     # Default Grafana port. Retain the default value.
        selector:
          app: grafana
        type: LoadBalancer

#. After the creation, visit **load balancer public IP:Service port** to access Grafana and select a proper dashboard to view the aggregated data.

Release History
---------------

.. table:: **Table 4** Grafana add-on

   +-----------------------+---------------------------+-------------------------------+
   | Add-on Version        | Supported Cluster Version | New Feature                   |
   +=======================+===========================+===============================+
   | 1.3.2                 | v1.17                     | Clusters v1.32 are supported. |
   |                       |                           |                               |
   |                       | v1.19                     |                               |
   |                       |                           |                               |
   |                       | v1.21                     |                               |
   |                       |                           |                               |
   |                       | v1.23                     |                               |
   |                       |                           |                               |
   |                       | v1.25                     |                               |
   |                       |                           |                               |
   |                       | v1.27                     |                               |
   |                       |                           |                               |
   |                       | v1.28                     |                               |
   |                       |                           |                               |
   |                       | v1.29                     |                               |
   |                       |                           |                               |
   |                       | v1.30                     |                               |
   |                       |                           |                               |
   |                       | v1.31                     |                               |
   |                       |                           |                               |
   |                       | v1.32                     |                               |
   +-----------------------+---------------------------+-------------------------------+
