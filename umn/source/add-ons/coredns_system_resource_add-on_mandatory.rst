:original_name: cce_01_0129.html

.. _cce_01_0129:

coredns (System Resource Add-on, Mandatory)
===========================================

Introduction
------------

The coredns add-on is a DNS server that provides domain name resolution services for Kubernetes clusters. coredns chains plug-ins to provide additional features.

coredns is an open-source software and has been a part of CNCF. It provides a means for cloud services to discover each other in cloud-native deployments. Each of the plug-ins chained by coredns provides a particular DNS function. You can integrate coredns with only the plug-ins you need to make it fast, efficient, and flexible. When used in a Kubernetes cluster, coredns can automatically discover services in the cluster and provide domain name resolution for these services. By working with a cloud DNS server, coredns can resolve external domain names for workloads in a cluster.

**coredns is a system resource add-on. It is installed by default when a cluster of Kubernetes v1.11 or later is created.**

Kubernetes v1.11 and later back CoreDNS as the official default DNS for all clusters going forward.

CoreDNS official website: https://coredns.io/

Open source community: https://github.com/coredns/coredns

Notes and Constraints
---------------------

When CoreDNS is running properly or being upgraded, ensure that the number of available nodes is greater than or equal to the number of CoreDNS instances and all CoreDNS instances are running. Otherwise, the upgrade will fail.

Installing the Add-on
---------------------

This add-on has been installed by default. If it is uninstalled due to some reasons, you can reinstall it by performing the following steps:

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Marketplace** tab page, click **Install Add-on** under **coredns**.

#. On the **Install Add-on** page, select the cluster and the add-on version, and click **Next: Configuration**.

#. In the **Configuration** step, set the following parameters:

   .. table:: **Table 1** coredns add-on parameters

      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                                             |
      +=======================+=========================================================================================================================================================================================================================================================================================================================================+
      | Add-on Specifications | Concurrent domain name resolution ability. Select add-on specifications that best fit your needs.                                                                                                                                                                                                                                       |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Instances             | Number of pods that will be created to match the selected add-on specifications. The number cannot be modified.                                                                                                                                                                                                                         |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Container             | CPU and memory quotas of the container allowed for the selected add-on specifications. The quotas cannot be modified.                                                                                                                                                                                                                   |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Notes                 | Add-on precautions. Read the precautions before you proceed with the step.                                                                                                                                                                                                                                                              |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | stub domain           | A domain name server for a user-defined domain name. The format is a key-value pair. The key is a suffix of DNS domain name, and the value is one or more DNS IP addresses. For example, **acme.local -- 1.2.3.4,6.7.8.9** means that DNS requests with the **.acme.local** suffix are forwarded to a DNS listening at 1.2.3.4,6.7.8.9. |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. After the preceding configurations are complete, click **Install**.

   After the add-on is installed, click **Go Back to Previous Page**. On the **Add-on Instance** tab page, select the corresponding cluster to view the running instance. This indicates that the add-on has been installed on each node in the cluster.

Configuring the Stub Domain for CoreDNS
---------------------------------------

Cluster administrators can modify the ConfigMap for the CoreDNS Corefile to change how service discovery works. They can configure stub domains for CoreDNS using the proxy plug-in.

Assume that a cluster administrator has a Consul DNS server located at 10.150.0.1 and all Consul domain names have the suffix **.consul.local**.

To configure this Consul DNS server in CoreDNS, run the following command to edit the CoreDNS ConfigMap:

**kubectl edit configmap coredns -n kube-system**

Example configuration:

.. code-block::

   consul.local:5353 {
           errors
           cache 30
           proxy . 10.150.0.1
       }

**In clusters of v1.15.11 and later, the modified ConfigMap is as follows:**

.. code-block::

   apiVersion: v1
   metadata:
     name: coredns
     namespace: kube-system
     selfLink: /api/v1/namespaces/kube-system/configmaps/coredns
     uid: 00cb8f29-62d7-4df8-a769-0a16237903c1
     resourceVersion: '2074614'
     creationTimestamp: '2021-04-07T03:52:42Z'
     labels:
       app: coredns
       k8s-app: coredns
       kubernetes.io/cluster-service: 'true'
       kubernetes.io/name: CoreDNS
       release: cceaddon-coredns
   data:
     Corefile: |-
       .:5353 {
           bind {$POD_IP}
           cache 30
           errors
           health {$POD_IP}:8080
           kubernetes cluster.local in-addr.arpa ip6.arpa {
             pods insecure
             upstream /etc/resolv.conf
             fallthrough in-addr.arpa ip6.arpa
           }
           loadbalance round_robin
           prometheus {$POD_IP}:9153
           forward . /etc/resolv.conf
           reload
       }

       consul.local:5353 {
           errors
           cache 30
           proxy . 10.150.0.1
       }

**In clusters earlier than v1.15.11, the modified ConfigMap is as follows:**

.. code-block::

   apiVersion: v1
   data:
     Corefile: |-
       .:5353 {
           cache 30
           errors
           health
           kubernetes cluster.local in-addr.arpa ip6.arpa {
             pods insecure
             upstream /etc/resolv.conf
             fallthrough in-addr.arpa ip6.arpa
           }
           loadbalance round_robin
           prometheus 0.0.0.0:9153
           proxy . /etc/resolv.conf
           reload
       }

       consul.local:5353 {
           errors
           cache 30
           proxy . 10.150.0.1
       }
   kind: ConfigMap
   metadata:
     name: coredns
     namespace: kube-system

How Does Domain Name Resolution Work in Kubernetes?
---------------------------------------------------

DNS policies can be set on a per-pod basis. Currently, Kubernetes supports four types of DNS policies: **Default**, **ClusterFirst**, **ClusterFirstWithHostNet**, and **None**. For details, see https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/. These policies are specified in the **dnsPolicy** field in the pod-specific.

-  **Default**: Pods inherit the name resolution configuration from the node that the pods run on. The custom upstream DNS server and the stub domain cannot be used together with this policy.
-  **ClusterFirst**: Any DNS query that does not match the configured cluster domain suffix, such as **www.kubernetes.io**, is forwarded to the upstream name server inherited from the node. Cluster administrators may have extra stub domains and upstream DNS servers configured.
-  **ClusterFirstWithHostNet**: For pods running with hostNetwork, set its DNS policy **ClusterFirstWithHostNet**.
-  **None**: It allows a pod to ignore DNS settings from the Kubernetes environment. All DNS settings are supposed to be provided using the **dnsPolicy** field in the pod-specific.

.. note::

   -  Clusters of Kubernetes v1.10 and later support **Default**, **ClusterFirst**, **ClusterFirstWithHostNet**, and **None**. Clusters earlier than Kubernetes v1.10 support only **Default**, **ClusterFirst**, and **ClusterFirstWithHostNet**.
   -  **Default** is not the default DNS policy. If **dnsPolicy** is not explicitly specified, **ClusterFirst** is used.

**Routing**

**Without stub domain configurations**: Any query that does not match the configured cluster domain suffix, such as **www.kubernetes.io**, is forwarded to the upstream DNS server inherited from the node.

**With stub domain configurations**: If stub domains and upstream DNS servers are configured, DNS queries are routed according to the following flow:

#. The query is first sent to the DNS caching layer in coredns.
#. From the caching layer, the suffix of the request is examined and then the request is forwarded to the corresponding DNS:

   -  Names with the cluster suffix, for example, **.cluster.local**: The request is sent to coredns.

   -  Names with the stub domain suffix, for example, **.acme.local**: The request is sent to the configured custom DNS resolver that listens, for example, on 1.2.3.4.
   -  Names that do not match the suffix (for example, **widget.com**): The request is forwarded to the upstream DNS.


.. figure:: /_static/images/en-us_image_0186273271.png
   :alt: **Figure 1** Routing

   **Figure 1** Routing

Upgrading the Add-on
--------------------

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Instance** tab page, click **Upgrade** under **coredns**.

   .. note::

      -  If the **Upgrade** button is unavailable, the current add-on is already up-to-date and no upgrade is required.
      -  During the upgrade, the previous configurations are lost and need to be specified again.
      -  When the upgrade is complete, the original coredns version on cluster nodes will be replaced by the latest version. If an exception occurs during the upgrade, uninstall the add-on and then re-install it.

#. On the **Basic Information** page, select the add-on version and click **Next**.

#. Configure the parameters listed in :ref:`Table 2 <cce_01_0129__table1410658238>`. After the configuration is complete, click **Upgrade** to upgrade the coredns add-on.

   .. _cce_01_0129__table1410658238:

   .. table:: **Table 2** Parameters for installing coredns

      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                                             |
      +=======================+=========================================================================================================================================================================================================================================================================================================================================+
      | Add-on Specifications | Concurrent domain name resolution ability. Select add-on specifications that best fit your needs.                                                                                                                                                                                                                                       |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | stub domain           | A domain name server for a user-defined domain name. The format is a key-value pair. The key is a suffix of DNS domain name, and the value is one or more DNS IP addresses. For example, **acme.local -- 1.2.3.4,6.7.8.9** means that DNS requests with the **.acme.local** suffix are forwarded to a DNS listening at 1.2.3.4,6.7.8.9. |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Uninstalling the Add-on
-----------------------

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Instance** tab page, click **Uninstall** under **coredns**.
#. In the dialog box displayed, click **Yes** to uninstall the add-on.
