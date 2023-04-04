:original_name: cce_10_0025.html

.. _cce_10_0025:

CCE Operations Supported by CTS
===============================

.. table:: **Table 1** CCE operations supported by CTS

   +--------------------------------------------------------+---------------------+------------------------------+
   | Operation                                              | Resource Type       | Event Name                   |
   +========================================================+=====================+==============================+
   | Creating an agency                                     | Cluster             | createUserAgencies           |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a cluster                                     | Cluster             | createCluster                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Updating the description of a cluster                  | Cluster             | updateCluster                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Upgrading a cluster                                    | Cluster             | clusterUpgrade               |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a cluster                                     | Cluster             | claimCluster/deleteCluster   |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Downloading a cluster certificate                      | Cluster             | getClusterCertByUID          |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Binding and unbinding an EIP                           | Cluster             | operateMasterEIP             |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Waking up a cluster and resetting node management (V2) | Cluster             | operateCluster               |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Hibernating a cluster (V3)                             | Cluster             | hibernateCluster             |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Waking up a cluster (V3)                               | Cluster             | awakeCluster                 |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Changing the specifications of a cluster               | Cluster             | resizeCluster                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Modifying configurations of a cluster                  | Cluster             | updateConfiguration          |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a node pool                                   | Node pool           | createNodePool               |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Updating a node pool                                   | Node pool           | updateNodePool               |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a node pool                                   | Node pool           | claimNodePool                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Migrating a node pool                                  | Node pool           | migrateNodepool              |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Modifying node pool configurations                     | Node pool           | updateConfiguration          |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a node                                        | Node                | createNode                   |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting all the nodes from a specified cluster        | Node                | deleteAllHosts               |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a single node                                 | Node                | deleteOneHost/claimOneHost   |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Updating the description of a node                     | Node                | updateNode                   |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating an add-on instance                            | Add-on instance     | createAddonInstance          |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting an add-on instance                            | Add-on instance     | deleteAddonInstance          |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Uploading a chart                                      | Chart               | uploadChart                  |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Updating a chart                                       | Chart               | updateChart                  |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a chart                                       | Chart               | deleteChart                  |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a release                                     | Release             | createRelease                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Upgrading a release                                    | Release             | updateRelease                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a release                                     | Release             | deleteRelease                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a ConfigMap                                   | Kubernetes resource | createConfigmaps             |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a DaemonSet                                   | Kubernetes resource | createDaemonsets             |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a Deployment                                  | Kubernetes resource | createDeployments            |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating an event                                      | Kubernetes resource | createEvents                 |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating an Ingress                                    | Kubernetes resource | createIngresses              |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a job                                         | Kubernetes resource | createJobs                   |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a namespace                                   | Kubernetes resource | createNamespaces             |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a node                                        | Kubernetes resource | createNodes                  |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a PersistentVolumeClaim                       | Kubernetes resource | createPersistentvolumeclaims |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a pod                                         | Kubernetes resource | createPods                   |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a replica set                                 | Kubernetes resource | createReplicasets            |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a resource quota                              | Kubernetes resource | createResourcequotas         |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a secret                                      | Kubernetes resource | createSecrets                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a service                                     | Kubernetes resource | createServices               |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a StatefulSet                                 | Kubernetes resource | createStatefulsets           |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a volume                                      | Kubernetes resource | createVolumes                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a ConfigMap                                   | Kubernetes resource | deleteConfigmaps             |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a DaemonSet                                   | Kubernetes resource | deleteDaemonsets             |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a Deployment                                  | Kubernetes resource | deleteDeployments            |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting an event                                      | Kubernetes resource | deleteEvents                 |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting an Ingress                                    | Kubernetes resource | deleteIngresses              |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a job                                         | Kubernetes resource | deleteJobs                   |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a namespace                                   | Kubernetes resource | deleteNamespaces             |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a node                                        | Kubernetes resource | deleteNodes                  |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a Pod                                         | Kubernetes resource | deletePods                   |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a replica set                                 | Kubernetes resource | deleteReplicasets            |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a resource quota                              | Kubernetes resource | deleteResourcequotas         |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a secret                                      | Kubernetes resource | deleteSecrets                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a service                                     | Kubernetes resource | deleteServices               |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a StatefulSet                                 | Kubernetes resource | deleteStatefulsets           |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting volumes                                       | Kubernetes resource | deleteVolumes                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified ConfigMap                        | Kubernetes resource | updateConfigmaps             |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified DaemonSet                        | Kubernetes resource | updateDaemonsets             |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified Deployment                       | Kubernetes resource | updateDeployments            |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified event                            | Kubernetes resource | updateEvents                 |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified ingress                          | Kubernetes resource | updateIngresses              |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified job                              | Kubernetes resource | updateJobs                   |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified namespace                        | Kubernetes resource | updateNamespaces             |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified node                             | Kubernetes resource | updateNodes                  |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified PersistentVolumeClaim            | Kubernetes resource | updatePersistentvolumeclaims |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified pod                              | Kubernetes resource | updatePods                   |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified replica set                      | Kubernetes resource | updateReplicasets            |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified resource quota                   | Kubernetes resource | updateResourcequotas         |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified secret                           | Kubernetes resource | updateSecrets                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified service                          | Kubernetes resource | updateServices               |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing a specified StatefulSet                      | Kubernetes resource | updateStatefulsets           |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Replacing the specified status                         | Kubernetes resource | updateStatus                 |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Uploading a chart                                      | Kubernetes resource | uploadChart                  |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Updating a component template                          | Kubernetes resource | updateChart                  |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a chart                                       | Kubernetes resource | deleteChart                  |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Creating a template application                        | Kubernetes resource | createRelease                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Updating a template application                        | Kubernetes resource | updateRelease                |
   +--------------------------------------------------------+---------------------+------------------------------+
   | Deleting a template application                        | Kubernetes resource | deleteRelease                |
   +--------------------------------------------------------+---------------------+------------------------------+
