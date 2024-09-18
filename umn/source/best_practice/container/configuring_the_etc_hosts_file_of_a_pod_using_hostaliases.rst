:original_name: cce_bestpractice_00226.html

.. _cce_bestpractice_00226:

Configuring the /etc/hosts File of a Pod Using hostAliases
==========================================================

Application Scenarios
---------------------

If DNS or other related settings are inappropriate, you can use **hostAliases** to overwrite the resolution of the hostname at the pod level when adding entries to the **/etc/hosts** file of the pod.

Procedure
---------

#. Use kubectl to connect to the cluster.

#. Create the **hostaliases-pod.yaml** file.

   **vi hostaliases-pod.yaml**

   The field in bold in the YAML file indicates the image name and tag. You can replace the example value as required.

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        name: hostaliases-pod
      spec:
        hostAliases:
        - ip: 127.0.0.1
          hostnames:
          - foo.local
          - bar.local
        - ip: 10.1.2.3
          hostnames:
          - foo.remote
          - bar.remote
        containers:
          - name: cat-hosts
            image: tomcat:9-jre11-slim
            lifecycle:
              postStart:
                exec:
                  command:
                    - cat
                    - /etc/hosts
        imagePullSecrets:
          - name: default-secret

   .. table:: **Table 1** pod field description

      +------------+-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter  | Mandatory | Description                                                                                                                                                |
      +============+===========+============================================================================================================================================================+
      | apiVersion | Yes       | API version number                                                                                                                                         |
      +------------+-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kind       | Yes       | Type of the object to be created                                                                                                                           |
      +------------+-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | metadata   | Yes       | Metadata definition of a resource object                                                                                                                   |
      +------------+-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | name       | Yes       | Name of a pod                                                                                                                                              |
      +------------+-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | spec       | Yes       | Detailed description of the pod. For details, see :ref:`Table 2 <cce_bestpractice_00226__en-us_topic_0226102200_en-us_topic_0179003345_table33531919193>`. |
      +------------+-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_bestpractice_00226__en-us_topic_0226102200_en-us_topic_0179003345_table33531919193:

   .. table:: **Table 2** spec field description

      +-------------+-----------+----------------------------------------------------------------------------------------------------------------------------+
      | Parameter   | Mandatory | Description                                                                                                                |
      +=============+===========+============================================================================================================================+
      | hostAliases | Yes       | Host alias                                                                                                                 |
      +-------------+-----------+----------------------------------------------------------------------------------------------------------------------------+
      | containers  | Yes       | For details, see :ref:`Table 3 <cce_bestpractice_00226__en-us_topic_0226102200_en-us_topic_0179003345_table196127172016>`. |
      +-------------+-----------+----------------------------------------------------------------------------------------------------------------------------+

   .. _cce_bestpractice_00226__en-us_topic_0226102200_en-us_topic_0179003345_table196127172016:

   .. table:: **Table 3** containers field description

      ========= ========= ====================
      Parameter Mandatory Description
      ========= ========= ====================
      name      Yes       Container name
      image     Yes       Container image name
      lifecycle No        Lifecycle
      ========= ========= ====================

#. Create a pod.

   **kubectl create -f hostaliases-pod.yaml**

   If information similar to the following is displayed, the pod is created.

   .. code-block::

      pod/hostaliases-pod created

#. Query the pod status.

   **kubectl get pod hostaliases-pod**

   If the pod is in the **Running** state, the pod is successfully created.

   .. code-block::

      NAME                  READY          STATUS       RESTARTS      AGE
      hostaliases-pod       1/1            Running      0             16m

#. Check whether the **hostAliases** functions properly.

   **docker ps \|grep hostaliases-pod**

   **docker exec -ti Container ID /bin/sh**

   |image1|

.. |image1| image:: /_static/images/en-us_image_0000001981274553.png
