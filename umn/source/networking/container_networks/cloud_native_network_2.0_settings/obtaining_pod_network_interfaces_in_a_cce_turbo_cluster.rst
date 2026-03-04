:original_name: cce_10_1062.html

.. _cce_10_1062:

Obtaining Pod Network Interfaces in a CCE Turbo Cluster
=======================================================

In a CCE Turbo cluster, the network model add-on automatically binds an independent network interface or supplementary network interface to each pod. If there are a large number of pods in the cluster, the number of network interfaces also increases accordingly. In some scenarios, you may need to obtain network interface details of pods based on service requirements. For example, during network monitoring or troubleshooting, you can obtain the network interface ID of a pod to more accurately locate the fault. In some scenarios, you can specify custom configuration management based on network interface details.

This section describes how to obtain network interface details of pods from the pod annotations. This function supports the following configurations:

-  Global configuration: Network interface details of all pods will be displayed. This means you can enable **enablePodNetworkStatus** in **YangtseConfiguration**. For details, see :ref:`Global Configuration <cce_10_1062__section1875674445611>`.
-  On-demand configuration: You can configure the **yangtse.io/enable-pod-network-status** annotation for a pod to display its network interface details. For details, see :ref:`On-demand Configuration <cce_10_1062__section2061855119567>`.

The following table lists the logic of displaying network interface details when you configure switches at different levels.

+----------------------+-------------------------+-------------------------------------+
| Global Configuration | On-demand Configuration | Network Interface Details Displayed |
+======================+=========================+=====================================+
| Enabled (**true**)   | Enabled (**true**)      | Yes                                 |
+----------------------+-------------------------+-------------------------------------+
| Enabled (**true**)   | Disabled (**false**)    | No                                  |
+----------------------+-------------------------+-------------------------------------+
| Enabled (**true**)   | Not set                 | Yes                                 |
+----------------------+-------------------------+-------------------------------------+
| Disabled (**false**) | Enabled (**true**)      | Yes                                 |
+----------------------+-------------------------+-------------------------------------+
| Disabled (**false**) | Disabled (**false**)    | No                                  |
+----------------------+-------------------------+-------------------------------------+
| Disabled (**false**) | Not set                 | No                                  |
+----------------------+-------------------------+-------------------------------------+

Constraints
-----------

-  This function is only available in CCE Turbo clusters of the following versions:

   -  v1.27: v1.27.16-r50 or later
   -  v1.28: v1.28.15-r40 or later
   -  v1.29: v1.29.15-r0 or later
   -  v1.30: v1.30.14-r0 or later
   -  v1.31: v1.31.10-r0 or later
   -  v1.32: v1.32.6-r0 or later
   -  v1.33 or later

-  This function affects the pod startup performance when a large number of pods are created in a batch.

.. _cce_10_1062__section1875674445611:

Global Configuration
--------------------

.. note::

   After the global configuration is enabled or disabled, network interface details of existing pods can be displayed or not displayed only after the pods are restarted.

#. Connect to your cluster using kubectl. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Edit the YangtseConfiguration resource:

   .. code-block::

      kubectl patch yc default --type='merge' -p '{"spec":{"eni":{"enablePodNetworkStatus":true}}}'

   .. note::

      To disable the global configuration, change the preceding command to the following:

      .. code-block::

         kubectl patch yc default --type='merge' -p '{"spec":{"eni":{"enablePodNetworkStatus":false}}}'

#. Create a file named **nginx.yaml**. You can change the file name as required.

   .. code-block::

      vi nginx.yaml

   Example YAML:

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        name: nginx
      spec:
        containers:
        - image: nginx:latest
          name: container-0
          resources:
            limits:
              cpu: 100m
              memory: 200Mi
            requests:
              cpu: 100m
              memory: 200Mi
        imagePullSecrets:
        - name: default-secret

#. Create a pod.

   .. code-block::

      kubectl create -f nginx.yaml

   Command output:

   .. code-block::

      pod/nginx created

#. Query the pod status. Network interface details are automatically obtained and displayed in the pod annotation.

   .. code-block::

      kubectl get pod nginx -o yaml

   Command output:

   |image1|

   In this command:

   -  **name**: indicates the container network configuration name used by the pod.
   -  **portID**: indicates the ID of the network interface used by the pod.
   -  **ips**: indicates the IP address of the network interface.

.. _cce_10_1062__section2061855119567:

On-demand Configuration
-----------------------

#. Connect to your cluster using kubectl. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a file named **nginx.yaml**. You can change the file name as required.

   .. code-block::

      vi nginx.yaml

   Example YAML:

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        name: nginx
        annotations:
          yangtse.io/enable-pod-network-status: "true"  # Displays network interface details.
      spec:
        containers:
        - image: nginx:latest
          name: container-0
          resources:
            limits:
              cpu: 100m
              memory: 200Mi
            requests:
              cpu: 100m
              memory: 200Mi
        imagePullSecrets:
        - name: default-secret

#. Create a pod.

   .. code-block::

      kubectl create -f nginx.yaml

   Command output:

   .. code-block::

      pod/nginx created

#. Query the pod status. Network interface details are automatically obtained and displayed in the pod annotation.

   .. code-block::

      kubectl get pod nginx -o yaml

   Command output:

   |image2|

   In this command:

   -  **name**: indicates the container network configuration name used by the pod.
   -  **portID**: indicates the ID of the network interface used by the pod.
   -  **ips**: indicates the IP address of the network interface.

.. |image1| image:: /_static/images/en-us_image_0000002516199533.png
.. |image2| image:: /_static/images/en-us_image_0000002484119560.png
