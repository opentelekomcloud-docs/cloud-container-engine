:original_name: cce_10_0966.html

.. _cce_10_0966:

Automatically Applying Updated Access Keys (AK/SK) for an OBS Volume
====================================================================

If your service containers use OBS for data storage and access, you must manually restart them whenever the OBS volume's access keys are changed to apply the new keys. This process may interrupt service continuity.

CCE Container Storage (Everest) can automatically apply the new access keys for an OBS volume after they are updated. After the access keys of an OBS volume are updated, CCE automatically detects the change and applies the new keys to all affected workloads. This eliminates the need to manually restart these workloads, ensuring seamless service continuity during the key update.

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available, and the cluster version meets the following requirements:

   -  v1.25: v1.25.16-r30 or later
   -  v1.27: v1.27.16-r30 or later
   -  v1.28: v1.28.15-r20 or later
   -  v1.29: v1.29.13-r0 or later
   -  v1.30: v1.30.10-r0 or later
   -  v1.31: v1.31.6-r0 or later
   -  v1.32: v1.32.1-r0 or later
   -  Clusters of later versions

-  The CCE Container Storage (Everest) add-on v2.4.150 or later has been installed in the cluster.

Notes and Constraints
---------------------

-  If the add-on version is between 2.4.150 (inclusive) and 2.4.165 (exclusive), automatic access key updating applies only to parallel file systems. Starting from version 2.4.165 and onward, both parallel file systems and OBS buckets are supported.
-  Automatic access key updating applies only to workloads created after CCE Container Storage (Everest) is updated to a specific version (2.4.150 or later for parallel file systems, and 2.4.165 or later for OBS buckets). To enable existing workloads to support this function, manually rebuild these workloads after CCE Container Storage (Everest) is upgraded.

Step 1: Check Whether a Workload Supports Automatic Key Updates
---------------------------------------------------------------

Automatic key updates for OBS volumes are only available for workloads created after CCE Container Storage (Everest) is upgraded to the required version. Ensure this add-on is correctly upgraded and the target workload is created afterward.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. Check whether the CCE Container Storage (Everest) version is 2.4.150 or later.

   Click **Edit**. In **Edit Add-on**, ensure dynamic AK/SK update is enabled in **Extended Parameter Settings** under **Advanced Settings**. Verify **enable_aksk_refresh** is set to **true**.

#. Check the workloads that use OBS buckets and the nodes where these workloads are running in the cluster. The following uses a parallel file system as an example.

   a. In the navigation pane, choose **Storage**. Click the **PersistentVolumeClaims (PVCs)** tab on the right and check whether there are storage volumes using parallel file systems across all namespaces.

   b. Confirm parallel file system storage across all namespaces. The following uses the **default** namespace as an example, where *{pvc_name}* indicates the PVC name:

      .. code-block::

         kubectl get pods -n default -o custom-columns="POD:.metadata.name,PVC:.spec.volumes[*].persistentVolumeClaim.claimName,hostIP:.status.hostIP,UID:.metadata.uid" | grep {pvc_name}

      Information similar to the following is displayed:

      .. code-block::

         obs-test-65c6dd7675-c67ss     obs      192.168.0.227   adb83cd4-58cc-4d01-a91a-584c6ed477fc

      The *obs-test-65c6dd7675-c67ss* pod is mounted with the *obs* PVC. The IP address of the node where the pod is running is 192.168.0.227, and the node's UID is *adb83cd4-58cc-4d01-a91a-584c6ed477fc*.

#. Log in to the node where the pod is deployed and run the following command to check when the obsfs tool will be updated after CCE Container Storage (Everest) is upgraded:

   .. code-block::

      ls -l /usr/bin/obsfs

   Information similar to the following is displayed, which indicates that obsfs was updated at 17:09 on March 20:

   .. code-block::

      -rwx------ 1 root root 9424176 Mar 20 17:09 /usr/bin/obsfs

#. Check the start time of obsfs on the node where the pod is deployed.

   .. code-block::

      ps -ef | grep {pod_uid}

   Information similar to the following is displayed.

   |image1|

   The obsfs used by the pod was mounted at 10:34, which is later than the time when obsfs was updated by CCE Container Storage (Everest). Therefore, the pod supports automatic key updates.

   If the obsfs process used by the pod is earlier than the time when the obsfs tool was updated by CCE Container Storage (Everest), you need to manually restart the pod. Automatic key updates will take effect automatically.

Step 2: Update the Custom Access Keys (AK/SK) of the OBS Volume
---------------------------------------------------------------

After confirming that the workload with the OBS volume mounted supports automatic key updates, you can update the custom access keys (AK/SK) of the OBS volume.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Storage**. On the **PVCs** tab, locate the volume and click **More** > **Update Access Key** in the **Operation** column.
#. Upload the new access keys (AK/SK) and click **OK**.

   .. caution::

      Ensure that the updated keys are valid and have the permissions to access the corresponding OBS volume. Otherwise, the workload cannot access the mounted object storage.

Step 3: Confirm Updated Keys for a Workload
-------------------------------------------

After the access keys used by OBS are updated, you can take the following steps to verify that the update takes effect in about 30 seconds by viewing pod events or alarms:

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Workloads**. Click the workload name to go to the details page.

#. On the **Pods** tab, click **View Events** in the **Operation** column of the pod.

   If the event "failed to refresh ak/sk for xxx" is displayed, the keys fail to be updated for the workload. Locate the cause as prompted. For details, see :ref:`Common Events <cce_10_0966__section654769155014>`. If no alarm or event is generated, the update is successful.

.. _cce_10_0966__section654769155014:

Common Events
-------------

-  The certificate is invalid. The event is as follows:

   .. code-block::

      failed to refresh ak/sk for xxx, reason: invalid credentials(host=xxx:443) - result of checking service.

   **Solution**

   Change the certificate to a valid one and then check whether the fault is rectified.

-  Updating the AK/SK times out. The event is as follows:

   .. code-block::

      failed to refresh ak/sk for xxx, reason: wait timeout...

   **Solution**

   This alarm may be generated occasionally when the obsfs process exits due to pod deletion or migration. This is a normal situation. If no alarm is reported continuously, ignore it.

   If this alarm persists, rebuild the pod and then check whether the fault is rectified.

.. |image1| image:: /_static/images/en-us_image_0000002434079924.png
