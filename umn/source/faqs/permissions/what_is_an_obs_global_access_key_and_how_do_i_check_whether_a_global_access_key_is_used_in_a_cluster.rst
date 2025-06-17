:original_name: cce_faq_00484.html

.. _cce_faq_00484:

What Is an OBS Global Access Key and How Do I Check Whether a Global Access Key Is Used in a Cluster?
=====================================================================================================

When creating an OBS PVC in a CCE cluster, you need to select an access key (AK/SK). OBS access keys are classified into the following types:

-  (Recommended) Custom access key: When a custom access key is used, the YAML file of the PVC contains the **csi.storage.k8s.io/node-publish-secret-namespace** and **csi.storage.k8s.io/node-publish-secret-name** annotations, which specify the secret namespace and secret name for storing keys in the cluster, respectively.

   |image1|

-  (Not recommended and supported only by existing users who have uploaded files) Global access key: When a global access key is used, the YAML file of the PVC does not contain the **csi.storage.k8s.io/node-publish-secret-namespace** and **csi.storage.k8s.io/node-publish-secret-name** annotations, and the secret of the global access key is fixed in the **kube-system** namespace and named **paas.longaksk**.

   |image2|

   .. note::

      The global access secret (**paas.longaksk**) is used by project. Once a global access secret is used, it is automatically created for each cluster within the same project. However, this can lead to security and management complexities. Therefore, it is not recommended that you use global access secrets.

      If you do not need to use the global access key, you can disable it in **Settings** of the cluster. After the operation, CCE deletes the global access key **paas.longaksk** from the **kube-system** namespace. If you need to use this function later, you can enable it again in the settings.

Checking Whether a Global Access Key Is Used in a Cluster
---------------------------------------------------------

-  Using the console

   In the navigation pane, choose **Storage**. In the right pane, select all namespaces, click the **PersistentVolumeClaims (PVCs)** tab, add a filter **Storage Settings: obs**, and view the results in the **Storage Settings** column.

   If the access key is empty, the global access key is used. If the access key is not empty, a custom access key is used.

-  Using kubectl

   .. code-block::

      kubectl get pvc --all-namespaces -o=custom-columns=Name:'metadata.name',Namespace:'metadata.namespace',secretName:'metadata.annotations.csi\.storage\.k8s\.io\/node-publish-secret-name' | grep \<none\>

   You can use the command above to find out which PVC in the cluster is using the global access key. The first column in the command output shows the PVC name, and the second column indicates the namespace where the PVC is located.

   .. code-block::

      obs-test     default     <none>

   If the command output is blank, it means no PVC is using the global access key in the cluster.

.. |image1| image:: /_static/images/en-us_image_0000002218659314.png
.. |image2| image:: /_static/images/en-us_image_0000002218819126.png
