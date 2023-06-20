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

#. Log in to the CCE console and access the cluster console.

#. In the navigation pane, choose **Add-ons**. On the displayed page, click **Edit** under CoreDNS.

#. Add a stub domain in the **Parameters** area.

   Modify the **stub_domains** parameter in the format of a key-value pair. The key is a DNS suffix domain name, and the value is a DNS IP address or a group of DNS IP addresses.

   .. code-block::

      {
          "stub_domains": {
                      "consul.local": [
                  "10.150.0.1"
              ]
          },
          "upstream_nameservers": []
      }

#. Click **OK**.

You can also modify the ConfigMap as follows:

.. important::

   The parameter values in red in the example can only be modified and cannot be deleted.

.. code-block::

   $ kubectl edit configmap coredns -n kube-system
   apiVersion: v1
   data:
     Corefile: |-
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
       }

       consul.local:5353 {
           bind {$POD_IP}
           errors
           cache 30
           forward . 10.150.0.1
       }
   kind: ConfigMap
   metadata:
     creationTimestamp: "2022-05-04T04:42:24Z"
     labels:
       app: coredns
       k8s-app: coredns
       kubernetes.io/cluster-service: "true"
       kubernetes.io/name: CoreDNS
       release: cceaddon-coredns
     name: coredns
     namespace: kube-system
     resourceVersion: "8663493"
     uid: bba87142-9f8d-4056-b8a6-94c3887e9e1d

.. _cce_10_0361__section106211954135311:

Modifying the CoreDNS Hosts Configuration File
----------------------------------------------

#. Use kubectl to connect to the cluster.

#. Modify the CoreDNS configuration file and add the custom domain name to the hosts file.

   Point **www.example.com** to **192.168.1.1**. When CoreDNS resolves **www.example.com**, **192.168.1.1** is returned.

   .. important::

      The fallthrough field must be configured. **fallthrough** indicates that when the domain name to be resolved cannot be found in the hosts file, the resolution task is transferred to the next CoreDNS plug-in. If **fallthrough** is not specified, the task ends and the domain name resolution stops. As a result, the domain name resolution in the cluster fails.

      For details about how to configure the hosts file, visit https://coredns.io/plugins/hosts/.

   .. code-block::

      $ kubectl edit configmap coredns -n kube-system
      apiVersion: v1
      data:
        Corefile: |-
          .:5353 {
              bind {$POD_IP}
              cache 30
              errors
              health {$POD_IP}:8080
              kubernetes cluster.local in-addr.arpa ip6.arpa {
                pods insecure
                fallthrough in-addr.arpa ip6.arpa
              }
              hosts {
                192.168.1.1 www.example.com
                fallthrough
              }
              loadbalance round_robin
              prometheus {$POD_IP}:9153
              forward . /etc/resolv.conf
              reload
          }
      kind: ConfigMap
      metadata:
        creationTimestamp: "2021-08-23T13:27:28Z"
        labels:
          app: coredns
          k8s-app: coredns
          kubernetes.io/cluster-service: "true"
          kubernetes.io/name: CoreDNS
          release: cceaddon-coredns
        name: coredns
        namespace: kube-system
        resourceVersion: "460"
        selfLink: /api/v1/namespaces/kube-system/configmaps/coredns
        uid: be64aaad-1629-441f-8a40-a3efc0db9fa9

   After modifying the hosts file in CoreDNS, you do not need to configure the hosts file in each pod.

.. _cce_10_0361__section2213823544:

Adding the CoreDNS Rewrite Configuration to Point the Domain Name to Services in the Cluster
--------------------------------------------------------------------------------------------

Use the Rewrite plug-in of CoreDNS to resolve a specified domain name to the domain name of a Service.

#. Use kubectl to connect to the cluster.

#. Modify the CoreDNS configuration file to point **example.com** to the **example** service in the **default** namespace.

   .. code-block::

      $ kubectl edit configmap coredns -n kube-system
      apiVersion: v1
      data:
        Corefile: |-
          .:5353 {
              bind {$POD_IP}
              cache 30
              errors
              health {$POD_IP}:8080
              kubernetes cluster.local in-addr.arpa ip6.arpa {
                pods insecure
                fallthrough in-addr.arpa ip6.arpa
              }
              rewrite name example.com example.default.svc.cluster.local
              loadbalance round_robin
              prometheus {$POD_IP}:9153
              forward . /etc/resolv.conf
              reload
          }
      kind: ConfigMap
      metadata:
        creationTimestamp: "2021-08-23T13:27:28Z"
        labels:
          app: coredns
          k8s-app: coredns
          kubernetes.io/cluster-service: "true"
          kubernetes.io/name: CoreDNS
          release: cceaddon-coredns
        name: coredns
        namespace: kube-system
        resourceVersion: "460"
        selfLink: /api/v1/namespaces/kube-system/configmaps/coredns
        uid: be64aaad-1629-441f-8a40-a3efc0db9fa9

.. _cce_10_0361__section677819913541:

Using CoreDNS to Cascade Self-Built DNS
---------------------------------------

#. Use kubectl to connect to the cluster.

#. Modify the CoreDNS configuration file and change **/etc/resolv.conf** following **forward** to the IP address of the external DNS server.

   .. code-block::

      $ kubectl edit configmap coredns -n kube-system
      apiVersion: v1
      data:
        Corefile: |-
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
              forward . 192.168.1.1
              reload
          }
      kind: ConfigMap
      metadata:
        creationTimestamp: "2021-08-23T13:27:28Z"
        labels:
          app: coredns
          k8s-app: coredns
          kubernetes.io/cluster-service: "true"
          kubernetes.io/name: CoreDNS
          release: cceaddon-coredns
        name: coredns
        namespace: kube-system
        resourceVersion: "460"
        selfLink: /api/v1/namespaces/kube-system/configmaps/coredns
        uid: be64aaad-1629-441f-8a40-a3efc0db9fa9
