:original_name: cce_bestpractice_0310.html

.. _cce_bestpractice_0310:

Installing the Migration Tool
=============================

Velero is an open-source backup and migration tool for Kubernetes clusters. With restic's PV data backup capabilities, Velero can back up Kubernetes resource objects (such as Deployments, jobs, Services, and ConfigMaps) in the source cluster and data in the PVs mounted to pods and uploaded them to object storage. If a disaster occurs or migration is required, the target cluster can obtain the corresponding backup data from the object storage using Velero and restore cluster resources as required.

According to :ref:`Migration Solution <cce_bestpractice_0307__section96147345128>`, you will need to prepare temporary object storage to store backup files before the migration. Velero supports OBS.

Prerequisites
-------------

-  The Kubernetes version of the source on-premises cluster must be 1.10 or later, and the cluster can use DNS and Internet services properly.
-  If you are using OBS to store backup files, you must obtain the AK/SK of a user who can operate OBS. For details, see `Obtaining Access Keys (AK/SK) <https://docs.otc.t-systems.com/en-us/api/obs/obs_04_0116.html>`__. Temporary AKs or SKs cannot be used.
-  The target CCE cluster has been created.
-  The source cluster and target cluster must each have at least one idle node. It is recommended that the node specifications be 4 vCPUs and 8 GiB memory or higher.

Installing Velero
-----------------

You need to first go to the OBS console and create a bucket named **velero** for storing backup files. The bucket name can be a custom one, which must be specified when you install Velero. Otherwise, the bucket cannot be accessed and the backup will fail. For details, see :ref:`5 <cce_bestpractice_0310__li1722825643415>`.

.. important::

   -  Velero instances need to be installed and deployed in both the **source and target clusters** and are used for backup and restoration, respectively. The installation procedures are the same.
   -  The master node of a CCE cluster does not support remote login ports. You can use kubectl to install Velero.
   -  If there are a large number of resources to back up, you are advised to adjust the CPU and memory resources of Velero and node-agent to 1 vCPU and 1 GiB of memory or higher. For details, see :ref:`Backup Tool Resources Are Insufficient <cce_bestpractice_0314__section321054511332>`.
   -  The object storage bucket for storing backup files must be **empty**.

Then, you can download the latest, stable binary file from https://github.com/vmware-tanzu/velero/releases. The following uses Velero 1.13.1 as an example. The process of installing Velero in the source cluster is identical to the target cluster.

#. Log in to a VM that can access the public network and use kubectl to access the cluster where Velero is to be installed.

#. Download the binary file of `Velero 1.13.1 <https://github.com/vmware-tanzu/velero/releases/download/v1.13.1/velero-v1.13.1-linux-amd64.tar.gz>`__.

   .. code-block::

      wget https://github.com/vmware-tanzu/velero/releases/download/v1.13.1/velero-v1.13.1-linux-amd64.tar.gz

#. Install the Velero client.

   .. code-block::

      tar -xvf velero-v1.13.1-linux-amd64.tar.gz
      cp ./velero-v1.13.1-linux-amd64/velero /usr/local/bin

#. .. _cce_bestpractice_0310__li197871715322:

   Create the access key file **credentials-velero** for the backup object storage.

   .. code-block::

      vim credentials-velero

   Replace the AK/SK in the file as required.

   .. code-block::

      [default]
      aws_access_key_id = {AK}
      aws_secret_access_key = {SK}

#. .. _cce_bestpractice_0310__li1722825643415:

   Deploy the Velero server. Change the value of **--bucket** to the name of the created object storage bucket. In this example, the bucket name is **velero**. For more information about custom installation parameters, see `Customize Velero Install <https://velero.io/docs/v1.13/customize-installation/>`__.

   .. code-block::

      velero install \
        --provider aws \
        --plugins velero/velero-plugin-for-aws:v1.9.1 \
        --bucket velero \
        --secret-file ./credentials-velero \
        --use-node-agent \
        --use-volume-snapshots=false \
        --backup-location-config region=eu-de,s3ForcePathStyle="false",s3Url=http://obs.eu-de.otc.t-systems.com

   .. table:: **Table 1** Installation parameters of Velero

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                       |
      +===================================+===================================================================================================================================================================================================================================================================+
      | --provider                        | Specify the plugin type, which is AWS S3. OBS used in this section supports the AWS S3 protocol.                                                                                                                                                                  |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --plugins                         | Use an API component that is compatible with AWS S3. OBS used in this section supports the S3 protocol.                                                                                                                                                           |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --bucket                          | Name of the object storage bucket for storing backup files. The bucket must be created in advance.                                                                                                                                                                |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --secret-file                     | Use the secret file for accessing the object storage, which is, the **credentials-velero** file created in :ref:`4 <cce_bestpractice_0310__li197871715322>`.                                                                                                      |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --use-node-agent                  | Specify whether to enable PV data backup. You are advised to enable this function. Otherwise, storage volume resources cannot be backed up.                                                                                                                       |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --use-volume-snapshots            | Specify whether to create a VolumeSnapshotLocation object for PV snapshot, which requires support from the snapshot program. Set this parameter to **false**.                                                                                                     |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --backup-location-config          | Specify the OBS bucket configurations, including **region**, **s3ForcePathStyle**, **s3Url**, and more.                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                   |
      |                                   | -  **region**: the region where the OBS bucket is located.                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                   |
      |                                   |    -  Configure this parameter based on the actual region, for example, **eu-de**.                                                                                                                                                                                |
      |                                   |                                                                                                                                                                                                                                                                   |
      |                                   | -  **s3ForcePathStyle**: If this parameter is set to **false**, a bucket domain name in the virtual-hosted-style is used. The bucket name is directly embedded in the access domain name, for example, *{bucket-name}*\ **.obs.**\ *{region}.{domain}*\ **.com**. |
      |                                   | -  **s3Url**: API access address of the OBS bucket.                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                   |
      |                                   |    -  The value is in the format of **http://obs.**\ *{region}*\ **otc.t-systems.com**. It is determined by the region where the OBS bucket is located. For example, if the region is **eu-de**, the parameter value is **http://obs.eu-de.otc.t-systems.com**.   |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. View the pod status. By default, a namespace named **velero** is created for the Velero instance.

   .. code-block::

      kubectl get pod -n velero

   Information similar to the following is displayed:

   .. code-block::

      NAME                   READY   STATUS    RESTARTS   AGE
      node-agent-rn29c       1/1     Running   0          16s
      velero-c9ddd56-tkzpk   1/1     Running   0          16s

   .. note::

      To prevent memory insufficiency during backup in the actual production environment, you are advised to change the CPU and memory allocated to node-agent and Velero by referring to :ref:`Backup Tool Resources Are Insufficient <cce_bestpractice_0314__section321054511332>`.

#. Check the interconnection between Velero and OBS and ensure that the status is **Available**.

   .. code-block::

      velero backup-location get

   Information similar to the following is displayed:

   .. code-block::

      NAME      PROVIDER   BUCKET/PREFIX   PHASE       LAST VALIDATED                  ACCESS MODE   DEFAULT
      default   ***        velero          Available   2025-06-04 17:40:55 +0800 CST   ReadWrite     true

Uninstalling Velero
-------------------

To completely uninstall Velero from a cluster, run the following command to delete all resources created by Velero:

.. code-block::

   velero uninstall
