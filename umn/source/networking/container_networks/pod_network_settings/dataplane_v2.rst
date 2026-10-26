:original_name: cce_10_0945.html

.. _cce_10_0945:

DataPlane V2
============

DataPlane V2 can be enabled in clusters that use a VPC network or Cloud Native Network 2.0. This feature uses eBPF redirection to enable features like NetworkPolicy.

+-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| DataPlane V2                      | Description                                                                                                                                                                                                                                                                                                                  |
+===================================+==============================================================================================================================================================================================================================================================================================================================+
| Technical implementation          | DataPlane V2 integrates open-source `cilium <https://docs.cilium.io/en/stable/>`__ to enable features like NetworkPolicy.                                                                                                                                                                                                    |
+-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Supported cluster versions        | CCE standard clusters using VPC networks for commercial use of v1.27.16-r30, v1.28.15-r20, v1.29.13-r0, v1.30.10-r0, v1.31.6-r0, or later                                                                                                                                                                                    |
+-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Usage                             | -  When creating a CCE standard cluster, select the VPC network in the container network configuration and enable **DataPlane V2**.                                                                                                                                                                                          |
|                                   | -  When creating a CCE Turbo cluster, select Cloud Native Network 2.0 and enable **DataPlane V2**.                                                                                                                                                                                                                           |
|                                   |                                                                                                                                                                                                                                                                                                                              |
|                                   | .. important::                                                                                                                                                                                                                                                                                                               |
|                                   |                                                                                                                                                                                                                                                                                                                              |
|                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                   |
|                                   |                                                                                                                                                                                                                                                                                                                              |
|                                   |    -  After DataPlane V2 is enabled, secure containers are not supported.                                                                                                                                                                                                                                                    |
|                                   |    -  Enabled DataPlane V2 cannot be disabled.                                                                                                                                                                                                                                                                               |
|                                   |    -  DataPlane V2 can only be enabled for new clusters.                                                                                                                                                                                                                                                                     |
|                                   |    -  If Layer 7 or DNS-based network policies are enabled in your cluster, matching traffic will be disrupted during Cilium upgrades. For details, see `community constraints <https://docs.cilium.io/en/stable/operations/upgrade/#version-specific-notes>`__.                                                             |
+-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Supported OS                      | HCE OS 2.0 is supported.                                                                                                                                                                                                                                                                                                     |
|                                   |                                                                                                                                                                                                                                                                                                                              |
|                                   | Clusters v1.28.15-r70, v1.29.15-r30, v1.30.14-r30, v1.31.10-r30, v1.32.6-r30, v1.33.5-r20, v1.34.1-r0, and later versions support Ubuntu 22.04.                                                                                                                                                                              |
+-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Performance optimization          | -  eBPF is used to limit the bandwidth. This makes bandwidth limitation more accurate and resource consumption lower.                                                                                                                                                                                                        |
+-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Bandwidth                         | With DataPlane V2 enabled, CCE standard clusters on the VPC network model use eBPF for bandwidth limiting, and CCE Turbo clusters use TBF Qdisc. For details, see :ref:`Configuring QoS for Pods <cce_10_0382>`.                                                                                                             |
+-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| NetworkPolicy                     | -  The implementation of network policies is different from that of container tunnel networks. For details, see :ref:`Configuring Network Policies to Restrict Pod Access <cce_10_0059>`.                                                                                                                                    |
|                                   |                                                                                                                                                                                                                                                                                                                              |
|                                   |    -  The ipBlock selector can only select CIDR blocks outside a cluster.                                                                                                                                                                                                                                                    |
|                                   |    -  The ipBlock selector does not have good support for the **except** keyword, so this keyword is not recommended.                                                                                                                                                                                                        |
|                                   |    -  If a network policy of the egress type is used, the pod fails to access the IP addresses of the hostNetwork pod and node in the cluster.                                                                                                                                                                               |
|                                   |                                                                                                                                                                                                                                                                                                                              |
|                                   | -  `CiliumNetworkPolicy <https://docs.cilium.io/en/stable/network/kubernetes/policy/#ciliumnetworkpolicy>`__ and `CiliumClusterwideNetworkPolicy <https://docs.cilium.io/en/stable/network/kubernetes/policy/#ciliumclusterwidenetworkpolicy>`__ API support varies by cluster type.                                         |
|                                   |                                                                                                                                                                                                                                                                                                                              |
|                                   |    -  CCE standard clusters do not support `host policies <https://docs.cilium.io/en/stable/security/policy/language/#hostpolicies>`__.                                                                                                                                                                                      |
|                                   |    -  CCE Turbo clusters do not support `host policies <https://docs.cilium.io/en/stable/security/policy/language/#hostpolicies>`__, `DNS-based policies <https://docs.cilium.io/en/stable/security/dns/>`__, or `Layer 7 network policies <https://docs.cilium.io/en/stable/security/policy/language/#layer-7-examples>`__. |
+-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Resource consumption              | The resident cilium-agent process on each node is responsible for eBPF network acceleration. Each cilium-agent process may occupy 80 MiB of memory. Each time a pod is added, the cilium-agent memory consumption may increase by 10 KiB.                                                                                    |
+-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Components
----------

After DataPlane V2 is enabled, components listed in the following table are installed.

+-----------------------+------------------------------------------------------------------------+-----------------------+
| Component             | Description                                                            | Resource Type         |
+=======================+========================================================================+=======================+
| cilium-operator       | -  Synchronizes CRDs.                                                  | Deployment            |
|                       | -  Removes the **node.cilium.io/agent-not-ready** taint of a node.     |                       |
|                       | -  Tunes and recycles internal resources.                              |                       |
+-----------------------+------------------------------------------------------------------------+-----------------------+
| yangtse-cilium        | -  Installs the auxiliary CNI (cilium-cni) for CCE to adapt to Cilium. | DaemonSet             |
|                       | -  Deploys cilium-agent.                                               |                       |
+-----------------------+------------------------------------------------------------------------+-----------------------+

Configuration Management
------------------------

You can use ConfigMaps to create custom network components of DataPlane V2.

+-------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+-----------------+
| ConfigMap Configuration | Description                                                                                                                                                                                                              | Configurable Component                                                                 | Priority        |
+=========================+==========================================================================================================================================================================================================================+========================================================================================+=================+
| yangtse-cilium-config   | Default DataPlane V2 configuration. If this configuration is modified, the configuration will be restored to what it was during the cluster upgrade. **Do not modify this configuration.**                               | cilium-agent                                                                           | 1               |
+-------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+-----------------+
| cilium-config           | Native configuration of the Cilium community. The priority of this configuration is lower than that of yangtse-cilium-config. **If you need to create custom DataPlane V2 components, modify this configuration first.** | `cilium-agent <https://docs.cilium.io/en/stable/cmdref/cilium-agent/>`__               | 2               |
|                         |                                                                                                                                                                                                                          |                                                                                        |                 |
|                         |                                                                                                                                                                                                                          | `cilium-operator <https://docs.cilium.io/en/stable/cmdref/cilium-operator-generic/>`__ |                 |
+-------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+-----------------+

.. note::

   Currently, only the Cilium add-on of 2.1.1 or later supports custom configuration.

Example custom network components:

-  Example 1: To change the default NodePort range of a cluster, modify the **Service Port Range** in :ref:`Settings \> Network \> Service Settings <cce_10_0784__section18418310155812>`, and then update the Cilium settings by creating a native Cilium community ConfigMap:

   .. code-block::

      apiVersion: v1
      kind: ConfigMap
      metadata:
        name: cilium-config
        namespace: kube-system
      data:
        node-port-range: "30000,32767"

   After configuring cilium-config, run the following command to rebuild yangtse-cilium in rolling mode to apply the modification:

   .. code-block::

      uuid=$(uuidgen)
      kubectl patch daemonset -nkube-system yangtse-cilium --type='json' -p="[{\"op\": \"add\", \"path\": \"/spec/template/metadata/annotations/change-id\", \"value\": \"$uuid\"}]"

-  Example 2: When cilium-agent malfunctions, configure **--set-cilium-node-taints=true** for **cilium-operator** to automatically taint affected nodes, preventing pods from being scheduled to them.

   You can create the following native ConfigMap configuration of the Cilium community:

   .. code-block::

      apiVersion: v1
      kind: ConfigMap
      metadata:
        name: cilium-config
        namespace: kube-system
      data:
        set-cilium-node-taints: "true"

   After configuring cilium-config, you can run the following command to roll back and rebuild cilium-operator to apply the configuration:

   .. code-block::

      uuid=$(uuidgen)
      kubectl patch deployment -n kube-system cilium-operator --type='json' -p="[{\"op\": \"add\", \"path\": \"/spec/template/metadata/annotations/change-id\", \"value\": \"$uuid\"}]"

-  Example 3: To enable Hubble for cilium-agent, create the following native Cilium community ConfigMap. For details about the parameter settings, see :ref:`Deploying Hubble for DataPlane V2 Network Observability <cce_10_1063>`.

   .. code-block::

      apiVersion: v1
      kind: ConfigMap
      metadata:
        name: cilium-config
        namespace: kube-system
      data:
        enable-hubble: "true"
        hubble-disable-tls: "true"
        hubble-listen-address: :4244
        hubble-metrics: dns drop tcp flow port-distribution icmp http
        hubble-metrics-server: :9965

   After configuring cilium-config, run the following command to rebuild yangtse-cilium in rolling mode to apply the modification:

   .. code-block::

      uuid=$(uuidgen)
      kubectl patch daemonset -nkube-system yangtse-cilium --type='json' -p="[{\"op\": \"add\", \"path\": \"/spec/template/metadata/annotations/change-id\", \"value\": \"$uuid\"}]"

Release History
---------------

You can run the following command to check the cilium-operator image tag for the DataPlane V2 version:

.. code-block::

   kubectl get deploy -nkube-system cilium-operator -oyaml | grep "image:" | cut -d ':' -f 3

Information similar to the following is displayed:

.. code-block::

   3.1.6

+----------------+----------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------+
| Add-on Version | Status         | Supported Cluster Version | What's New                                                                                                                     | Community Version                            |
+================+================+===========================+================================================================================================================================+==============================================+
| 3.1.6          | Commercial use | v1.29                     | -  CCE standard clusters' VPC networks are supported.                                                                          | `v1.19 <https://docs.cilium.io/en/v1.19/>`__ |
|                |                |                           | -  CCE Turbo clusters v1.34 and later are supported. CCE Turbo clusters do not support Layer 7 network policies.               |                                              |
|                |                | v1.30                     | -  Cilium is upgraded to v1.19.1.                                                                                              |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.31                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.32                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.33                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.34                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.35                     |                                                                                                                                |                                              |
+----------------+----------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------+
| 3.0.1          | Commercial use | v1.28                     | -  CCE standard clusters' VPC networks are supported.                                                                          | `v1.18 <https://docs.cilium.io/en/v1.18/>`__ |
|                |                |                           | -  If the initial version of a new cluster is v1.34 or later and DataPlane V2 is enabled, kube-proxy will not run on the node. |                                              |
|                |                | v1.29                     | -  Upgraded Cilium to v1.18.3.                                                                                                 |                                              |
|                |                |                           | -  Support for Ubuntu 22.04                                                                                                    |                                              |
|                |                | v1.30                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.31                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.32                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.33                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.34                     |                                                                                                                                |                                              |
+----------------+----------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------+
| 2.1.1          | Commercial use | v1.27                     | -  Support for CCE standard clusters that use VPC networks                                                                     | `v1.17 <https://docs.cilium.io/en/v1.17/>`__ |
|                |                |                           | -  Upgraded Cilium to v1.17.6.                                                                                                 |                                              |
|                |                | v1.28                     | -  Support for CCE standard clusters of v1.33                                                                                  |                                              |
|                |                |                           | -  Custom Cilium parameters                                                                                                    |                                              |
|                |                | v1.29                     | -  Configurable Hubble observability                                                                                           |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.30                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.31                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.32                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.33                     |                                                                                                                                |                                              |
+----------------+----------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------+
| 2.0.2          | OBT            | v1.27                     | -  Support for CCE standard clusters that use VPC networks                                                                     | `v1.17 <https://docs.cilium.io/en/v1.17/>`__ |
|                |                |                           | -  Upgraded Cilium to v1.17.3.                                                                                                 |                                              |
|                |                | v1.28                     | -  Disabled bpf-lb-sock (by setting **bpf-lb-sock=false**).                                                                    |                                              |
|                |                |                           | -  Disabled host-based firewalls (by setting **enable-host-firewall=false**).                                                  |                                              |
|                |                | v1.29                     | -  Enabled Layer 7 network policies (by setting **enable-l7-proxy=true**).                                                     |                                              |
|                |                |                           | -  Enabled host-routing (by setting **enable-host-legacy-routing=false**).                                                     |                                              |
|                |                | v1.30                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.31                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.32                     |                                                                                                                                |                                              |
+----------------+----------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------+
| 1.0.16         | Limited OBT    | v1.27                     | -  Support for only CCE Turbo clusters                                                                                         | `v1.14 <https://docs.cilium.io/en/v1.14/>`__ |
|                |                |                           | -  Support for CCE Turbo clusters of v1.33                                                                                     |                                              |
|                |                | v1.28                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.29                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.30                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.31                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.32                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.33                     |                                                                                                                                |                                              |
+----------------+----------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------+
| 1.0.15         | Limited OBT    | v1.27                     | -  Support for only CCE Turbo clusters                                                                                         | `v1.14 <https://docs.cilium.io/en/v1.14/>`__ |
|                |                |                           | -  Disabled bpf-lb-sock (by setting **bpf-lb-sock=false**).                                                                    |                                              |
|                |                | v1.28                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.29                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.30                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.31                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.32                     |                                                                                                                                |                                              |
+----------------+----------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------+
| 1.0.8          | Limited OBT    | v1.27                     | -  Support for only CCE Turbo clusters                                                                                         | `v1.14 <https://docs.cilium.io/en/v1.14/>`__ |
|                |                |                           | -  Disabled host-based firewalls (by setting **enable-host-firewall=false**).                                                  |                                              |
|                |                | v1.28                     | -  Disabled L7 network policies (by setting **enable-l7-proxy=false**).                                                        |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.29                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.30                     |                                                                                                                                |                                              |
|                |                |                           |                                                                                                                                |                                              |
|                |                | v1.31                     |                                                                                                                                |                                              |
+----------------+----------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------+
