:original_name: cce_10_0386.html

.. _cce_10_0386:

Configuring Labels and Annotations
==================================

Pod Annotations
---------------

CCE allows you to add annotations to a YAML file to realize some advanced pod functions. The following table describes the annotations you can add.

.. _cce_10_0386__table194691458405:

.. table:: **Table 1** Pod annotations

   +----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | Annotation                                   | Description                                                                                                                                                                           | Default Value         |
   +==============================================+=======================================================================================================================================================================================+=======================+
   | kubernetes.AOM.log.stdout                    | Standard output parameter. If not specified, the standard log output of all containers is reported to AOM. You can collect stdout logs from certain containers or ignore them at all. | None                  |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | Example:                                                                                                                                                                              |                       |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | -  Collecting none of the stdout logs:                                                                                                                                                |                       |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              |    .. code-block::                                                                                                                                                                    |                       |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              |       kubernetes.AOM.log.stdout: '[]'                                                                                                                                                 |                       |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | -  Collecting stdout logs of container-1 and container-2:                                                                                                                             |                       |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              |    .. code-block::                                                                                                                                                                    |                       |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              |       kubernetes.AOM.log.stdout: '["container-1","container-2"]'                                                                                                                      |                       |
   +----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | metrics.alpha.kubernetes.io/custom-endpoints | Parameter for reporting AOM monitoring metrics that you specify.                                                                                                                      | None                  |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | For details, see :ref:`Monitoring Custom Metrics on AOM <cce_10_0201>`.                                                                                                               |                       |
   +----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | prometheus.io/scrape                         | Parameter for reporting Prometheus metrics. If the value is **true**, the current workload reports the monitoring metrics.                                                            | None                  |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | For details, see :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>`.                                                                                |                       |
   +----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | prometheus.io/path                           | URL for Prometheus to collect data.                                                                                                                                                   | /metrics              |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | For details, see :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>`.                                                                                |                       |
   +----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | prometheus.io/port                           | Endpoint port number for Prometheus to collect data.                                                                                                                                  | None                  |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | For details, see :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>`.                                                                                |                       |
   +----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | prometheus.io/scheme                         | Protocol used by Prometheus to collect data. The value can be **http** or **https**.                                                                                                  | None                  |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | For details, see :ref:`Monitoring Custom Metrics Using Cloud Native Cluster Monitoring <cce_10_0373>`.                                                                                |                       |
   +----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | kubernetes.io/ingress-bandwidth              | Ingress bandwidth of a pod.                                                                                                                                                           | None                  |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | For details, see :ref:`Configuring QoS for a Pod <cce_10_0382>`.                                                                                                                      |                       |
   +----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | kubernetes.io/egress-bandwidth               | Egress bandwidth of a pod.                                                                                                                                                            | None                  |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | For details, see :ref:`Configuring QoS for a Pod <cce_10_0382>`.                                                                                                                      |                       |
   +----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

Pod Labels
----------

When you create a workload on the console, the following labels are added to the pod by default. The value of **app** is the workload name.

Example YAML:

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

You can also add other labels to the pod for affinity and anti-affinity scheduling. In the following figure, three pod labels (release, env, and role) are defined for workload APP 1, APP 2, and APP 3. The values of these labels vary with workload.

-  APP 1: [release:alpha;env:development;role:frontend]
-  APP 2: [release:beta;env:testing;role:frontend]
-  APP 3: [release:alpha;env:production;role:backend]


.. figure:: /_static/images/en-us_image_0000001981436409.png
   :alt: **Figure 1** Label example

   **Figure 1** Label example

For example, if **key/value** is set to **role/backend**, APP 3 will be selected for affinity scheduling. For details, see :ref:`Workload Affinity (podAffinity) <cce_10_0232__section3218151791419>`.
