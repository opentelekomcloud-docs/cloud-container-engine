:original_name: cce_10_0196.html

.. _cce_10_0196:

NetworkAttachmentDefinition
===========================

Scenario
--------

In a CCE Turbo cluster, you can configure subnets and security groups for containers by namespace or workload using NetworkAttachmentDefinition `CRDs <https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/>`__. If you want to configure a specified container subnet and security group for a specified namespace or workload, you can create a custom container NetworkAttachmentDefinition and associate the container network configuration with the corresponding namespace or workload. In this way, service subnets can be planned or services can be securely isolated.

The following table lists the resources that a container network configuration can be associated with.

.. _cce_10_0196__table17616165517211:

.. table:: **Table 1** Associated resources

   +------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
   | Aspect                                   | Resources a Container Network Configuration Can Associate with                                                                                  |                                                                                                                               |
   +==========================================+=================================================================================================================================================+===============================================================================================================================+
   |                                          | Namespace                                                                                                                                       | Workload                                                                                                                      |
   +------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
   | Subnet and security group configurations | All workloads created in the namespace associated with a container network configuration use the same subnet and security group configurations. | The workloads associated with the same container network configuration use the same subnet and security group configurations. |
   +------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
   | Supported Cluster Version                | Available only in CCE Turbo clusters of 1.23.8-r0, 1.25.3-r0, or later                                                                          | Available only in CCE Turbo clusters of 1.23.11-r0, 1.25.6-r0, 1.27.3-r0, or later                                            |
   +------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
   | Constraint                               | The namespaces associated with different container network configurations must be unique.                                                       | Only the custom container network configurations that are not associated with any namespace can be specified.                 |
   +------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+

Constraints
-----------

-  Only the default-network supports container ENI prebinding. The creation speed of pods using the custom container network is slower than that of pods using the default-network. Therefore, this function is not suitable for ultra-fast pod scaling scenarios.
-  The default container network configuration **default-network** cannot be deleted.
-  To delete a NetworkAttachmentDefinition, delete pods (with the **cni.yangtse.io/network-status** annotation) created using the configuration in the corresponding namespace first. For details, see :ref:`Deleting a Network Configuration <cce_10_0196__section2314125415245>`.

Creating a NetworkAttachmentDefinition of the Namespace Type Using the CCE Console
----------------------------------------------------------------------------------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Settings** in the navigation pane and click the **Network** tab.

   .. note::

      If default-network exists in the cluster, it will take effect for all pods for which no custom network is configured. Default container subnet in the network configuration area on the **Overview** page is the container subnet in default-network.

#. View **Custom Container Network Settings** and click **Add**. In the dialog box displayed, configure the container subnet and security group.

   -  **Name**: Enter a name that contains a maximum of 253 characters. Do not use **default-network**, **default**, **mgnt0**, and **mgnt1**.
   -  **Associated Resource Type**: resource type associated with the custom container network configuration. For details, see :ref:`Table 1 <cce_10_0196__table17616165517211>`. To create a container network configuration of the namespace type, select **Namespace**.
   -  **Namespace**: Select the namespace to be associated. Namespaces associated with different container network configurations must be unique. If no namespace is available, click **Create Namespace** to create one.
   -  **Pod Subnet**: Select a subnet. If no subnet is available, click **Create Subnet** to create a subnet. After the subnet is created, click the refresh button. A maximum of 20 subnets can be selected.
   -  **Associate Security Group**: The default value is the container ENI security group. You can also click **Create Security Group** to create one. After the security group is created, click the refresh button. A maximum of five security groups can be selected.

#. Click **OK**. After the creation is complete, you will be redirected to the network configuration list. You can see that the newly added subnet is in the list.

Using the CCE Console to Create a Container Network Configuration of the Workload Type
--------------------------------------------------------------------------------------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Settings** in the navigation pane and click the **Network** tab.

   .. note::

      If default-network exists in the cluster, it will take effect for all pods for which no custom network is configured. Default container subnet in the network configuration area on the **Overview** page is the container subnet in default-network.

#. View the **Custom Container Network Settings** and click **Add**. In the window that slides out from the right, configure parameters such as the pod subnet and security group.

   -  **Name**: Enter a name that contains a maximum of 253 characters. Do not use **default-network**, **default**, **mgnt0**, and **mgnt1**.
   -  **Associated Resource Type**: resource type associated with the custom container network configuration. For details, see :ref:`Table 1 <cce_10_0196__table17616165517211>`. To create a container network configuration of the workload type, select **Workloads**.
   -  **Pod Subnet**: Select a subnet. If no subnet is available, click **Create Subnet** to create a subnet. After the subnet is created, click the refresh button. A maximum of 20 subnets can be selected.
   -  **Associate Security Group**: The default value is the container ENI security group. You can also click **Create Security Group** to create one. After the security group is created, click the refresh button. A maximum of five security groups can be selected.

#. Click **OK**. After the creation, you will be redirected to the custom container network configuration list, where the new container network configuration is included.
#. When creating a workload, select a custom container network configuration.

   a. In the navigation pane, choose **Workloads**. In the right pane, click the **Deployments** tab.

   b. Click **Create Workload** in the upper right corner of the page. In the **Advanced Settings** area, choose **Network Configuration** and determine whether to enable a specified container network configuration.

   c. Select an existing container network configuration. If no configuration is available, click **Add** to create one.

   d. After the configuration, click **Create Workload**.

      Return to the **Settings** page. In the container network configuration list, the name of the resource associated with the created container network configuration is displayed.

Creating a NetworkAttachmentDefinition of the Namespace Type Using Kubectl
--------------------------------------------------------------------------

This section describes how to create a NetworkAttachmentDefinition of the namespace type using kubectl.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Modify the **networkattachment-test.yaml** file.

   **vi networkattachment-test.yaml**

   .. code-block::

      apiVersion: k8s.cni.cncf.io/v1
      kind: NetworkAttachmentDefinition
      metadata:
        annotations:
          yangtse.io/project-id: 05e38**
        name: example
        namespace: kube-system
      spec:
        config:
        '{
          "type":"eni-neutron",
          "args":{
            "securityGroups":"41891**",
            "subnets":[
              {
                "subnetID":"27d95**"
              }
            ]
          },
          "selector":{
            "namespaceSelector":{
              "matchLabels":{
                "kubernetes.io/metadata.name":"default"
              }
            }
          }
        }'

   .. table:: **Table 2** Key parameters

      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+
      | Parameter             | Mandatory | Type                                                   | Description                                                                              |
      +=======================+===========+========================================================+==========================================================================================+
      | apiVersion            | Yes       | String                                                 | API version. The value is fixed at **k8s.cni.cncf.io/v1**.                               |
      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+
      | kind                  | Yes       | String                                                 | Type of the object to be created. The value is fixed at **NetworkAttachmentDefinition**. |
      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+
      | yangtse.io/project-id | Yes       | String                                                 | Project ID.                                                                              |
      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+
      | name                  | Yes       | String                                                 | Configuration item name.                                                                 |
      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+
      | namespace             | Yes       | String                                                 | Namespace of the configuration resource. The value is fixed to **kube-system**.          |
      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+
      | config                | Yes       | :ref:`Table 3 <cce_10_0196__table452992692116>` object | Configuration content, which is a string in JSON format.                                 |
      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+

   .. _cce_10_0196__table452992692116:

   .. table:: **Table 3** config parameters

      +-----------------+-----------------+--------------------------------------------------------+----------------------------------------------------+
      | Parameter       | Mandatory       | Type                                                   | Description                                        |
      +=================+=================+========================================================+====================================================+
      | type            | Yes             | String                                                 | The value is fixed at **eni-neutron**.             |
      +-----------------+-----------------+--------------------------------------------------------+----------------------------------------------------+
      | args            | No              | :ref:`Table 4 <cce_10_0196__table1253012616211>`       | Configuration parameters.                          |
      |                 |                 |                                                        |                                                    |
      |                 |                 | object                                                 |                                                    |
      +-----------------+-----------------+--------------------------------------------------------+----------------------------------------------------+
      | selector        | No              | :ref:`Table 5 <cce_10_0196__table696412574307>` object | Namespace on which the configuration takes effect. |
      +-----------------+-----------------+--------------------------------------------------------+----------------------------------------------------+

   .. _cce_10_0196__table1253012616211:

   .. table:: **Table 4** args parameters

      +-----------------+-----------------+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter       | Mandatory       | Type                      | Description                                                                                                                                                                             |
      +=================+=================+===========================+=========================================================================================================================================================================================+
      | securityGroups  | No              | String                    | Security group ID. If no security group is planned, ensure that the security group is the same as that in **default-network**.                                                          |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           | How to obtain:                                                                                                                                                                          |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           | Log in to the VPC console. In the navigation pane, choose **Access Control** > **Security Groups**. Click the target security group name and copy the ID on the **Summary** tab page.   |
      +-----------------+-----------------+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | subnets         | Yes             | Array of subnetID Objects | List of container subnet IDs. At least one subnet ID must be entered. The format is as follows:                                                                                         |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           | .. code-block::                                                                                                                                                                         |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           |    [{"subnetID":"27d95**"},{"subnetID":"827bb**"},{"subnetID":"bdd6b**"}]                                                                                                               |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           | Subnet ID not used by the cluster in the same VPC.                                                                                                                                      |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           | How to obtain:                                                                                                                                                                          |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           | Log in to the VPC console. In the navigation pane, choose **Virtual Private Cloud** > **Subnets**. Click the target subnet name and copy the **Subnet ID** on the **Summary** tab page. |
      +-----------------+-----------------+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0196__table696412574307:

   .. table:: **Table 5** selector parameters

      +-------------------+-----------------+--------------------+------------------------------------------------------------------------------------+
      | Parameter         | Mandatory       | Type               | Description                                                                        |
      +===================+=================+====================+====================================================================================+
      | namespaceSelector | No              | matchLabels Object | A Kubernetes standard selector. Enter the namespace label in the following format: |
      |                   |                 |                    |                                                                                    |
      |                   |                 |                    | .. code-block::                                                                    |
      |                   |                 |                    |                                                                                    |
      |                   |                 |                    |    "matchLabels":{                                                                 |
      |                   |                 |                    |              "kubernetes.io/metadata.name":"default"                               |
      |                   |                 |                    |            }                                                                       |
      |                   |                 |                    |                                                                                    |
      |                   |                 |                    | The namespaces of different configurations cannot overlap.                         |
      +-------------------+-----------------+--------------------+------------------------------------------------------------------------------------+

#. Create a NetworkAttachmentDefinition.

   **kubectl create -f networkattachment-test.yaml**

   If information similar to the following is displayed, the NetworkAttachmentDefinition has been created.

   .. code-block::

      networkattachmentdefinition.k8s.cni.cncf.io/example created

Using Kubectl to Create a Container Network Configuration of the Workload Type
------------------------------------------------------------------------------

This section describes how to use kubectl to create a container network configuration of the workload type.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Modify the **networkattachment-test.yaml** file.

   **vi networkattachment-test.yaml**

   .. code-block::

      apiVersion: k8s.cni.cncf.io/v1
      kind: NetworkAttachmentDefinition
      metadata:
        annotations:
          yangtse.io/project-id: 05e38**
        name: example
        namespace: kube-system
      spec:
        config:
        '{
          "type":"eni-neutron",
          "args":{
            "securityGroups":"41891**",
            "subnets":[
              {
                "subnetID":"27d95**"
              }
            ]
          }'

   .. table:: **Table 6** Key parameters

      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+
      | Parameter             | Mandatory | Type                                                   | Description                                                                              |
      +=======================+===========+========================================================+==========================================================================================+
      | apiVersion            | Yes       | String                                                 | API version. The value is fixed at **k8s.cni.cncf.io/v1**.                               |
      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+
      | kind                  | Yes       | String                                                 | Type of the object to be created. The value is fixed at **NetworkAttachmentDefinition**. |
      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+
      | yangtse.io/project-id | Yes       | String                                                 | Project ID.                                                                              |
      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+
      | name                  | Yes       | String                                                 | Configuration item name.                                                                 |
      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+
      | namespace             | Yes       | String                                                 | Namespace of the configuration resource. The value is fixed to **kube-system**.          |
      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+
      | config                | Yes       | :ref:`Table 3 <cce_10_0196__table452992692116>` object | Configuration content, which is a string in JSON format.                                 |
      +-----------------------+-----------+--------------------------------------------------------+------------------------------------------------------------------------------------------+

   .. table:: **Table 7** config parameters

      +-----------------+-----------------+--------------------------------------------------+----------------------------------------+
      | Parameter       | Mandatory       | Type                                             | Description                            |
      +=================+=================+==================================================+========================================+
      | type            | Yes             | String                                           | The value is fixed at **eni-neutron**. |
      +-----------------+-----------------+--------------------------------------------------+----------------------------------------+
      | args            | No              | :ref:`Table 4 <cce_10_0196__table1253012616211>` | Configuration parameters.              |
      |                 |                 |                                                  |                                        |
      |                 |                 | object                                           |                                        |
      +-----------------+-----------------+--------------------------------------------------+----------------------------------------+

   .. table:: **Table 8** args parameters

      +-----------------+-----------------+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter       | Mandatory       | Type                      | Description                                                                                                                                                                             |
      +=================+=================+===========================+=========================================================================================================================================================================================+
      | securityGroups  | No              | String                    | Security group ID. If no security group is planned, select the same security group as that in **default-network**.                                                                      |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           | How to obtain:                                                                                                                                                                          |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           | Log in to the VPC console. In the navigation pane, choose **Access Control** > **Security Groups**. Click the target security group name and copy the ID on the **Summary** tab page.   |
      +-----------------+-----------------+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | subnets         | Yes             | Array of subnetID Objects | List of container subnet IDs. At least one subnet ID must be entered. The format is as follows:                                                                                         |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           | .. code-block::                                                                                                                                                                         |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           |    [{"subnetID":"27d95**"},{"subnetID":"827bb**"},{"subnetID":"bdd6b**"}]                                                                                                               |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           | Subnet ID not used by the cluster in the same VPC.                                                                                                                                      |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           | How to obtain:                                                                                                                                                                          |
      |                 |                 |                           |                                                                                                                                                                                         |
      |                 |                 |                           | Log in to the VPC console. In the navigation pane, choose **Virtual Private Cloud** > **Subnets**. Click the target subnet name and copy the **Subnet ID** on the **Summary** tab page. |
      +-----------------+-----------------+---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create a NetworkAttachmentDefinition.

   **kubectl create -f networkattachment-test.yaml**

   If information similar to the following is displayed, the NetworkAttachmentDefinition has been created.

   .. code-block::

      networkattachmentdefinition.k8s.cni.cncf.io/example created

#. Create a Deployment workload and associate it with the newly created container network configuration.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx
      spec:
        replicas: 3
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
              yangtse.io/network: "example"  # Name of the custom container network configuration, which can be used to obtain all pods associated with the container network configuration by label
            annotations:
              yangtse.io/network: "example"  # Name of the custom container network configuration
          spec:
            containers:
              - name: container-0
                image: nginx:alpine
                resources:
                  limits:
                    cpu: 100m
                    memory: 200Mi
                  requests:
                    cpu: 100m
                    memory: 200Mi
            imagePullSecrets:
              - name: default-secret

   -  **yangtse.io/network**: name of the specified custom container network configuration. Only a container network configuration that is not associated with any namespace can be specified. Add this parameter to the label so that you can use the label to obtain all pods associated with this container network configuration.

.. _cce_10_0196__section2314125415245:

Deleting a Network Configuration
--------------------------------

You can delete the new network configuration or view its YAML file.

.. note::

   Before deleting a network configuration, delete the container corresponding to the configuration. Otherwise, the deletion fails.

   #. Run the following command to filter the pod that uses the configuration in the cluster (**example** is an example configuration name and you should replace it):

      .. code-block::

         kubectl get po -A -o=jsonpath="{.items[?(@.metadata.annotations.cni\.yangtse\.io/network-status=='[{\"name\":\"example\"}]')]['metadata.namespace', 'metadata.name']}"

      The command output contains the pod name and namespace associated with the configuration.

   #. Delete the owner of the pod. The owner may be a Deployment, StatefulSet, DaemonSet, or Job.
