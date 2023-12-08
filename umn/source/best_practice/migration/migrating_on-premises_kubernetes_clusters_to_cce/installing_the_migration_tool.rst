:original_name: cce_bestpractice_0310.html

.. _cce_bestpractice_0310:

Installing the Migration Tool
=============================

Velero is an open-source backup and migration tool for Kubernetes clusters. It integrates the persistent volume (PV) data backup capability of the Restic tool and can be used to back up Kubernetes resource objects (such as Deployments, jobs, Services, and ConfigMaps) in the source cluster. Data in the PV mounted to the pod is backed up and uploaded to the object storage. When a disaster occurs or migration is required, the target cluster can use Velero to obtain the corresponding backup data from OBS and restore cluster resources as required.

According to :ref:`Migration Solution <cce_bestpractice_0307__section96147345128>`, prepare temporary object storage to store backup files before the migration. Velero supports OSB or `MinIO <https://min.io/>`__ as the object storage. OBS requires sufficient storage space for storing backup files. You can estimate the storage space based on your cluster scale and data volume. You are advised to use OBS for backup. For details about how to deploy Velero, see :ref:`Installing Velero <cce_bestpractice_0310__section138392220432>`.

Prerequisites
-------------

-  The Kubernetes version of the source on-premises cluster must be 1.10 or later, and the cluster can use DNS and Internet services properly.
-  If you use OBS to store backup files, obtain the AK/SK of a user who has the right to operate OBS. For details, see `Access Keys <https://docs.otc.t-systems.com/en-us/api/obs/obs_04_0116.html>`__.
-  If you use MinIO to store backup files, bind an EIP to the server where MinIO is installed and enable the API and console port of MinIO in the security group.
-  The target CCE cluster has been created.
-  The source cluster and target cluster must each have at least one idle node. It is recommended that the node specifications be 4 vCPUs and 8 GiB memory or higher.

Installing MinIO
----------------

MinIO is an open-source, high-performance object storage tool compatible with the S3 API protocol. If MinIO is used to store backup files for cluster migration, you need a temporary server to deploy MinIO and provide services for external systems. If you use OBS to store backup files, skip this section and go to :ref:`Installing Velero <cce_bestpractice_0310__section138392220432>`.

MinIO can be installed in any of the following locations:

-  Temporary ECS outside the cluster

   If the MinIO server is installed outside the cluster, backup files will not be affected when a catastrophic fault occurs in the cluster.

-  Idle nodes in the cluster

   You can remotely log in to a node to install the MinIO server or install MinIO in a container. For details, see the official Velero documentation at https://velero.io/docs/v1.7/contributions/minio/#set-up-server.

   .. important::

      For example, to install MinIO in a container, run the following command:

      -  The storage type in the YAML file provided by Velero is **emptyDir**. You are advised to change the storage type to **HostPath** or **Local**. Otherwise, backup files will be permanently lost after the container is restarted.
      -  Ensure that the MinIO service is accessible externally. Otherwise, backup files cannot be downloaded outside the cluster. You can change the Service type to NodePort or use other types of public network access Services.

Regardless of which deployment method is used, the server where MinIO is installed must have sufficient storage space, an EIP must be bound to the server, and the MinIO service port must be enabled in the security group. Otherwise, backup files cannot be uploaded or downloaded.

In this example, MinIO is installed on a temporary ECS outside the cluster.

#. Download MinIO.

   .. code-block::

      mkdir /opt/minio
      mkdir /opt/miniodata
      cd /opt/minio
      wget https://dl.minio.io/server/minio/release/linux-amd64/minio
      chmod +x minio

#. .. _cce_bestpractice_0310__li126129251432:

   Set the username and password of MinIO.

   The username and password set using this method are temporary environment variables and must be reset after the service is restarted. Otherwise, the default root credential **minioadmin:minioadmin** will be used to create the service.

   .. code-block::

      export MINIO_ROOT_USER=minio
      export MINIO_ROOT_PASSWORD=minio123

#. Create a service. In the command, **/opt/miniodata/** indicates the local disk path for MinIO to store data.

   The default API port of MinIO is 9000, and the console port is randomly generated. You can use the **--console-address** parameter to specify a console port.

   .. code-block::

      ./minio server /opt/miniodata/ --console-address ":30840" &

   .. note::

      Enable the API and console ports in the firewall and security group on the server where MinIO is to be installed. Otherwise, access to the object bucket will fail.

#. Use a browser to access http://{*EIP of the node where MinIO resides*}:30840. The MinIO console page is displayed.

.. _cce_bestpractice_0310__section138392220432:

Installing Velero
-----------------

Go to the OBS console or MinIO console and create a bucket named **velero** to store backup files. You can custom the bucket name, which must be used when installing Velero. Otherwise, the bucket cannot be accessed and the backup fails. For details, see :ref:`4 <cce_bestpractice_0310__li1722825643415>`.

.. important::

   -  Velero instances need to be installed and deployed in both the **source and target clusters**. The installation procedures are the same, which are used for backup and restoration, respectively.
   -  The master node of a CCE cluster does not provide a port for remote login. You can install Velero using kubectl.
   -  If there are a large number of resources to back up, you are advised to adjust the CPU and memory resources of Velero and Restic to 1 vCPU and 1 GiB memory or higher. For details, see :ref:`Backup Tool Resources Are Insufficient <cce_bestpractice_0314__section321054511332>`.
   -  The object storage bucket for storing backup files must be **empty**.

Download the latest, stable binary file from https://github.com/vmware-tanzu/velero/releases. This section uses Velero 1.7.0 as an example. The installation process in the source cluster is the same as that in the target cluster.

#. Download the binary file of Velero 1.7.0.

   .. code-block::

      wget https://github.com/vmware-tanzu/velero/releases/download/v1.7.0/velero-v1.7.0-linux-amd64.tar.gz

#. Install the Velero client.

   .. code-block::

      tar -xvf velero-v1.7.0-linux-amd64.tar.gz
      cp ./velero-v1.7.0-linux-amd64/velero /usr/local/bin

#. .. _cce_bestpractice_0310__li197871715322:

   Create the access key file **credentials-velero** for the backup object storage.

   .. code-block::

      vim credentials-velero

   Replace the AK/SK in the file based on the site requirements. If MinIO is used, the AK/SK are the username and password created in :ref:`2 <cce_bestpractice_0310__li126129251432>`.

   .. code-block::

      [default]
      aws_access_key_id = {AK}
      aws_secret_access_key = {SK}

#. .. _cce_bestpractice_0310__li1722825643415:

   Deploy the Velero server. Change the value of **--bucket** to the name of the created object storage bucket. In this example, the bucket name is **velero**. For more information about custom installation parameters, see `Customize Velero Install <https://velero.io/docs/v1.7/customize-installation/>`__.

   .. code-block::

      velero install \
        --provider aws \
        --plugins velero/velero-plugin-for-aws:v1.2.1 \
        --bucket velero \
        --secret-file ./credentials-velero \
        --use-restic \
        --use-volume-snapshots=false \
        --backup-location-config region=eu-de,s3ForcePathStyle="true",s3Url=http://obs.eu-de.otc.t-systems.com

   .. table:: **Table 1** Installation parameters of Velero

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                            |
      +===================================+========================================================================================================================================================================================================================================================================+
      | --provider                        | Vendor who provides the plug-in.                                                                                                                                                                                                                                       |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --plugins                         | API component compatible with AWS S3. Both OBS and MinIO support the S3 protocol.                                                                                                                                                                                      |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --bucket                          | Name of the object storage bucket for storing backup files. The bucket must be created in advance.                                                                                                                                                                     |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --secret-file                     | Secret file for accessing the object storage, that is, the **credentials-velero** file created in :ref:`3 <cce_bestpractice_0310__li197871715322>`.                                                                                                                    |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --use-restic                      | Whether to use Restic to support PV data backup. You are advised to enable this function. Otherwise, storage volume resources cannot be backed up.                                                                                                                     |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --use-volume-snapshots            | Whether to create the VolumeSnapshotLocation object for PV snapshot, which requires support from the snapshot program. Set this parameter to **false**.                                                                                                                |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | --backup-location-config          | OBS bucket configurations, including region, s3ForcePathStyle, and s3Url.                                                                                                                                                                                              |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | region                            | Region to which object storage bucket belongs.                                                                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                        |
      |                                   | -  If OBS is used, set this parameter according to your region, for example, **eu-de**.                                                                                                                                                                                |
      |                                   | -  If MinIO is used, set this parameter to **minio**.                                                                                                                                                                                                                  |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | s3ForcePathStyle                  | The value **true** indicates that the S3 file path format is used.                                                                                                                                                                                                     |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | s3Url                             | API access address of the object storage bucket.                                                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                        |
      |                                   | -  If OBS is used, set this parameter to **http://obs.{region}.otc.t-systems.com** (*region* indicates the region where the object storage bucket is located). For example, if the region is **eu-de**, the parameter value is **http://obs.eu-de.otc.t-systems.com**. |
      |                                   | -  If MinIO is used, set this parameter to **http://{EIP of the node where minio is located}:9000**. The value of this parameter is determined based on the IP address and port of the node where MinIO is installed.                                                  |
      |                                   |                                                                                                                                                                                                                                                                        |
      |                                   |    .. note::                                                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                        |
      |                                   |       -  The access port in s3Url must be set to the API port of MinIO instead of the console port. The default API port of MinIO is 9000.                                                                                                                             |
      |                                   |       -  To access MinIO installed outside the cluster, enter the public IP address of MinIO.                                                                                                                                                                          |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. By default, a namespace named **velero** is created for the Velero instance. Run the following command to view the pod status:

   .. code-block::

      $ kubectl get pod -n velero
      NAME                   READY   STATUS    RESTARTS   AGE
      restic-rn29c           1/1     Running   0          16s
      velero-c9ddd56-tkzpk   1/1     Running   0          16s

   .. note::

      To prevent memory insufficiency during backup in the actual production environment, you are advised to change the CPU and memory allocated to Restic and Velero by referring to :ref:`Backup Tool Resources Are Insufficient <cce_bestpractice_0314__section321054511332>`.

#. Check the interconnection between Velero and the object storage and ensure that the status is **Available**.

   .. code-block::

      $ velero backup-location get
      NAME      PROVIDER   BUCKET/PREFIX   PHASE       LAST VALIDATED                  ACCESS MODE   DEFAULT
      default   aws        velero          Available   2021-10-22 15:21:12 +0800 CST   ReadWrite     true
