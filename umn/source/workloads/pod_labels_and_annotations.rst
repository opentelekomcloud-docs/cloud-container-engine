:original_name: cce_10_0386.html

.. _cce_10_0386:

Pod Labels and Annotations
==========================

Pod Annotations
---------------

CCE allows you to add annotations to a YAML file to realize some advanced pod functions. The following table describes the annotations you can add.

.. table:: **Table 1** Pod annotations

   +----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | Annotation                                   | Description                                                                                                                                                                           | Default Value         |
   +==============================================+=======================================================================================================================================================================================+=======================+
   | kubernetes.AOM.log.stdout                    | Standard output parameter. If not specified, the standard log output of all containers is reported to AOM. You can collect stdout logs from certain containers or ignore them at all. | ``-``                 |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | Example:                                                                                                                                                                              |                       |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | -  Collecting none of the stdout logs:                                                                                                                                                |                       |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              |    kubernetes.AOM.log.stdout: '[]'                                                                                                                                                    |                       |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | -  Collecting stdout logs of container-1 and container-2:                                                                                                                             |                       |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              |    kubernetes.AOM.log.stdout: '["container-1","container-2"]'                                                                                                                         |                       |
   +----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
   | metrics.alpha.kubernetes.io/custom-endpoints | Parameter for reporting AOM monitoring metrics that you specify.                                                                                                                      | ``-``                 |
   |                                              |                                                                                                                                                                                       |                       |
   |                                              | For details, see :ref:`Custom Monitoring <cce_10_0201>`.                                                                                                                              |                       |
   +----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

Pod Labels
----------

When you create a workload on the console, the following labels are added to the pod by default. The value of **app** is the workload name. You can add labels as required.

The pod labels added here will be added to the **selector.matchLabels** parameter in the workload definition. The following is an example YAML file:

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
