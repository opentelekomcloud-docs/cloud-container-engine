:original_name: cce_10_1080.html

.. _cce_10_1080:

Rotating Cluster Certificates
=============================

A Kubernetes cluster relies on multiple certificate chains and credentials. Proper lifecycle management, including timely rotation, is critical to cluster security and workload integrity. When a cluster is created, CCE automatically generates certificates to secure communication among control plane components (kube-apiserver, etcd, kube-controller-manager, kube-scheduler) and cluster nodes. You may also provide custom certificates for agent authentication and kube-apiserver aggregation layer configuration. If certificates expire without rotation, cluster functionality may fail, affecting running workloads.

CCE cluster certificates are valid for five years by default. To prevent service interruptions, rotate certificates before expiry. CCE supports manual certificate rotation via the console or API. CCE sends a reminder six months before certificate expiry, displays an expiration alert on the console, and notifies you through Message Center. These mechanisms ensure timely action to maintain continuous and secure cluster operations.

Precautions
-----------

-  Certificate rotation is only available for clusters v1.19 or later. If your cluster is earlier than v1.19, upgrade the cluster first.
-  kube-apiserver access is briefly interrupted during each rotation. This may affect the workloads that depend on kube-apiserver. You can schedule the rotations during off-peak hours to reduce service disruptions.
-  Kubernetes components on cluster nodes restart during certificate rotation. If CCE Node Problem Detector is installed, you may receive transient exception alerts. These do not indicate actual faults.
-  Do not modify the cluster during certificate rotation.
-  Rotation duration depends on cluster size. Typically, it takes 10 to 15 minutes. Upon successful rotation, certificates are valid for five years.
-  If a cluster certificate has expired, contact technical support.

.. _cce_10_1080__section16903151163610:

Rotating System-Generated Certificates
--------------------------------------

Before a certificate expires, the **Update Certificate** button is displayed in the cluster list or on the cluster overview page of the CCE console.

-  If a cluster certificate is about to expire in 30 days, a certificate expiration alert is displayed when you access the CCE console, and the alert is also displayed in the cluster list. If you click the cluster name to view the cluster details, the cluster certificate expiration alert is also displayed on the cluster overview page.
-  If the cluster certificate is about to expire in 180 days, the cluster overview page displays an alert when you click the cluster name to view the cluster details.

Click **Update Certificate**. Carefully review the risk warnings and complete the certificate rotation as prompted.

You are unaware of the master node certificates. The following table lists the certificates to be rotated on worker nodes.

+----------------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------+
| Certificate                | Certificate Path                       | Certificate Validity Period                                                                                                       |
+============================+========================================+===================================================================================================================================+
| kube-node-tls.crt          | /var/paas/srv/kubernetes               | The initial certificate is valid for five years. After the certificate is rotated, the validity period is extended by five years. |
|                            |                                        |                                                                                                                                   |
| kube-node-tls.key          |                                        |                                                                                                                                   |
+----------------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------+
| kube-proxy-tls.crt         | /var/paas/srv/kubernetes               | The initial certificate is valid for five years. After the certificate is rotated, the validity period is extended by five years. |
|                            |                                        |                                                                                                                                   |
| kube-proxy-tls.key         |                                        |                                                                                                                                   |
+----------------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------+
| kubelet-client-current.pem | /opt/cloud/cce/kubernetes/kubelet/pki/ | The certificate is automatically rotated before expiration. The validity period is 1 year.                                        |
|                            |                                        |                                                                                                                                   |
| kubelet-server-current.pem |                                        |                                                                                                                                   |
+----------------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------+
| kubecfg.crt                | /var/paas/srv/kubernetes               | The initial certificate is valid for five years. After the certificate is rotated, the validity period is extended by five years. |
|                            |                                        |                                                                                                                                   |
| kubecfg_crypto.key         |                                        |                                                                                                                                   |
+----------------------------+----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------+

Rotating Self-Managed Certificates
----------------------------------

If you provide a self-managed certificate during cluster creation, **Update Certificate** appears in the cluster list or on the CCE cluster overview page before expiry. Alert behavior is identical to that for :ref:`system-generated certificates <cce_10_1080__section16903151163610>`. To complete rotation, upload the CA root certificate, client certificate, and client private key as prompted.

Rotating Certificates During a Cluster Upgrade
----------------------------------------------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. In the navigation pane, choose **Cluster Upgrade**.
#. Specify a cluster upgrade path and back up data. CCE then performs a pre-upgrade check.
#. During upgrade configuration, specify cluster certificate settings and choose whether to rotate certificates synchronously with the upgrade.

   -  **Rotate CCE Cluster Certificate**: You can choose whether to rotate a CCE cluster certificate during a cluster upgrade based on the remaining validity of these certificates. Upon successful rotation, certificates are valid for five years.

      .. caution::

         If **OIDC Provider** is enabled (check status in **Connection Information** on the **Cluster Overview** page), you must retrieve the cluster signing public key after upgrade. Failure to do so invalidates OIDC identity authentication.

   -  **Rotate Self-Managed Certificate**: If you provided a self-managed certificate during cluster creation, you can choose whether to rotate it during upgrade. To complete rotation, upload the CA root certificate, client certificate, and client private key as prompted.

#. Click **Upgrade**.

Checking the Certificate Expiration Date
----------------------------------------

You can query the certificate expiration date of a cluster through an API..

The API for reading a specified cluster is used as an example to describe how to check the certificate expiration date. The URI of the API is as follows:

**GET /api/v3/projects/{project_id}/clusters/{cluster_id}**

========== ===========
Parameter  Description
========== ===========
project_id Project ID.
cluster_id Cluster ID.
========== ===========

The following is an example of the request body for reading a specified cluster:

|image1|

.. |image1| image:: /_static/images/en-us_image_0000002631690076.png
