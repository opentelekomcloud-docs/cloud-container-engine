:original_name: cce_10_0945.html

.. _cce_10_0945:

DataPlane V2 Network Acceleration
=================================

DataPlane V2 can be enabled in clusters that use VPC networks or Cloud Native Network 2.0. This function supports eBPF redirection for applying network policies.

.. note::

   CCE DataPlane V2 is released with restrictions. To use this feature, submit a service ticket to CCE.

+-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| DataPlane V2                      | Description                                                                                                                                                                                                                                                                                                                      |
+===================================+==================================================================================================================================================================================================================================================================================================================================+
| Technical implementation          | DataPlane V2 integrates open-source `Cilium <https://docs.cilium.io/en/stable/>`__ to provide capabilities such as network policies.                                                                                                                                                                                             |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Supported cluster versions        | CCE standard clusters using VPC networks for commercial use of v1.27.16-r30, v1.28.15-r20, v1.29.13-r0, v1.30.10-r0, v1.31.6-r0, or later                                                                                                                                                                                        |
|                                   |                                                                                                                                                                                                                                                                                                                                  |
|                                   | CCE Turbo clusters in limited OBT of v1.27.16-r10, v1.28.15-r0, v1.29.10-r0, v1.30.6-r0, or later                                                                                                                                                                                                                                |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Usage                             | -  When creating a CCE standard cluster, select the VPC network in the container network configuration and enable **DataPlane V2**.                                                                                                                                                                                              |
|                                   | -  When creating a CCE Turbo cluster, select Cloud Native Network 2.0 and enable **DataPlane V2**.                                                                                                                                                                                                                               |
|                                   |                                                                                                                                                                                                                                                                                                                                  |
|                                   | .. important::                                                                                                                                                                                                                                                                                                                   |
|                                   |                                                                                                                                                                                                                                                                                                                                  |
|                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                       |
|                                   |                                                                                                                                                                                                                                                                                                                                  |
|                                   |    -  After DataPlane V2 is enabled, secure containers (Kata Containers as the container runtime) are not supported.                                                                                                                                                                                                             |
|                                   |    -  Enabled DataPlane V2 cannot be disabled.                                                                                                                                                                                                                                                                                   |
|                                   |    -  DataPlane V2 can only be enabled for new clusters.                                                                                                                                                                                                                                                                         |
|                                   |    -  CCE Turbo DataPlane V2 is in limited OBT. Upgrading it to a commercial version requires the node to be reset. Exercise caution when enabling this function.                                                                                                                                                                |
|                                   |    -  If Layer 7 network policies or DNS-based policies are enabled for services in your cluster, the traffic that matches these policies will be disrupted during a Cilium upgrade. For details, see the `constraints in the community <https://docs.cilium.io/en/v1.17/operations/upgrade/#version-specific-notes>`__.         |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Supported OS                      | Only HCE OS 2.0 is supported.                                                                                                                                                                                                                                                                                                    |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Performance optimization          | -  EDT is used to limit the egress bandwidth. This makes bandwidth limitation more accurate and resource consumption lower.                                                                                                                                                                                                      |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Bandwidth                         | After DataPlane V2 network acceleration is enabled, pods on the nodes running HCE OS 2.0 use EDT to limit the egress bandwidth. The ingress bandwidth limitation is not supported. If DataPlane V2 is not enabled, the TBF Qdisc is used to limit the bandwidth. For details, see :ref:`Configuring QoS for Pods <cce_10_0382>`. |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| NetworkPolicy                     | -  The implementation of network policies is different from that of container tunnel networks. For details, see :ref:`Configuring Network Policies to Restrict Pod Access <cce_10_0059>`.                                                                                                                                        |
|                                   |                                                                                                                                                                                                                                                                                                                                  |
|                                   |    -  The IPBlock selector can only select CIDR blocks outside a cluster.                                                                                                                                                                                                                                                        |
|                                   |    -  The IPBlock selector does not have good support for the **except** keyword, so this keyword is not recommended.                                                                                                                                                                                                            |
|                                   |    -  If a network policy of the egress type is used, the pod fails to access the IP addresses of the hostNetwork pod and node in the cluster.                                                                                                                                                                                   |
|                                   |                                                                                                                                                                                                                                                                                                                                  |
|                                   | -  The `CiliumNetworkPolicy <https://docs.cilium.io/en/v1.17/network/kubernetes/policy/#ciliumnetworkpolicy>`__ and `CiliumClusterwideNetworkPolicy <https://docs.cilium.io/en/v1.17/network/kubernetes/policy/#ciliumclusterwidenetworkpolicy>`__ APIs support different functions in different clusters.                       |
|                                   |                                                                                                                                                                                                                                                                                                                                  |
|                                   |    -  CCE standard clusters do not support `host policies <https://docs.cilium.io/en/v1.17/security/policy/language/#hostpolicies>`__.                                                                                                                                                                                           |
|                                   |    -  CCE Turbo clusters do not support `host policies <https://docs.cilium.io/en/v1.14/security/policy/language/#hostpolicies>`__, `DNS-based policies <https://docs.cilium.io/en/v1.14/security/dns/>`__, or `Layer 7 network policies <https://docs.cilium.io/en/v1.14/security/policy/language/#layer-7-examples>`__.        |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Resource consumption              | The resident cilium-agent process on each node is responsible for eBPF network acceleration. Each cilium-agent process may occupy 80 MiB of memory. Each time a pod is added, the cilium-agent memory consumption may increase by 10 KiB.                                                                                        |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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

+-------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+-----------------+
| ConfigMap Configuration | Description                                                                                                                                                                                                              | Configurable Component                                                                | Priority        |
+=========================+==========================================================================================================================================================================================================================+=======================================================================================+=================+
| yangtse-cilium-config   | Default DataPlane V2 configuration. If this configuration is modified, the configuration will be restored to what it was during the cluster upgrade. **Do not modify this configuration.**                               | cilium-agent                                                                          | 1               |
+-------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+-----------------+
| cilium-config           | Native configuration of the Cilium community. The priority of this configuration is lower than that of yangtse-cilium-config. **If you need to create custom DataPlane V2 components, modify this configuration first.** | `cilium-agent <https://docs.cilium.io/en/v1.17/cmdref/cilium-agent/>`__               | 2               |
|                         |                                                                                                                                                                                                                          |                                                                                       |                 |
|                         |                                                                                                                                                                                                                          | `cilium-operator <https://docs.cilium.io/en/v1.17/cmdref/cilium-operator-generic/>`__ |                 |
+-------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+-----------------+

.. note::

   Currently, only the Cilium add-on of 2.1.1 or later supports custom configuration.

Example custom network components:

-  Example 1: When cilium-agent is abnormal, you can configure **--set-cilium-node-taints=true** for cilium-operator to automatically add taints to a node to prevent pods from being scheduled to that node.

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

-  Example 2: If you want to enable Hubble of cilium-agent, you can create the following naive ConfigMap configuration of the Cilium community. For details about the parameter settings, see :ref:`Deploying Hubble for DataPlane V2 Network Observability <cce_10_1063>`.

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

   After configuring cilium-config, you can run the following command to roll back and rebuild yangtse-cilium to apply the configuration:

   .. code-block::

      uuid=$(uuidgen)
      kubectl patch daemonset -nkube-system yangtse-cilium --type='json' -p="[{\"op\": \"add\", \"path\": \"/spec/template/metadata/annotations/change-id\", \"value\": \"$uuid\"}]"

Change History
--------------

You can run the following command to check the cilium-operator image tag for the DataPlane V2 version:

.. code-block::

   kubectl get deploy -nkube-system cilium-operator -oyaml | grep "image:" | cut -d ':' -f 3

Information similar to the following is displayed:

.. code-block::

   2.1.1

+----------------+-------------+-----------------+-------------------------------------------------------------------------------+----------------------------------------------+
| Add-on Version | Status      | Cluster Version | New Feature                                                                   | Community Version                            |
+================+=============+=================+===============================================================================+==============================================+
| 2.0.2          | OBT         | v1.27           | -  Support for only the CCE standard clusters that use VPC networks           | `v1.17 <https://docs.cilium.io/en/v1.17/>`__ |
|                |             |                 | -  Upgraded Cilium to v1.17.3.                                                |                                              |
|                |             | v1.28           | -  Disabled bpf-lb-sock (by setting **bpf-lb-sock=false**).                   |                                              |
|                |             |                 | -  Disabled host-based firewalls (by setting **enable-host-firewall=false**). |                                              |
|                |             | v1.29           | -  Enabled Layer 7 network policies (by setting **enable-l7-proxy=true**).    |                                              |
|                |             |                 | -  Enabled host-routing (by setting **enable-host-legacy-routing=false**).    |                                              |
|                |             | v1.30           |                                                                               |                                              |
|                |             |                 |                                                                               |                                              |
|                |             | v1.31           |                                                                               |                                              |
|                |             |                 |                                                                               |                                              |
|                |             | v1.32           |                                                                               |                                              |
+----------------+-------------+-----------------+-------------------------------------------------------------------------------+----------------------------------------------+
| 1.0.15         | Limited OBT | v1.27           | -  Disabled bpf-lb-sock (by setting **bpf-lb-sock=false**).                   | `v1.14 <https://docs.cilium.io/en/v1.14/>`__ |
|                |             |                 |                                                                               |                                              |
|                |             | v1.28           |                                                                               |                                              |
|                |             |                 |                                                                               |                                              |
|                |             | v1.29           |                                                                               |                                              |
|                |             |                 |                                                                               |                                              |
|                |             | v1.30           |                                                                               |                                              |
|                |             |                 |                                                                               |                                              |
|                |             | v1.31           |                                                                               |                                              |
|                |             |                 |                                                                               |                                              |
|                |             | v1.32           |                                                                               |                                              |
+----------------+-------------+-----------------+-------------------------------------------------------------------------------+----------------------------------------------+
| 1.0.8          | Limited OBT | v1.27           | -  Supported CCE Turbo clusters that use Cloud Native 2.0 networks.           | `v1.14 <https://docs.cilium.io/en/v1.14/>`__ |
|                |             |                 | -  Disabled host-based firewalls (by setting **enable-host-firewall=false**). |                                              |
|                |             | v1.28           | -  Disabled L7 network policies (by setting **enable-l7-proxy=false**).       |                                              |
|                |             |                 |                                                                               |                                              |
|                |             | v1.29           |                                                                               |                                              |
|                |             |                 |                                                                               |                                              |
|                |             | v1.30           |                                                                               |                                              |
|                |             |                 |                                                                               |                                              |
|                |             | v1.31           |                                                                               |                                              |
+----------------+-------------+-----------------+-------------------------------------------------------------------------------+----------------------------------------------+
