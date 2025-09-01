:original_name: cce_bestpractice_00199.html

.. _cce_bestpractice_00199:

Mounting Object Storage Across Accounts
=======================================

Application Scenarios
---------------------

-  Cross-account data sharing. For example, multiple teams within a company need to share data, but each team uses a different account.
-  Cross-account data migration and backup. When account A is about to be disabled, all data stored in the account needs to be transferred to a new account (account B).
-  Data processing and analysis. For example, account B is an external data processor and needs to access raw data from account A to perform tasks such as big data analysis and machine learning.

By linking object storage across accounts, you can share data, lower storage and transmission expenses, and guarantee data security and consistency. This enables various teams or organizations to securely and conveniently access each other's data resources, eliminating the need for repeated storage and redundant transmission. Additionally, data is kept current and compliant, enhancing overall service efficiency and security.

Procedure
---------

Assume that account B needs to access and use an OBS bucket of account A. For details, see :ref:`Figure 1 <cce_bestpractice_00199__fig7817125304714>` and :ref:`Table 1 <cce_bestpractice_00199__table1598613155469>`.

.. _cce_bestpractice_00199__fig7817125304714:

.. figure:: /_static/images/en-us_image_0000002218818310.png
   :alt: **Figure 1** Mounting an OBS bucket across accounts

   **Figure 1** Mounting an OBS bucket across accounts

.. _cce_bestpractice_00199__table1598613155469:

.. table:: **Table 1** Process description

   +------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | Procedure                                                                                                  | Description                                                                                                                          |
   +============================================================================================================+======================================================================================================================================+
   | :ref:`Step 1: Create an OBS Bucket Policy and ACL <cce_bestpractice_00199__section1171195014615>`          | Configure an OBS bucket policy and ACL using account A and grant account B required permissions like the read and write permissions. |
   +------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Step 2: Create a Workload with an OBS Volume Mounted <cce_bestpractice_00199__section1168445316142>` | Create a PV and a PVC based on the OBS bucket of account A using account B and mount the PVC to the required workload.               |
   +------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Step 3: Check the Pod Actions on the OBS Bucket <cce_bestpractice_00199__section14892114864113>`     | Check whether the pod created by account B has the required permissions based on the bucket policy.                                  |
   +------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Step 4: Clear Resources <cce_bestpractice_00199__section2860455112310>`                              | Once you have studied this example, delete any associated resources to prevent incurring settlement fees.                            |
   +------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+

Prerequisites
-------------

-  The involved accounts are in the same region.
-  You have created a cluster where the CCE Container Storage (Everest) add-on is installed. The add version must be 1.1.11 or later, and the cluster version must be 1.15 or later.
-  An ECS with an EIP bound has been created in the same VPC as the cluster, and the ECS has been connected to the cluster through kubectl.

.. _cce_bestpractice_00199__section1171195014615:

Step 1: Create an OBS Bucket Policy and ACL
-------------------------------------------

Configure an OBS bucket policy and ACL using account A and grant account B required permissions like the read and write permissions.

#. Configure the parameters. In this example, only some mandatory parameters are described. You can keep the default values for other parameters.

   .. table:: **Table 2** Bucket policy parameters

      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+
      | Parameter             | Description                                                                                                                                                                                                        | Example                                     |
      +=======================+====================================================================================================================================================================================================================+=============================================+
      | Policy Name           | Enter a name.                                                                                                                                                                                                      | example01                                   |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+
      | Effect                | Specify the behavior of a policy.                                                                                                                                                                                  | Allow                                       |
      |                       |                                                                                                                                                                                                                    |                                             |
      |                       | -  **Allow**: The actions defined in the policy are allowed.                                                                                                                                                       |                                             |
      |                       | -  **Deny**: The actions defined in the policy are denied.                                                                                                                                                         |                                             |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+
      | Principal             | Specify authorized accounts. (Multiple accounts can be selected.) For different types of authorized accounts, the OBS console provides different templates for authorizations.                                     | Other accounts                              |
      |                       |                                                                                                                                                                                                                    |                                             |
      |                       | -  **All accounts**: Any account can execute the current bucket policy without identity authentication, which may pose data security risks.                                                                        | XXX(account ID)/XXX (IAM ID)                |
      |                       | -  **Current account**: Grant permissions to a specific IAM account under the current account.                                                                                                                     |                                             |
      |                       | -  **Other accounts**: Grant permissions to a specific IAM account under another account.                                                                                                                          |                                             |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+
      | Resources             | Specify the authorized resources.                                                                                                                                                                                  | Entire bucket (including the objects in it) |
      |                       |                                                                                                                                                                                                                    |                                             |
      |                       | -  **Entire bucket (including the objects in it)**: Allow authorized accounts to perform certain actions on a bucket and the objects in it.                                                                        |                                             |
      |                       | -  **Current bucket**: Allow authorized accounts to perform certain actions on the current bucket.                                                                                                                 |                                             |
      |                       | -  **Specific objects**: Allow authorized accounts to perform certain actions on the specified objects in a bucket.                                                                                                |                                             |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+
      | Actions               | Specify actions.                                                                                                                                                                                                   | Use a template > Bucket Read/Write          |
      |                       |                                                                                                                                                                                                                    |                                             |
      |                       | -  **Use a template**: Use a permission template preset on the OBS console. If you selected **Bucket Read/Write**, the **Specified actions** option will be selected by default in the **Advanced Settings** area. |                                             |
      |                       | -  **Customize**: Customize the actions.                                                                                                                                                                           |                                             |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------+

#. In the navigation pane, choose **Permissions** > **Bucket ACL**. In the right pane, click **Add** under **User Access**. Enter the account ID of the authorized user, select **Read** and **Write** for **Access to Bucket**, select **Read** for **Access to Objects**, select **Read** and **Write** for **Access to ACL**, and click **OK**.

.. _cce_bestpractice_00199__section1168445316142:

Step 2: Create a Workload with an OBS Volume Mounted
----------------------------------------------------

Create a PV and a PVC based on the OBS bucket of account A using account B and mount the PVC to the required workload.

#. .. _cce_bestpractice_00199__li840004701911:

   Create a ConfigMap named **paas-obs-endpoint** and configure the region and endpoint of OBS.

   .. code-block::

      vim config.yaml

   The content is as follows: (For details about the parameters, see :ref:`Table 3 <cce_bestpractice_00199__table9353123918543>`.)

   .. code-block::

      apiVersion: v1
      kind: ConfigMap
      metadata:
        name: paas-obs-endpoint   # The value must be paas-obs-endpoint.
        namespace: kube-system    # The value must be kube-system.
      data:
        obs-endpoint: |
          {"<region_name>": "<endpoint_address>"}

   Create the ConfigMap using **config.yaml**.

   .. code-block::

      kubectl create -f config.yaml

   .. _cce_bestpractice_00199__table9353123918543:

   .. table:: **Table 3** ConfigMap parameters

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | ConfigMap                         | Description                                                                                                                                                                               |
      +===================================+===========================================================================================================================================================================================+
      | metadata.name                     | ConfigMap name, which is fixed at **paas-obs-endpoint** and cannot be changed                                                                                                             |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | metadata.namespace                | Namespace, which is fixed at **kube-system** and cannot be changed                                                                                                                        |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | data.obs-endpoint                 | Region names and endpoints are in key-value pairs. Replace *<region_name>* and *<endpoint_address>* with specific values. If multiple values are needed, use commas (,) to separate them. |
      |                                   |                                                                                                                                                                                           |
      |                                   | For details about its value, see `Regions and Endpoints <https://docs.otc.t-systems.com/regions-and-endpoints/index.html>`__.                                                             |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. .. _cce_bestpractice_00199__li1911923093810:

   Create a secret named **test-user**. (This secret is used to provide access credentials when volumes are mounted to CSI, and its name can be customized.)

   a. Obtain the AK. Go back to the management console, hover the cursor over the username in the upper right corner and choose **My Credentials** from the drop-down list.

      In the navigation pane, choose **Access Keys**. On the page displayed, click **Create Access Key**.

      Click **OK** and download the AK.

   b. Encode the AK using Base64 and save the encoded AK and SK. If the AK obtained is **xxx** and the SK is **yyy**, run the following commands:

      .. code-block::

         echo -n xxx|base64
         echo -n yyy|base64

   c. Create a secret YAML file, for example, **test_user.yaml**.

      .. code-block::

         vim test_user.yaml

      The content is as follows: (For details about the parameters, see :ref:`Table 4 <cce_bestpractice_00199__table1049315209011>`.)

      .. code-block::

         apiVersion: v1
         data:
           access.key: QUxPQUlJU******
           secret.key: aVMwZkduQ******
         kind: Secret
         metadata:
           name: test-user
           namespace: default
         type: cfe/secure-opaque

      Create a secret using **test_user.yaml**.

      .. code-block::

         kubectl create -f test_user.yaml

      .. _cce_bestpractice_00199__table1049315209011:

      .. table:: **Table 4** Secret parameters

         +-----------------------+------------------------------------------------------------------------------------+-----------------------+
         | Parameter             | Description                                                                        | Example               |
         +=======================+====================================================================================+=======================+
         | access.key            | A Base64-encoded AK                                                                | QUxPQUlJU*****\*      |
         +-----------------------+------------------------------------------------------------------------------------+-----------------------+
         | secret.key            | A Base64-encoded SK                                                                | aVMwZkduQ*****\*      |
         +-----------------------+------------------------------------------------------------------------------------+-----------------------+
         | type                  | Key type, which is fixed at **cfe/secure-opaque** and cannot be changed            | cfe/secure-opaque     |
         |                       |                                                                                    |                       |
         |                       | When this type is used, the data entered by users will be automatically encrypted. |                       |
         +-----------------------+------------------------------------------------------------------------------------+-----------------------+

#. Create a PV named **testing_abc** and mount the secret named **test_user** to the PV.

   .. code-block::

      vim testing_abc.yaml

   The content is as follows: (For details about the parameters, see :ref:`Table 5 <cce_bestpractice_00199__table10737110101213>`.)

   .. code-block::

      kind: PersistentVolume
      apiVersion: v1
      metadata:
        name: testing-abc
        annotations:
          pv.kubernetes.io/bound-by-controller: 'yes'
          pv.kubernetes.io/provisioned-by: everest-csi-provisioner
      spec:
        capacity:
          storage: 1Gi
        mountOptions:
        - default_acl=bucket-owner-full-control      #New OBS mounting parameters
        csi:
          driver: obs.csi.everest.io
          volumeHandle: obs-cce-test                 # Name of the OBS bucket to be mounted
          fsType: s3fs                               # obsfs indicates a parallel file system, and s3fs indicates an OBS bucket.
          volumeAttributes:
            everest.io/obs-volume-type: STANDARD     # Bucket type, which can be STANDARD or WARM when an OBS bucket is used
            everest.io/region: <region_name>         # Region where the OBS bucket is located (Replace it with the actual value.)
            storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
          nodePublishSecretRef:                      # AK/SK used for mounting the OBS bucket
            name: test-user
            namespace: default
        accessModes:
          - ReadWriteMany
        persistentVolumeReclaimPolicy: Retain        # PV reclaim policy
        storageClassName: csi-obs                    # csi-obs specifies an OBS storage class that is automatically created. You can customize it as required.
        volumeMode: Filesystem

   Create the PV using **testing_abc.yaml**.

   .. code-block::

      kubectl create -f testing_abc.yaml

   .. _cce_bestpractice_00199__table10737110101213:

   .. table:: **Table 5** PV parameters

      +-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                     | Description                                                                                                                                                                                                                                                                                                                | Example                                                                                                                                                                                           |
      +===============================+============================================================================================================================================================================================================================================================================================================================+===================================================================================================================================================================================================+
      | mountOptions.default_acl      | Specify access control policies for a bucket and objects in the bucket. In this example, account A owns the bucket, and both account A and account B can upload data.                                                                                                                                                      | bucket-owner-full-control                                                                                                                                                                         |
      |                               |                                                                                                                                                                                                                                                                                                                            |                                                                                                                                                                                                   |
      |                               | -  **private**: The bucket or objects can only be fully accessed by the owner of the bucket.                                                                                                                                                                                                                               | .. note::                                                                                                                                                                                         |
      |                               | -  **public-read**: The owner of the bucket has complete control over both the bucket and its objects. While other users can read data from the bucket, they are unable to modify, delete, or upload any data within it.                                                                                                   |                                                                                                                                                                                                   |
      |                               | -  **public-read-write**: The owner of the bucket has complete control over the bucket and its objects. Other users can read and write data from and to the bucket.                                                                                                                                                        |    Due to the bucket policy that was configured using account A, account B has been granted read and write permissions for the entire bucket, including objects uploaded by both account A and B. |
      |                               | -  **bucket-owner-read**: Users who have uploaded objects to the bucket has complete control over the objects, while the bucket owner is only granted read permissions for said objects. **This mode is usually used in cross-account sharing scenarios**.                                                                 |                                                                                                                                                                                                   |
      |                               | -  **bucket-owner-full-control**: Users who have uploaded objects to the bucket are granted write permissions for those specific objects, but not read permissions by default. The bucket owner has complete control over all objects within the bucket. **This mode is usually used in cross-account sharing scenarios.** |                                                                                                                                                                                                   |
      +-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | csi.nodePublishSecretRef      | Specify the secret to be mounted.                                                                                                                                                                                                                                                                                          | test-user                                                                                                                                                                                         |
      |                               |                                                                                                                                                                                                                                                                                                                            |                                                                                                                                                                                                   |
      |                               | -  name: name of a secret                                                                                                                                                                                                                                                                                                  | default                                                                                                                                                                                           |
      |                               | -  namespace: namespace where the secret is in                                                                                                                                                                                                                                                                             |                                                                                                                                                                                                   |
      +-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | csi.volumeHandle              | Specify the name of the OBS bucket to be mounted.                                                                                                                                                                                                                                                                          | obs-cce-test (OBS bucket name authorized by account A)                                                                                                                                            |
      +-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | csi.fsType                    | Specify the file type.                                                                                                                                                                                                                                                                                                     | s3fs                                                                                                                                                                                              |
      |                               |                                                                                                                                                                                                                                                                                                                            |                                                                                                                                                                                                   |
      |                               | -  **obsfs**: Create an OBS parallel file system.                                                                                                                                                                                                                                                                          | .. important::                                                                                                                                                                                    |
      |                               | -  **s3fs**: Create an OBS bucket.                                                                                                                                                                                                                                                                                         |                                                                                                                                                                                                   |
      |                               |                                                                                                                                                                                                                                                                                                                            |    NOTICE:                                                                                                                                                                                        |
      |                               |                                                                                                                                                                                                                                                                                                                            |    The file type specified by the PV and the PVC must match in order for the PV to be bound to the corresponding PVC. If they do not match, the binding cannot occur.                             |
      +-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | accessModes                   | Specify the access mode of the storage volume. OBS supports only **ReadWriteMany**.                                                                                                                                                                                                                                        | ReadWriteMany                                                                                                                                                                                     |
      |                               |                                                                                                                                                                                                                                                                                                                            |                                                                                                                                                                                                   |
      |                               | -  **ReadWriteOnce**: A storage volume can be mounted to a single node in read-write mode.                                                                                                                                                                                                                                 |                                                                                                                                                                                                   |
      |                               | -  **ReadWriteMany**: A storage volume can be mounted to multiple nodes in read-write mode.                                                                                                                                                                                                                                |                                                                                                                                                                                                   |
      +-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | persistentVolumeReclaimPolicy | Specify the reclaim policy for the PV.                                                                                                                                                                                                                                                                                     | Retain                                                                                                                                                                                            |
      |                               |                                                                                                                                                                                                                                                                                                                            |                                                                                                                                                                                                   |
      |                               | -  **Delete**: When a PVC is deleted, both the PV and underlying storage resources will be deleted. If the **everest.io/reclaim-policy: retain-volume-only** annotation is added to the YAML file, the underlying storage resources will be retained.                                                                      | .. note::                                                                                                                                                                                         |
      |                               |                                                                                                                                                                                                                                                                                                                            |                                                                                                                                                                                                   |
      |                               | -  **Retain**: When a PVC is deleted, both the PV and underlying storage resources will be retained. You need to manually delete them. After the PVC is deleted, the PV is in the **Released** state and cannot be bound to a PVC again.                                                                                   |    If multiple PVs use the same OBS volume, use **Retain** to prevent the underlying volume from being deleted with one of the PV.                                                                |
      +-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create a PVC named **pvc-test-abc** and bind the new PV **testing_abc** to it.

   .. code-block::

      vim pvc_test_abc.yaml

   The file content is as follows:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: pvc-test-abc
        namespace: default
        annotations:
          csi.storage.k8s.io/node-publish-secret-name: test-user      # Mount a secret.
          csi.storage.k8s.io/node-publish-secret-namespace: default   # Namespace of the secret
            everest.io/obs-volume-type: STANDARD     # Bucket type, which can be STANDARD or WARM when an OBS bucket is used
          csi.storage.k8s.io/fstype: s3fs            # File type. obsfs indicates a parallel file system, and s3fs indicates an OBS bucket.
          volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
      spec:
        accessModes:
        - ReadWriteMany             # The value must be ReadWriteMany for object storage.
        resources:
          requests:
            storage: 1Gi            # Storage capacity of a PVC. This parameter is valid only for verification (fixed to 1, cannot be empty or 0). The value setting does not take effect for OBS buckets.
        storageClassName: csi-obs   # csi-obs specifies an OBS storage class that is automatically created. You can customize it as required.
        volumeName: testing-abc     # PV name

   Create the PVC using **pvc_test_abc.yaml**.

   .. code-block::

      kubectl create -f pvc_test_abc.yaml

#. Create a workload and mount the PVC to it. The following uses an Nginx Deployment as an example.

   .. code-block::

      vim obs_deployment_example.yaml

   The file content is as follows:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: obs-deployment-example                  # Workload name, which can be customized
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: obs-deployment-example              # Label, which can be customized
        template:
          metadata:
            labels:
              app: obs-deployment-example
          spec:
            containers:
            - image: nginx
              name: container-0
              volumeMounts:
              - mountPath: /tmp                       # PVC mount path, which can be customized as required
                name: pvc-obs-example
            restartPolicy: Always
            imagePullSecrets:
              - name: default-secret
            volumes:
            - name: pvc-obs-example
              persistentVolumeClaim:
                claimName: pvc-test-abc               # PVC name

   Create the workload named **obs-deployment-example** using **obs_deployment_example.yaml**.

   .. code-block::

      kubectl create -f obs_deployment_example.yaml

   Check whether the workload has been created.

   .. code-block::

      kubectl get pod

   If information similar to the following is displayed and the workload is in the **Running** state, the workload has been created.

   .. code-block::

      NAME                                      READY   STATUS              RESTARTS        AGE
      obs-deployment-example-6b4dfd7b57-frfxv   1/1     Running             0               22h

.. _cce_bestpractice_00199__section14892114864113:

Step 3: Check the Pod Actions on the OBS Bucket
-----------------------------------------------

Check whether the pod created by account B has the required permissions based on the bucket policy.

#. Check whether the pod can read and write objects in the OBS bucket created by account A and assume that a **test.txt** file is present in the OBS bucket.

   Run the following command to access the created workload. (You can press **Ctrl**\ +\ **D** to exit the current workload.)

   .. code-block::

      kubectl -n default exec -it obs-deployment-example-6b4dfd7b57-frfxv -c container-0 -- bash

   Run the following command to check the pod actions on **test.txt**. **/tmp** specifies the PVC mount path.

   .. code-block::

      ls -l /tmp/test.txt

   If information similar to the following is displayed, the pod has the read and write permissions on the **test.txt** file, which is related to the bucket policy set by account A.

   .. code-block::

      -rwxrwxrwx 1 root root 4 Sep  5 09:09 /tmp/test.txt

#. Check whether the pod can read and write data from and to the objects uploaded by itself in the OBS bucket.

   Create a **test01.txt** file in **/tmp** and write **test\\n** into the file.

   .. code-block::

      echo -e "test\n" > /tmp/test01.txt

   Run the following command to check the **test01.txt** content and check whether the pod can read and write new objects uploaded by itself: (Account A can check the new objects in the OBS bucket.)

   .. code-block::

      cat /tmp/test01.txt

   If information similar the following is displayed, the pod has the read and write permissions on the objects uploaded by itself.

   .. code-block::

      test

.. _cce_bestpractice_00199__section2860455112310:

Step 4: Clear Resources
-----------------------

Once you have studied this example, delete any associated resources to prevent incurring settlement fees. If you plan to learn other examples, wait until they are finished before doing any clean-up.

#. Run the following command to delete the workload:

   .. code-block::

      kubectl delete -f obs_deployment_example.yaml

   Information similar to the following is displayed:

   .. code-block::

      deployment.apps "obs-deployment-example" deleted

#. Run the following command to delete the PVC:

   .. code-block::

      kubectl delete -f pvc_test_abc.yaml

   Information similar to the following is displayed:

   .. code-block::

      persistentvolumeclaim "pvc-test-abc" deleted

#. Run the following command to delete the PV:

   .. code-block::

      kubectl delete -f testing_abc.yaml

   Information similar to the following is displayed:

   .. code-block::

      persistentvolume "testing-abc" deleted

#. Run the following command to delete the secret:

   .. code-block::

      kubectl delete -f test_user.yaml

   Information similar to the following is displayed:

   .. code-block::

      secret "test-user" deleted

#. Run the following command to delete the ConfigMap:

   .. code-block::

      kubectl delete -f config.yaml

   Information similar to the following is displayed:

   .. code-block::

      configmap "paas-obs-endpoint" deleted

Common Issues
-------------

If a workload fails to be created, locate the fault based on the error information in the pod events. For details, see :ref:`Table 6 <cce_bestpractice_00199__table47899111691>`.

.. _cce_bestpractice_00199__table47899111691:

.. table:: **Table 6** Locating the fault

   +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Error                                                                                                                                                                                                                                                                                               | Possible Cause                                                                          | Fault Locating                                                                                                                                                                                                                                                                                                                                                                                             |
   +=====================================================================================================================================================================================================================================================================================================+=========================================================================================+============================================================================================================================================================================================================================================================================================================================================================================================================+
   | 0/4 nodes are available: pod has unbound immediate PersistentVolumeClaims. preemption: 0/4 nodes are available: 4 Preemption is not helpful for scheduling.                                                                                                                                         | The PVC is not bound to any PV.                                                         | #. Run the following command to check the PVC status:                                                                                                                                                                                                                                                                                                                                                      |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    .. code-block::                                                                                                                                                                                                                                                                                                                                                                                         |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |       kubectl get pvc                                                                                                                                                                                                                                                                                                                                                                                      |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    If the PVC is in the **Pending** state, it is not bound to any PV.                                                                                                                                                                                                                                                                                                                                      |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         | #. Check the PVC details and locate the cause of the binding failure.                                                                                                                                                                                                                                                                                                                                      |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    .. code-block::                                                                                                                                                                                                                                                                                                                                                                                         |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |       kubectl describe pvc <pvc_name>                                                                                                                                                                                                                                                                                                                                                                      |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         | #. Modify the YAML file to rectify the fault if the fault is caused by any of the following reasons:                                                                                                                                                                                                                                                                                                       |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    -  The PVC is not bound to the proper PV.                                                                                                                                                                                                                                                                                                                                                               |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    -  The PVC and PV parameters do not match. This includes **fsType**, **StorageClass**, **accessModes**, and **storage**. The **StorageClass** must be object storage, and **accessModes** must be set to **ReadWriteMany** because OBS buckets only support this mode. Additionally, the **storage** value requested by the PVC must be equal to or less than the **storage** value provided by the PV. |
   +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | MountVolume.SetUp failed for volume *"obs-cce-example"*: rpc error: code = Unknown desc = failed to get secret(paas.longaksk), err: get secret(paas.longaksk) failed: get secret paas.longaksk from namespace kube-system failed: secrets "paas.longaksk" not found                                 | The required secret cannot be found when the storage volume is mounted to the workload. | #. Check whether the mounted secret is present.                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    .. code-block::                                                                                                                                                                                                                                                                                                                                                                                         |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |       kubectl get secret                                                                                                                                                                                                                                                                                                                                                                                   |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    If it is present, the secret may be incorrectly configured. If it is not present, you can create one by referring to :ref:`2 <cce_bestpractice_00199__li1911923093810>`.                                                                                                                                                                                                                                |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         | #. Check the secret parameter configurations. The following shows the common issues:                                                                                                                                                                                                                                                                                                                       |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    -  The **access.key** and **secret.key** parameters are not set to the AK and SK encoded using Base64.                                                                                                                                                                                                                                                                                                  |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    -  The **type** parameter is not set to **cfe/secure-opaque**.                                                                                                                                                                                                                                                                                                                                          |
   +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | MountVolume.SetUp failed for volume *"pv-obs-example"*: rpc error: code = Internal desc = [8032c354-4e1b-41b0-81ce-9d4b3f8c49c9] get obsUrl failed before mount bucket obs-cce-example, get configMap paas-obs-endpoint from namespace kube-system failed: configmaps "paas-obs-endpoint" not found | The ConfigMap that stores the OBS endpoint is not present or is incorrectly configured. | #. Check whether the ConfigMap is present.                                                                                                                                                                                                                                                                                                                                                                 |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    .. code-block::                                                                                                                                                                                                                                                                                                                                                                                         |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |       kubectl get configmap -n kube-system                                                                                                                                                                                                                                                                                                                                                                 |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    If it is present, the ConfigMap may be incorrectly configured. If it is not present, you can create one by referring to :ref:`1 <cce_bestpractice_00199__li840004701911>`.                                                                                                                                                                                                                              |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         | #. Check the ConfigMap parameter configurations. The following shows the common issues:                                                                                                                                                                                                                                                                                                                    |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                            |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    -  The **name** parameter is not set to **paas-obs-endpoint**.                                                                                                                                                                                                                                                                                                                                          |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    -  The **namespace** parameter is not set to **kube-system**.                                                                                                                                                                                                                                                                                                                                           |
   |                                                                                                                                                                                                                                                                                                     |                                                                                         |    -  The region name is not set to the region where the OBS bucket is located.                                                                                                                                                                                                                                                                                                                            |
   +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
