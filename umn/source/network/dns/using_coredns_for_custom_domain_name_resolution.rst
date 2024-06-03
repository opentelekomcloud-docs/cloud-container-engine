:original_name: cce_10_0361.html

.. _cce_10_0361:

Using CoreDNS for Custom Domain Name Resolution
===============================================

Challenges
----------

When using CCE, you may need to resolve custom internal domain names in the following scenarios:

-  In the legacy code, a fixed domain name is configured for calling other internal services. If the system decides to use Kubernetes Services, the code refactoring workload could be heavy.
-  A service is created outside the cluster. Data in the cluster needs to be sent to the service through a fixed domain name.

Solution
--------

There are several CoreDNS-based solutions for custom domain name resolution:

-  :ref:`Configuring the Stub Domain for CoreDNS <cce_10_0361__section5202157467>`: You can add it on the console, which is easy to operate.
-  :ref:`Using the CoreDNS Hosts plug-in to configure resolution for any domain name <cce_10_0361__section106211954135311>`: You can add any record set, which is similar to adding a record set in the local **/etc/hosts** file.
-  :ref:`Using the CoreDNS Rewrite plug-in to point a domain name to a service in the cluster <cce_10_0361__section2213823544>`: A nickname is assigned to the Kubernetes Service. You do not need to know the IP address of the resolution record in advance.
-  :ref:`Using the CoreDNS Forward plug-in to set the self-built DNS as the upstream DNS <cce_10_0361__section677819913541>`: The self-built DNS can manage a large number of resolution records. You do not need to modify the CoreDNS configuration when adding or deleting records.

Precautions
-----------

Improper modification on CoreDNS configuration may cause domain name resolution failures in the cluster. Perform tests before and after the modification.

.. _cce_10_0361__section5202157467:

Configuring the Stub Domain for CoreDNS
---------------------------------------

Cluster administrators can modify the ConfigMap for the CoreDNS Corefile to change how service discovery works.

Assume that a cluster administrator has a Consul DNS server located at 10.150.0.1 and all Consul domain names have the suffix **.consul.local**.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. Then, click **Edit** under **CoreDNS**.

#. Add a stub domain in the **Parameters** area. The format is a key-value pair. The key is a DNS suffix domain name, and the value is a DNS IP address or a group of DNS IP addresses, for example, **consul.local -- 10.150.0.1**.

#. Click **OK**.

#. Choose **ConfigMaps and Secrets** in the navigation pane, select the **kube-system** namespace, and view the ConfigMap data of **coredns** to check whether the update is successful.

   The corresponding Corefile content is as follows:

   .. code-block::

      .:5353 {
          bind {$POD_IP}
          cache 30
          errors
          health {$POD_IP}:8080
          kubernetes cluster.local in-addr.arpa ip6.arpa {
              pods insecure
              fallthrough in-addr.arpa ip6.arpa
          }
          loadbalance round_robin
          prometheus {$POD_IP}:9153
          forward . /etc/resolv.conf {
              policy random
          }
          reload
          ready {$POD_IP}:8081
      }
      consul.local:5353 {
          bind {$POD_IP}
          errors
          cache 30
          forward . 10.150.0.1
      }

.. _cce_10_0361__section106211954135311:

Modifying the CoreDNS Hosts Configuration File
----------------------------------------------

After modifying the hosts file in CoreDNS, you do not need to configure the hosts file in each pod to add resolution records.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. Then, click **Edit** under **CoreDNS**.

#. Edit the advanced configuration under **Parameters** and add the following content to the **plugins** field:

   .. code-block::

      {
        "configBlock": "192.168.1.1 www.example.com\nfallthrough",
        "name": "hosts"
      }

   .. important::

      The **fallthrough** field must be configured. **fallthrough** indicates that when the domain name to be resolved cannot be found in the hosts file, the resolution task is transferred to the next CoreDNS plug-in. If **fallthrough** is not specified, the task ends and the domain name resolution stops. As a result, the domain name resolution in the cluster fails.

      For details about how to configure the hosts file, visit https://coredns.io/plugins/hosts/.

#. Click **OK**.

#. Choose **ConfigMaps and Secrets** in the navigation pane, select the **kube-system** namespace, and view the ConfigMap data of **coredns** to check whether the update is successful.

   The corresponding Corefile content is as follows:

   .. code-block::

      .:5353 {
          bind {$POD_IP}
          hosts {
            192.168.1.1 www.example.com
            fallthrough
          }
          cache 30
          errors
          health {$POD_IP}:8080
          kubernetes cluster.local in-addr.arpa ip6.arpa {
              pods insecure
              fallthrough in-addr.arpa ip6.arpa
          }
          loadbalance round_robin
          prometheus {$POD_IP}:9153
          forward . /etc/resolv.conf {
              policy random
          }
          reload
          ready {$POD_IP}:8081
      }

.. _cce_10_0361__section2213823544:

Adding the CoreDNS Rewrite Configuration to Point the Domain Name to Services in the Cluster
--------------------------------------------------------------------------------------------

Use the Rewrite plug-in of CoreDNS to resolve a specified domain name to the domain name of a Service. For example, the request for accessing the example.com domain name is redirected to the example.default.svc.cluster.local domain name, that is, the example service in the default namespace.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. Then, click **Edit** under **CoreDNS**.

#. Edit the advanced configuration under **Parameters** and add the following content to the **plugins** field:

   .. code-block::

      {
         "name": "rewrite",
         "parameters": "name example.com example.default.svc.cluster.local"
      }

#. Click **OK**.

#. Choose **ConfigMaps and Secrets** in the navigation pane, select the **kube-system** namespace, and view the ConfigMap data of **coredns** to check whether the update is successful.

   The corresponding Corefile content is as follows:

   .. code-block::

      .:5353 {
          bind {$POD_IP}
          rewrite name example.com example.default.svc.cluster.local
          cache 30
          errors
          health {$POD_IP}:8080
          kubernetes cluster.local in-addr.arpa ip6.arpa {
              pods insecure
              fallthrough in-addr.arpa ip6.arpa
          }
          loadbalance round_robin
          prometheus {$POD_IP}:9153
          forward . /etc/resolv.conf {
              policy random
          }
          reload
          ready {$POD_IP}:8081
      }

.. _cce_10_0361__section677819913541:

Using CoreDNS to Cascade Self-Built DNS
---------------------------------------

By default, CoreDNS uses the **/etc/resolv.conf** file of the node for resolution. You can also change the resolution address to that of the external DNS.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. Then, click **Edit** under **CoreDNS**.

#. Edit the advanced configuration under **Parameters** and modify the following content in the **plugins** field:

   .. code-block::

      {
          "configBlock": "policy random",
          "name": "forward",
          "parameters": ". 192.168.1.1"
      }

#. Click **OK**.

#. Choose **ConfigMaps and Secrets** in the navigation pane, select the **kube-system** namespace, and view the ConfigMap data of **coredns** to check whether the update is successful.

   The corresponding Corefile content is as follows:

   .. code-block::

      .:5353 {
          bind {$POD_IP}
          cache 30
          errors
          health {$POD_IP}:8080
          kubernetes cluster.local in-addr.arpa ip6.arpa {
              pods insecure
              fallthrough in-addr.arpa ip6.arpa
          }
          loadbalance round_robin
          prometheus {$POD_IP}:9153
          forward . 192.168.1.1 {
              policy random
          }
          reload
          ready {$POD_IP}:8081
      }
