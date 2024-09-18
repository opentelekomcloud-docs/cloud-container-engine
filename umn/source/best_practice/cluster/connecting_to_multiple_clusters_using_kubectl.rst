:original_name: cce_bestpractice_00254.html

.. _cce_bestpractice_00254:

Connecting to Multiple Clusters Using kubectl
=============================================

Background
----------

The kubectl command line tool relies on the kubeconfig configuration file to locate the necessary authentication information to select a cluster and communicate with its API server. By default, kubectl uses the **$HOME/.kube/config** file as the credential for accessing the cluster.

When working with CCE clusters on a daily basis, it is common to manage multiple clusters simultaneously. However, this can make using the kubectl command line tool to connect to clusters cumbersome, as it requires frequent switching of the kubeconfig file during routine O&M. This section introduces how to connect to multiple clusters using the same kubectl client.

.. note::

   The file used to configure cluster access is called the kubeconfig file, but it does not mean that the file name is **kubeconfig**.

Solution
--------

When performing O&M on Kubernetes clusters, it is often necessary to switch between multiple clusters. The following shows some typical solutions for cluster switchover:

-  :ref:`Solution 1 <cce_bestpractice_00254__section673745134411>`: Specify **--kubeconfig** of kubectl to select the kubeconfig file used by each cluster and use aliases to simplify commands.

-  :ref:`Solution 2 <cce_bestpractice_00254__section1272762813465>`: Combine clusters, users, and credentials in multiple kubeconfig files into one configuration file and run **kubectl config use-context** to switch clusters.

   Compared with solution 1, this solution requires manual configuration of the kubeconfig file, which is relatively complex.


.. figure:: /_static/images/en-us_image_0000001950315204.png
   :alt: **Figure 1** Using kubectl to connect to multiple clusters

   **Figure 1** Using kubectl to connect to multiple clusters

Prerequisites
-------------

-  You have a Linux VM with the kubectl command line tool installed. The kubectl version must match the cluster version. For details, see `Install Tools <https://kubernetes.io/docs/tasks/tools/>`__.
-  The VM where kubectl is installed must be able to access the network of each cluster.

kubeconfig File Structure
-------------------------

kubeconfig is the configuration file of kubectl. You can download it on the cluster details page.

|image1|

The content of the kubeconfig file is as follows:

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

The preceding kubeconfig defines the private network address and public network address of the cluster as two clusters with two different contexts. You can switch the context to use different addresses to access the cluster.

.. _cce_bestpractice_00254__section673745134411:

Solution 1: Specify Different kubeconfig Files in Commands
----------------------------------------------------------

#. Log in to the VM where kubectl is installed.

#. Download the kubeconfig files of the two clusters to the **/home** directory on the kubectl client. The following names are taken as examples.

   ============ ====================
   Cluster Name kubeconfig File Name
   ============ ====================
   Cluster A    kubeconfig-a.json
   Cluster B    kubeconfig-b.json
   ============ ====================

#. Make kubectl access cluster A by default and move the **kubeconfig-a.json** file to **$HOME/.kube/config**.

   .. code-block::

      cd /home
      mkdir -p $HOME/.kube
      mv -f kubeconfig-a.json $HOME/.kube/config

#. Move the **kubeconfig-b.json** file of cluster B to **$HOME/.kube/config-test**.

   .. code-block::

      mv -f kubeconfig-b.json $HOME/.kube/config-test

   The name of the **config-test** file can be customized.

#. Add **--kubeconfig** to specify the credential used by the kubectl commands when accessing cluster B. (There is no need to add **--kubeconfig** when running kubectl commands to access cluster A, because kubectl can access cluster A by default.) For example, run the following command to check the nodes in cluster B:

   .. code-block::

      kubectl --kubeconfig=$HOME/.kube/config-test get node

   If you frequently use a long command, the preceding method can be inconvenient. To simplify the command, you can use aliases. For example:

   .. code-block::

      alias ka='kubectl --kubeconfig=$HOME/.kube/config'
      alias kb='kubectl --kubeconfig=$HOME/.kube/config-test'

   In the preceding information, **ka** and **kb** can be custom aliases. When running the kubectl command, you can directly enter **ka** or **kb** to replace **kubectl**. The **--kubeconfig** parameter is automatically added. For example, the command for checking nodes in cluster B can be simplified as follows:

   .. code-block::

      kb get node

.. _cce_bestpractice_00254__section1272762813465:

Solution 2: Combine the kubeconfig Files of the Two Clusters Together
---------------------------------------------------------------------

The following steps walk you through the procedure of modifying the kubeconfig files and accessing multiple clusters.

This example configures only the public network access to the clusters. If you want to access multiple clusters over private networks, retain the **clusters** field and ensure that the clusters can be accessed over private networks. Its configuration is similar to that described in this example.

#. Download the kubeconfig files of the two clusters and delete the lines related to private network access, as shown in the following figure.

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

#. Run the following command to copy the combined file to the kubectl configuration path:

   **mkdir -p $HOME/.kube**

   **mv -f kubeconfig.json $HOME/.kube/config**

#. Run the kubectl command to check whether the two clusters can be accessed.

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

   If you frequently use a long command, the preceding method can be inconvenient. To simplify the command, you can use aliases. For example:

   .. code-block::

      alias ka='kubectl config use-context Cluster-A-Context;kubectl'
      alias kb='kubectl config use-context Cluster-B-Context;kubectl'

   In the preceding information, **ka** and **kb** can be custom aliases. When running the kubectl command, you can directly enter **ka** or **kb** to replace **kubectl**. You need to switch the context and then run the kubectl command. For example:

   .. code-block::

      # ka cluster-info
      Switched to context "Cluster-A-Context".
      Kubernetes control plane is running at https://119.xxx.xxx.xxx:5443
      CoreDNS is running at https://119.xxx.xxx.xxx:5443/api/v1/namespaces/kube-system/services/coredns:dns/proxy

      To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

.. |image1| image:: /_static/images/en-us_image_0000001981434585.png
