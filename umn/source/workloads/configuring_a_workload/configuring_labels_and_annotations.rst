:original_name: cce_10_0386.html

.. _cce_10_0386:

Configuring Labels and Annotations
==================================

To support multi-dimensional metadata management, Kubernetes offers `labels <https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/>`__ and `annotations <https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/>`__. They both attach metadata to resources in the format of key-value pairs, but their purposes and use cases differ significantly.

-  Labels are used for identifying and classifying Kubernetes objects. They follow a structured key-value format (for example, **app=frontend**) and can be used to efficiently select resources using selectors. They directly influence resource scheduling and management. The key and value of a label must comply with naming rules and typically represent persistent attributes of a resource, such as version.
-  Annotations serve to extend functionality and store additional metadata. They support flexible data formats (such as JSON or multi-line text). They are not used to select resources and do not influence scheduling.

Both labels and annotations apply to various Kubernetes resource types. This section will specifically examine their use in pods.

Pod Labels
----------

Pod labels enable intelligent resource association. Once labels are added to pods, other Kubernetes resources can accurately identify and be associated with these pods using label selectors. Label keys and values must follow specific naming rules. For details, see `Syntax and character set <https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set>`__. For example, you can add the following label to two workloads (App 1 and App 2), respectively:

-  App 1: **release=alpha**
-  App 2: **release=beta**

When App 3 is created with a workload affinity of **release=alpha**, Kubernetes will attempt to schedule App 3 onto the same node as App 1 or another node that matches this affinity. This enables intelligent collaborative deployment between App 1 and App 3. For details, see :ref:`Configuring Workload Affinity or Anti-affinity Scheduling (podAffinity or podAntiAffinity) <cce_10_0893>`.

You can add labels to a pod using either the console or YAML.

Adding a Pod Label Using the Console
------------------------------------

When creating or upgrading a workload on the CCE console, you can add a **pod label** by clicking **Labels and Annotations** in the **Advanced Settings** area. This label helps identify the pod within the cluster. During the workload creation, CCE will add the label shown in the figure below to the workload pod by default. The value of **app** is the workload name.

Adding a Pod Label Using YAML
-----------------------------

When creating or upgrading a workload, you can add a pod label in the following area of the YAML file:

.. code-block::

   ...
   spec:
     selector:
       matchLabels:
         app: nginx
         version: v1
     template:
       metadata:
         labels:
           app: nginx
           version: v1
       spec:
         ...

Pod Annotations
---------------

CCE provides some advanced functions for pods. These functions can be implemented by adding annotations to the YAML files. You can add annotations to a pod using either the console or YAML.

Adding a Pod Annotation Using the Console
-----------------------------------------

When creating or upgrading a workload on the CCE console, you can add a **pod annotation** by clicking **Labels and Annotations** in the **Advanced Settings** area to enable advanced functions for the pod.

For example, to disable the collection of container standard output logs for a pod, set the pod annotation to **kubernetes.AOM.log.stdout='[]'** and click **Confirm**.

Adding a Pod Annotation Using YAML
----------------------------------

When creating or upgrading a workload using YAML, you can use the **annotations** parameter to enable advanced pod functions.

For example, you can use **annotations** to disable the collection of container standard output logs for a pod:

.. code-block::

   ...
   spec:
     replicas: 1     # Number of pods
     selector:
       matchLabels:  # Selector for selecting resources with specific labels
         app: nginx
     template:
       metadata:
         labels:     # Labels
           app: nginx
         annotations:
           kubernetes.AOM.log.stdout: '[]'
   ...

Typical Pod Annotations
-----------------------

:ref:`Table 1 <cce_10_0386__table19757101213714>` provides some typically used annotations. You can add annotations for pods as required.

.. _cce_10_0386__table19757101213714:

.. table:: **Table 1** Pod annotations

   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | Annotation                                   | Description                                                                                                                                                                                                                                                                                                                                   | Default Value         |
   +==============================================+===============================================================================================================================================================================================================================================================================================================================================+=======================+
   | kubernetes.AOM.log.stdout                    | The standard output log collection setting for containers. If left unspecified, the standard output logs from all containers will be automatically reported to AOM by default. You can customize this annotation to collect standard output logs only from specified containers or disable standard output log collection for all containers. | N/A                   |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              | Example:                                                                                                                                                                                                                                                                                                                                      |                       |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              | -  Disabling standard output log collection for all containers:                                                                                                                                                                                                                                                                               |                       |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              |    .. code-block::                                                                                                                                                                                                                                                                                                                            |                       |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              |       kubernetes.AOM.log.stdout: '[]'                                                                                                                                                                                                                                                                                                         |                       |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              | -  Collecting standard output logs from container-1 and container-2:                                                                                                                                                                                                                                                                          |                       |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              |    .. code-block::                                                                                                                                                                                                                                                                                                                            |                       |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              |       kubernetes.AOM.log.stdout: '["container-1","container-2"]'                                                                                                                                                                                                                                                                              |                       |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | metrics.alpha.kubernetes.io/custom-endpoints | The AOM metric reporting setting, which allows specified metrics to be sent to AOM.                                                                                                                                                                                                                                                           | N/A                   |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              | For details, see :ref:`Monitoring Custom Metrics on AOM <cce_10_0201>`.                                                                                                                                                                                                                                                                       |                       |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | prometheus.io/scrape                         | The Prometheus metric reporting setting, which allows specified metrics to be sent to Prometheus. If set to **true**, the metrics of the specified workload will be reported to Prometheus.                                                                                                                                                   | N/A                   |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              | For details, see :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>`.                                                                                                                                                                                                                                        |                       |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | prometheus.io/path                           | URL for Prometheus to collect data.                                                                                                                                                                                                                                                                                                           | /metrics              |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              | For details, see :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>`.                                                                                                                                                                                                                                        |                       |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | prometheus.io/port                           | Endpoint port number for Prometheus to collect data.                                                                                                                                                                                                                                                                                          | N/A                   |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              | For details, see :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>`.                                                                                                                                                                                                                                        |                       |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | prometheus.io/scheme                         | Protocol used by Prometheus to collect data. The value can be **http** or **https**.                                                                                                                                                                                                                                                          | N/A                   |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              | For details, see :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>`.                                                                                                                                                                                                                                        |                       |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | kubernetes.io/ingress-bandwidth              | The ingress bandwidth of a pod. It controls the rate at which the pod receives data to ensure that the pod can process external requests.                                                                                                                                                                                                     | N/A                   |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              | For details, see :ref:`Configuring QoS for a Pod <cce_10_0382>`.                                                                                                                                                                                                                                                                              |                       |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | kubernetes.io/egress-bandwidth               | The egress bandwidth of a pod. It controls the rate at which the pod sends data to external systems. This affects the efficiency of communication between the pod and external services or users.                                                                                                                                             | N/A                   |
   |                                              |                                                                                                                                                                                                                                                                                                                                               |                       |
   |                                              | For details, see :ref:`Configuring QoS for a Pod <cce_10_0382>`.                                                                                                                                                                                                                                                                              |                       |
   +----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

Helpful Links
-------------

-  For details about how to configure workload affinity or anti-affinity, see :ref:`Configuring Workload Affinity or Anti-affinity Scheduling (podAffinity or podAntiAffinity) <cce_10_0893>`.
-  For details about how to configure an EIP for a pod through annotations, see :ref:`Configuring an EIP for a Pod in a CCE Turbo Cluster <cce_10_0734>`.
