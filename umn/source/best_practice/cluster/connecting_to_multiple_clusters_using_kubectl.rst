:original_name: cce_bestpractice_00254.html

.. _cce_bestpractice_00254:

Connecting to Multiple Clusters Using kubectl
=============================================

Painpoint
---------

When you have multiple CCE clusters, you may find it difficult to efficiently connect to all of them.

Solution
--------

This section describes how to configure access to multiple clusters by modifying **kubeconfig.json**. The file describes multiple clusters, users, and contexts. To access different clusters, run the **kubectl config use-context** command to switch between contexts.


.. figure:: /_static/images/en-us_image_0261820020.png
   :alt: **Figure 1** Using kubectl to connect to multiple clusters

   **Figure 1** Using kubectl to connect to multiple clusters

Prerequisites
-------------

kubectl can access multiple clusters.

Introduction to kubeconfig.json
-------------------------------

kubeconfig.json is the configuration file of kubectl. You can download it on the cluster details page.

|image1|

The content of kubeconfig.json is as follows:

.. code-block::

   {
       "kind": "Config",
       "apiVersion": "v1",
       "preferences": {},
       "clusters": [{
           "name": "internalCluster",
           "cluster": {
               "server": "https://192.168.0.85:5443",
               "certificate-authority-data": "LS0tLS1CRUULIE..."
           }
       }, {
           "name": "externalCluster",
           "cluster": {
               "server": "https://xxx.xxx.xxx.xxx:5443",
               "insecure-skip-tls-verify": true
           }
       }],
       "users": [{
           "name": "user",
           "user": {
               "client-certificate-data": "LS0tLS1CRUdJTiBDRVJ...",
               "client-key-data": "LS0tLS1CRUdJTiBS..."
           }
       }],
       "contexts": [{
           "name": "internal",
           "context": {
               "cluster": "internalCluster",
               "user": "user"
           }
       }, {
           "name": "external",
           "context": {
               "cluster": "externalCluster",
               "user": "user"
           }
       }],
       "current-context": "external"
   }

It mainly consists of three sections.

-  **clusters**: describes the cluster information, mainly the access address of the cluster.
-  **users**: describes information about the users who access the cluster. It includes the **client-certificate-data** and **client-key-data** certificate files.
-  **contexts**: describes the configuration contexts. You switch between contexts to access different clusters. A context is associated with **user** and **cluster**, that is, it defines which user accesses which cluster.

The preceding kubeconfig.json defines the private network address and public network address of the cluster as two clusters with two different contexts. You can switch the context to use different addresses to access the cluster.

Configuring Access to Multiple Clusters
---------------------------------------

The following steps walk you through the procedure of configuring access to two clusters by modifying kubeconfig.json.

This example configures only the public network access to the clusters. If you want to access multiple clusters over private networks, retain the **clusters** field and ensure that the clusters can be accessed over private networks. Its configuration is similar to that described in this example.

#. Download kubeconfig.json of the two clusters and delete the lines related to private network access, as shown in the following figure.

   -  Cluster A:

      .. code-block::

         {
             "kind": "Config",
             "apiVersion": "v1",
             "preferences": {},
             "clusters": [ {
                 "name": "externalCluster",
                 "cluster": {
                     "server": "https://119.xxx.xxx.xxx:5443",
                     "insecure-skip-tls-verify": true
                 }
             }],
             "users": [{
                 "name": "user",
                 "user": {
                     "client-certificate-data": "LS0tLS1CRUdJTxM...",
                     "client-key-data": "LS0tLS1CRUdJTiB...."
                 }
             }],
             "contexts": [{
                 "name": "external",
                 "context": {
                     "cluster": "externalCluster",
                     "user": "user"
                 }
             }],
             "current-context": "external"
         }

   -  Cluster B:

      .. code-block::

         {
             "kind": "Config",
             "apiVersion": "v1",
             "preferences": {},
             "clusters": [ {
                 "name": "externalCluster",
                 "cluster": {
                     "server": "https://124.xxx.xxx.xxx:5443",
                     "insecure-skip-tls-verify": true
                 }
             }],
             "users": [{
                 "name": "user",
                 "user": {
                     "client-certificate-data": "LS0tLS1CRUdJTxM...",
                     "client-key-data": "LS0rTUideUdJTiB...."
                 }
             }],
             "contexts": [{
                 "name": "external",
                 "context": {
                     "cluster": "externalCluster",
                     "user": "user"
                 }
             }],
             "current-context": "external"
         }

      The preceding files have the same structure except that the **client-certificate-data** and **client-key-data** fields of **user** and the **clusters.cluster.server** field are different.

#. Modify the **name** field as follows:

   -  Cluster A:

      .. code-block::

         {
             "kind": "Config",
             "apiVersion": "v1",
             "preferences": {},
             "clusters": [ {
                 "name": "Cluster-A",
                 "cluster": {
                     "server": "https://119.xxx.xxx.xxx:5443",
                     "insecure-skip-tls-verify": true
                 }
             }],
             "users": [{
                 "name": "Cluster-A-user",
                 "user": {
                     "client-certificate-data": "LS0tLS1CRUdJTxM...",
                     "client-key-data": "LS0tLS1CRUdJTiB...."
                 }
             }],
             "contexts": [{
                 "name": "Cluster-A-Context",
                 "context": {
                     "cluster": "Cluster-A",
                     "user": "Cluster-A-user"
                 }
             }],
             "current-context": "Cluster-A-Context"
         }

   -  Cluster B:

      .. code-block::

         {
             "kind": "Config",
             "apiVersion": "v1",
             "preferences": {},
             "clusters": [ {
                 "name": "Cluster-B",
                 "cluster": {
                     "server": "https://124.xxx.xxx.xxx:5443",
                     "insecure-skip-tls-verify": true
                 }
             }],
             "users": [{
                 "name": "Cluster-B-user",
                 "user": {
                     "client-certificate-data": "LS0tLS1CRUdJTxM...",
                     "client-key-data": "LS0rTUideUdJTiB...."
                 }
             }],
             "contexts": [{
                 "name": "Cluster-B-Context",
                 "context": {
                     "cluster": "Cluster-B",
                     "user": "Cluster-B-user"
                 }
             }],
             "current-context": "Cluster-B-Context"
         }

#. Combine these two files.

   The file structure remains unchanged. Combine the contents of **clusters**, **users**, and **contexts** as follows:

   .. code-block::

      {
          "kind": "Config",
          "apiVersion": "v1",
          "preferences": {},
          "clusters": [ {
              "name": "Cluster-A",
              "cluster": {
                  "server": "https://119.xxx.xxx.xxx:5443",
                  "insecure-skip-tls-verify": true
              }
          },
           {
              "name": "Cluster-B",
              "cluster": {
                  "server": "https://124.xxx.xxx.xxx:5443",
                  "insecure-skip-tls-verify": true
              }
          }],
          "users": [{
              "name": "Cluster-A-user",
              "user": {
                  "client-certificate-data": "LS0tLS1CRUdJTxM...",
                  "client-key-data": "LS0tLS1CRUdJTiB...."
              }
          },
          {
              "name": "Cluster-B-user",
              "user": {
                  "client-certificate-data": "LS0tLS1CRUdJTxM...",
                  "client-key-data": "LS0rTUideUdJTiB...."
              }
          }],
          "contexts": [{
              "name": "Cluster-A-Context",
              "context": {
                  "cluster": "Cluster-A",
                  "user": "Cluster-A-user"
              }
          },
          {
              "name": "Cluster-B-Context",
              "context": {
                  "cluster": "Cluster-B",
                  "user": "Cluster-B-user"
              }
          }],
          "current-context": "Cluster-A-Context"
      }

Verification
------------

Run the following commands to copy the file to the kubectl configuration path:

**mkdir -p $HOME/.kube**

**mv -f kubeconfig.json $HOME/.kube/config**

Run the kubectl commands to check whether the two clusters can be connected.

.. code-block::

   # kubectl config use-context Cluster-A-Context
   Switched to context "Cluster-A-Context".
   # kubectl cluster-info
   Kubernetes control plane is running at https://119.xxx.xxx.xxx:5443
   CoreDNS is running at https://119.xxx.xxx.xxx:5443/api/v1/namespaces/kube-system/services/coredns:dns/proxy

   To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

   # kubectl config use-context Cluster-B-Context
   Switched to context "Cluster-B-Context".
   # kubectl cluster-info
   Kubernetes control plane is running at https://124.xxx.xxx.xxx:5443
   CoreDNS is running at https://124.xxx.xxx.xxx:5443/api/v1/namespaces/kube-system/services/coredns:dns/proxy

   To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

.. |image1| image:: /_static/images/en-us_image_0000001274882416.png
