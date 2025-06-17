:original_name: cce_bestpractice_0310.html

.. _cce_bestpractice_0310:

Installing the Migration Tool
=============================

Velero is an open source backup and migration tool for Kubernetes clusters. With restic's PV data backup capabilities, Velero can back up Kubernetes resource objects (such as Deployments, jobs, Services, and ConfigMaps) in the source cluster and data in PVs mounted to pods and uploaded them to object storage. If a disaster occurs or migration is required, the target cluster can obtain the corresponding backup data from the object storage using Velero and restore cluster resources as required.

According to :ref:`Migration Solution <cce_bestpractice_0307__section96147345128>`, prepare temporary object storage to store backup files before the migration. Velero supports OBS and `MinIO <https://min.io/>`__ as the object storage. The object storage requires sufficient storage space for storing backup files. You can estimate the storage space based on your cluster scale and data volume. OBS buckets are recommended for data backup. For details about how to deploy Velero, see :ref:`Installing Velero <cce_bestpractice_0310__section138392220432>`.

Prerequisites
-------------

-  The Kubernetes version of the source on-premises cluster must be 1.10 or later, and the cluster can use DNS and Internet services properly.
-  If you are using OBS to store backup files, you must obtain the AK/SK of a user who has the right to operate OBS. For details, see `Access Keys <https://docs.otc.t-systems.com/en-us/api/obs/obs_04_0116.html>`__.
-  If you are using MinIO to store backup files, you must bind an EIP to the server where MinIO is installed and enable the API and console port of MinIO in the security group.
-  The target CCE cluster has been created.
-  The source cluster and target cluster must each have at least one idle node. It is recommended that the node specifications be 4 vCPUs and 8 GiB memory or higher.

(Optional) Installing MinIO
---------------------------

MinIO is an open source, high-performance object storage tool compatible with the S3 API protocol. If MinIO is used to store backup files for cluster migration, you need a temporary server to deploy MinIO and provide services for external systems. If you are using OBS to store backup files, you can skip this section and go to :ref:`Installing Velero <cce_bestpractice_0310__section138392220432>`.

MinIO can be installed in any of the following locations:

-  A temporary ECS outside a cluster

   If the MinIO server is installed outside the cluster, backup files will not be affected when a catastrophic fault occurs in the cluster.

-  An idle node in a cluster

   You can remotely log in to a node and install MinIO or the containerized MinIO. For details, see `Velero official document <https://velero.io/docs/v1.13/contributions/minio/#set-up-server>`__.

   .. important::

      To containerize MinIO, do as follows:

      -  Change the storage type (emptyDir) in the YAML file provided by Velero to hostPath or local. Otherwise, backup files will be permanently lost after the containerized MinIO is restarted.
      -  Change the Service type to NodePort or use other types of Services that support public network access to ensure that MinIO can be accessed by external networks. Otherwise, backup files cannot be downloaded outside the cluster.

Regardless of which deployment method is used, the server where MinIO is installed must have **sufficient storage space**, an **EIP must be bound** to the server, and **the MinIO service port must be enabled in the security group**. Otherwise, backup files cannot be uploaded or downloaded.

In this example, MinIO is installed on a temporary ECS outside the cluster.

#. Download MinIO.

   .. code-block::

      mkdir /opt/minio
      mkdir /opt/miniodata
      cd /opt/minio
      wget https://dl.minio.io/server/minio/release/linux-amd64/minio
      chmod +x minio

#. .. _cce_bestpractice_0310__li126129251432:

   Configure the username and password of MinIO.

   The username and password configured using this method are temporary environment variables and must be reset after the service is restarted. Otherwise, the default root credential **minioadmin:minioadmin** will be used to create the service.

   .. code-block::

      export MINIO_ROOT_USER=minio
      export MINIO_ROOT_PASSWORD=minio123

#. Create a service. In the command, **/opt/miniodata/** indicates the local disk path for MinIO to store data.

   The default API port of MinIO is 9000, and the console port is randomly generated. You can use the **--console-address** parameter to specify a console port.

   .. code-block::

      ./minio server /opt/miniodata/ --console-address ":30840" &

   .. note::

      To access the object storage buckets, make sure to enable the API and console ports in the firewall and security group on the server with MinIO installed.

#. Use a browser to access http://*{EIP of the node where MinIO resides}*\ **:30840**. The MinIO console is displayed.

.. _cce_bestpractice_0310__section138392220432:

Installing Velero
-----------------

You need to first go to the OBS console or MinIO console and create a bucket named **velero** for storing backup files. The bucket name can be a custom one, which must be specified when installing Velero. Otherwise, the bucket cannot be accessed and the backup will fail. For details, see :ref:`5 <cce_bestpractice_0310__li1722825643415>`.

.. important::

   -  Velero instances need to be installed and deployed in both the **source and target clusters** and are used for backup and restoration, respectively. The installation procedures are the same.
   -  The master node of a CCE cluster does not support remote login ports. You can use kubectl to install Velero.
   -  If there are a large number of resources to back up, you are advised to adjust the CPU and memory resources of Velero and node-agent to 1 vCPU and 1 GiB of memory or higher. For details, see :ref:`Backup Tool Resources Are Insufficient <cce_bestpractice_0314__section321054511332>`.
   -  The object storage bucket for storing backup files must be **empty**.

Then, you can download the latest, stable binary file from https://github.com/vmware-tanzu/velero/releases. The following uses Velero 1.13.1 as an example. The process of installing Velero in the source cluster is identical to the target cluster.

#. Log in to a VM that can access the public network and use kubectl to access the cluster where Velero is to be installed.

#. Download the binary file of Velero 1.13.1.

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

   Replace the AK/SK in the file as required. If MinIO is used, the AK/SK are the username and password created in :ref:`2 <cce_bestpractice_0310__li126129251432>`.

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
        --backup-location-config region=eu-de,s3ForcePathStyle="true",s3Url=http://obs.eu-de.otc.t-systems.com

   .. table:: **Table 1** Installation parameters of Velero

      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                     |
      +===================================+=================================================================================================================================================================================================================================================================================================================+
      | --provider                        | AWS S3 component to be used                                                                                                                                                                                                                                                                                     |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --plugins                         | API component compatible with AWS S3. Both OBS and MinIO support the S3 protocol.                                                                                                                                                                                                                               |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --bucket                          | Name of the object storage bucket for storing backup files. The bucket must be created in advance.                                                                                                                                                                                                              |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --secret-file                     | Secret file for accessing the object storage, which is, the **credentials-velero** file created in :ref:`4 <cce_bestpractice_0310__li197871715322>`.                                                                                                                                                            |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --use-node-agent                  | Whether to enable PV data backup. You are advised to enable this function. Otherwise, storage volume resources cannot be backed up.                                                                                                                                                                             |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --use-volume-snapshots            | Whether to create a VolumeSnapshotLocation object for PV snapshot, which requires support from the snapshot program. Set this parameter to **false**.                                                                                                                                                           |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --backup-location-config          | OBS bucket configurations, including region, s3ForcePathStyle, and s3Url.                                                                                                                                                                                                                                       |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | region                            | Region to which object storage bucket belongs.                                                                                                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                                                                 |
      |                                   | -  If OBS is used, configure this parameter based on the actual situation, for example, **eu-de**.                                                                                                                                                                                                              |
      |                                   | -  If MinIO is used, set this parameter to **minio**.                                                                                                                                                                                                                                                           |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | s3ForcePathStyle                  | The value **true** indicates that the S3 file path format is used.                                                                                                                                                                                                                                              |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | s3Url                             | API access address of the object storage bucket.                                                                                                                                                                                                                                                                |
      |                                   |                                                                                                                                                                                                                                                                                                                 |
      |                                   | -  If OBS is used, set this parameter to **http://obs.**\ *{region}*\ **.otc.t-systems.com**. The value of this parameter is determined based on the region where the object storage bucket is located. For example, if the region is **eu-de**, the parameter value is **http://obs.eu-de.otc.t-systems.com**. |
      |                                   | -  If MinIO is used, set this parameter to **http://**\ *{EIP of the node where minio is located}*\ **:9000**. The value of this parameter is determined based on the IP address and port of the node where MinIO is installed.                                                                                 |
      |                                   |                                                                                                                                                                                                                                                                                                                 |
      |                                   |    .. note::                                                                                                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                 |
      |                                   |       -  The access port in s3Url must be set to the API port of MinIO instead of the console port. The default API port of MinIO is 9000.                                                                                                                                                                      |
      |                                   |       -  To access MinIO installed outside the cluster, enter the public IP address of MinIO.                                                                                                                                                                                                                   |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. View the pod status: (By default, a namespace named **velero** is created for the Velero instance.)

   .. code-block::

      $ kubectl get pod -n velero
      NAME                   READY   STATUS    RESTARTS   AGE
      node-agent-rn29c       1/1     Running   0          16s
      velero-c9ddd56-tkzpk   1/1     Running   0          16s

   .. note::

      To prevent memory insufficiency during backup in the actual production environment, you are advised to change the CPU and memory allocated to node-agent and Velero by referring to :ref:`Backup Tool Resources Are Insufficient <cce_bestpractice_0314__section321054511332>`.

#. Check the interconnection between Velero and the object storage and ensure that the status is **Available**.

   .. code-block::

      $ velero backup-location get
      NAME      PROVIDER   BUCKET/PREFIX   PHASE       LAST VALIDATED                  ACCESS MODE   DEFAULT
      default   aws        velero          Available   2021-10-22 15:21:12 +0800 CST   ReadWrite     true
