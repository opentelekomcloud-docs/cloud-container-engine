:original_name: cce_bestpractice_00199.html

.. _cce_bestpractice_00199:

Mounting an Object Storage Bucket of a Third-Party Tenant
=========================================================

This section describes how to mount OBS buckets and OBS parallel file systems (preferred) of third-party tenants.

Application Scenarios
---------------------

The CCE cluster of a SaaS service provider needs to be mounted with the OBS bucket of a third-party tenant, as shown in :ref:`Figure 1 <cce_bestpractice_00199__fig1315433183918>`.

.. _cce_bestpractice_00199__fig1315433183918:

.. figure:: /_static/images/en-us_image_0000001898024613.png
   :alt: **Figure 1** Mounting an OBS bucket of a third-party tenant

   **Figure 1** Mounting an OBS bucket of a third-party tenant

#. :ref:`The third-party tenant authorizes the SaaS service provider to access the OBS buckets or parallel file systems <cce_bestpractice_00199__section193471249193310>` by setting the bucket policy and bucket ACL.
#. :ref:`The SaaS service provider statically imports the OBS buckets and parallel file systems of the third-party tenant <cce_bestpractice_00199__en-us_topic_0196817407_section155006183017>`.
#. The SaaS service provider processes the service and writes the processing result (result file or result data) back to the OBS bucket of the third-party tenant.

Precautions
-----------

-  Only parallel file systems and OBS buckets of third-party tenants in the same region can be mounted.
-  Only clusters where the everest add-on of v1.1.11 or later has been installed (the cluster version must be v1.15 or later) can be mounted with OBS buckets of third-party tenants.
-  The service platform of the SaaS service provider needs to manage the lifecycle of the third-party bucket PVs. When a PVC is deleted separately, the PV is not deleted. Instead, it will be retained. To do so, call the native Kubernetes APIs to create and delete static PVs.

.. _cce_bestpractice_00199__section193471249193310:

Authorizing the SaaS Service Provider to Access the OBS Buckets
---------------------------------------------------------------

The following uses an OBS bucket as an example to describe how to set a bucket policy and bucket ACL to authorize the SaaS service provider. The configuration for an OBS parallel file system is the same.

#. Log in to the OBS console.
#. In the bucket list, click a bucket name and access the **Overview** page.

3. In the navigation pane, choose **Permissions** > **Bucket Policies**. On the displayed page, click **Create** to create a bucket policy.


   .. figure:: /_static/images/en-us_image_0000001851585700.png
      :alt: **Figure 2** Creating a bucket policy

      **Figure 2** Creating a bucket policy

   -  **Policy Mode**: Select **Customized**.
   -  **Effect**: Select **Allow**.
   -  **Principal**: Select **Include**, select **Cloud service user**, and enter the account ID and user ID. The bucket policy is applied to the specified user.
   -  **Resources**: Select the resources that can be operated.
   -  **Actions**: Select the actions that can be operated.

4. In the navigation pane, choose **Permissions** > **Bucket ACLs**. In the right pane, click **Add**. Enter the account ID or account name of the authorized user, select **Read** and **Write** for **Access to Bucket**, select **Read** and **Write** for **Access to ACL**, and click **OK**.

.. _cce_bestpractice_00199__en-us_topic_0196817407_section155006183017:

Statically Importing OBS Buckets and Parallel File Systems
----------------------------------------------------------

-  **Static PV of an OBS bucket:**

   .. code-block::

      apiVersion: v1
      kind: PersistentVolume
      metadata:
        name: objbucket      #Replace the name with the actual PV name of the bucket.
        annotations:
          pv.kubernetes.io/provisioned-by: everest-csi-provisioner
      spec:
        accessModes:
        - ReadWriteMany
        capacity:
          storage: 1Gi
        mountOptions:
        - default_acl=bucket-owner-full-control      #New OBS mounting parameters
        csi:
          driver: obs.csi.everest.io
          fsType: s3fs
          volumeAttributes:
            everest.io/obs-volume-type: STANDARD
            everest.io/region:   eu-de     #Set it to the ID of the current region.
            storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
          volumeHandle: objbucket             #Replace the name with the actual bucket name of the third-party tenant.
        persistentVolumeReclaimPolicy: Retain    #This parameter must be set to Retain to ensure that the bucket will not be deleted when a PV is deleted.
        storageClassName: csi-obs-mountoption    #You can associate a new custom OBS storage class or the built-in csi-obs of the cluster.

   -  **mountOptions**: This field contains the new OBS mounting parameters that allow the bucket owner to have full access to the data in the bucket. This field solves the problem that the bucket owner cannot read the data written into a mounted third-party bucket. If the object storage of a third-party tenant is mounted, **default_acl** must be set to **bucket-owner-full-control**. For details about other values of **default_acl**, see `Bucket ACLs and Object ACLs <https://docs.otc.t-systems.com/usermanual/obs/en-us_topic_0066088967.html>`__.
   -  **persistentVolumeReclaimPolicy**: When the object storage of a third-party tenant is mounted, this field must be set to **Retain**. In this way, the OBS bucket will not be deleted when a PV is deleted. The service platform of the SaaS service provider needs to manage the lifecycle of the third-party bucket PVs. When a PVC is deleted separately, the PV is not deleted. Instead, it will be retained. To do so, call the native Kubernetes APIs to create and delete static PVs.
   -  **storageClassName**: You can associate a new custom OBS storage class (:ref:`click here <cce_bestpractice_00199__li1235812419467>`) or the built-in csi-obs of the cluster.

   **PVC of a bound OBS bucket:**

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        annotations:
          csi.storage.k8s.io/fstype: obsfs
          everest.io/obs-volume-type: STANDARD
          volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
        name: objbucketpvc      #Replace the name with the actual PVC name of the bucket.
        namespace: default
      spec:
        accessModes:
        - ReadWriteMany
        resources:
          requests:
            storage: 1Gi
        storageClassName: csi-obs-mountoption     #The value must be the same as the storage class associated with the bound PV.
        volumeName: objbucket       #Replace the name with the actual PV name of the bucket to be bound.

-  **Static PV of an OBS parallel file system:**

   .. code-block::

      apiVersion: v1
      kind: PersistentVolume
      metadata:
        name: obsfscheck   #Replace the name with the actual PV name of the parallel file system.
        annotations:
          pv.kubernetes.io/provisioned-by: everest-csi-provisioner
      spec:
        accessModes:
        - ReadWriteMany
        capacity:
          storage: 1Gi
        mountOptions:
        - default_acl=bucket-owner-full-control     #New OBS mounting parameters
        csi:
          driver: obs.csi.everest.io
          fsType: obsfs
          volumeAttributes:
            everest.io/obs-volume-type: STANDARD
            everest.io/region:   eu-de
            storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
          volumeHandle: obsfscheck               #Replace the name with the actual name of the parallel file system of the third-party tenant.
        persistentVolumeReclaimPolicy: Retain        #This parameter must be set to Retain to ensure that the bucket will not be deleted when a PV is deleted.
        storageClassName: csi-obs-mountoption       #You can associate a new custom OBS storage class or the built-in csi-obs of the cluster.

   -  **mountOptions**: This field contains the new OBS mounting parameters that allow the bucket owner to have full access to the data in the bucket. This field solves the problem that the bucket owner cannot read the data written into a mounted third-party bucket. If the object storage of a third-party tenant is mounted, **default_acl** must be set to **bucket-owner-full-control**. For details about other values of **default_acl**, see `Bucket ACLs and Object ACLs <https://docs.otc.t-systems.com/usermanual/obs/en-us_topic_0066088967.html>`__.
   -  **persistentVolumeReclaimPolicy**: When the object storage of a third-party tenant is mounted, this field must be set to **Retain**. In this way, the OBS bucket will not be deleted when a PV is deleted. The service platform of the SaaS service provider needs to manage the lifecycle of the third-party bucket PVs. When a PVC is deleted separately, the PV is not deleted. Instead, it will be retained. To do so, call the native Kubernetes APIs to create and delete static PVs.
   -  **storageClassName**: You can associate a new custom OBS storage class (:ref:`click here <cce_bestpractice_00199__li1235812419467>`) or the built-in csi-obs of the cluster.

   PVC of a bound OBS parallel file system:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        annotations:
          csi.storage.k8s.io/fstype: obsfs
          everest.io/obs-volume-type: STANDARD
          volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
        name: obsfscheckpvc   #Replace the name with the actual PVC name of the parallel file system.
        namespace: default
      spec:
        accessModes:
        - ReadWriteMany
        resources:
          requests:
            storage: 1Gi
        storageClassName: csi-obs-mountoption    #The value must be the same as the storage class associated with the bound PV.
        volumeName: obsfscheck     #Replace the name with the actual PV name of the parallel file system.

-  .. _cce_bestpractice_00199__li1235812419467:

   **(Optional) Creating a custom OBS storage class to associate with a static PV:**

   .. code-block::

      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: csi-obs-mountoption
      mountOptions:
        - default_acl=bucket-owner-full-control
      parameters:
        csi.storage.k8s.io/csi-driver-name: obs.csi.everest.io
        csi.storage.k8s.io/fstype: obsfs
        everest.io/obs-volume-type: STANDARD
      provisioner: everest-csi-provisioner
      reclaimPolicy: Retain
      volumeBindingMode: Immediate

   -  **csi.storage.k8s.io/fstype**: File type. The value can be **obsfs** or **s3fs**. If the value is **s3fs**, an OBS bucket is created and mounted using s3fs. If the value is **obsfs**, an OBS parallel file system is created and mounted using obsfs.
   -  **reclaimPolicy**: Reclaim policy of a PV. The value will be set in **PV.spec.persistentVolumeReclaimPolicy** dynamically created based on the new PVC associated with the storage class. If the value is **Delete**, the external OBS bucket and the PV will be deleted when the PVC is deleted. If the value is **Retain**, the PV and external storage are retained when the PVC is deleted. In this case, clear the PV separately. In the scenario where an imported third-party bucket is associated, the storage class is used only for associating static PVs (with this field set to **Retain**). Dynamic creation is not involved.
