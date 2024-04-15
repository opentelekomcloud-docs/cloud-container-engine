:original_name: cce_faq_00235.html

.. _cce_faq_00235:

How Can I Achieve Compatibility Between ExtendPathMode and Kubernetes client-go?
================================================================================

Application Scenarios
---------------------

The Kubernetes pod structure does not contain **ExtendPathMode**. Therefore, when a user calls the API for creating a pod or deployment by using client-go, the created pod does not contain **ExtendPathMode**. CCE provides a solution to ensure compatibility with the Kubernetes client-go.

Solution
--------

.. important::

   -  When creating a pod, you need to add **kubernetes.io/extend-path-mode** to **annotation** of the pod.
   -  When creating a Deployment, you need to add **kubernetes.io/extend-path-mode** to **kubernetes.io/extend-path-mode** in the template.

The following is an example YAML of creating a pod. After the **kubernetes.io/extend-path-mode** keyword is added to **annotation**, the **containername**, **name**, and **mountpath** fields are matched, and the corresponding **extendpathmode** is added to **volumeMount**.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: test-8b59d5884-96vdz
     generateName: test-8b59d5884-
     namespace: default
     selfLink: /api/v1/namespaces/default/pods/test-8b59d5884-96vdz
     labels:
       app: test
       pod-template-hash: 8b59d5884
     annotations:
       kubernetes.io/extend-path-mode: '[{"containername":"container-0","name":"vol-156738843032165499","mountpath":"/tmp","extendpathmode":"PodUID"}]'
       metrics.alpha.kubernetes.io/custom-endpoints: '[{"api":"","path":"","port":"","names":""}]'
     ownerReferences:
       - apiVersion: apps/v1
         kind: ReplicaSet
         name: test-8b59d5884
         uid: 2633020b-cd23-11e9-8f83-fa163e592534
         controller: true
         blockOwnerDeletion: true
   spec:
     volumes:
       - name: vol-156738843032165499
         hostPath:
           path: /tmp
           type: ''
       - name: default-token-4s959
         secret:
           secretName: default-token-4s959
           defaultMode: 420
     containers:
       - name: container-0
         image: 'nginx:latest'
         env:
           - name: PAAS_APP_NAME
             value: test
           - name: PAAS_NAMESPACE
             value: default
           - name: PAAS_PROJECT_ID
             value: b6315dd3d0ff4be5b31a963256794989
         resources:
           limits:
             cpu: 250m
             memory: 512Mi
           requests:
             cpu: 250m
             memory: 512Mi
         volumeMounts:
           - name: vol-156738843032165499
             mountPath: /tmp
             extendPathMode: PodUID
           - name: default-token-4s959
             readOnly: true
             mountPath: /var/run/secrets/kubernetes.io/serviceaccount
         terminationMessagePath: /dev/termination-log
         terminationMessagePolicy: File
         imagePullPolicy: Always
     restartPolicy: Always
     terminationGracePeriodSeconds: 30
     dnsPolicy: ClusterFirst
     serviceAccountName: default
     serviceAccount: default
     nodeName: 192.168.0.24
     securityContext: {}
     imagePullSecrets:
       - name: default-secret
       - name: default-secret
     affinity: {}
     schedulerName: default-scheduler
     tolerations:
       - key: node.kubernetes.io/not-ready
         operator: Exists
         effect: NoExecute
         tolerationSeconds: 300
       - key: node.kubernetes.io/unreachable
         operator: Exists
         effect: NoExecute
         tolerationSeconds: 300
     priority: 0
     dnsConfig:
       options:
         - name: timeout
           value: ''
         - name: ndots
           value: '5'
         - name: single-request-reopen
     enableServiceLinks: true

.. table:: **Table 1** Descriptions of key parameters

   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Type                  | Description                                                                                                                            |
   +=======================+=======================+========================================================================================================================================+
   | containername         | String                | Name of a container.                                                                                                                   |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | name                  | String                | Name of a volume.                                                                                                                      |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | mountpath             | String                | Mount path.                                                                                                                            |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | extendpathmode        | String                | A third-level directory is added to the created volume directory/subdirectory to facilitate the obtaining of a single pod output file. |
   |                       |                       |                                                                                                                                        |
   |                       |                       | The following types are supported.                                                                                                     |
   |                       |                       |                                                                                                                                        |
   |                       |                       | -  **None**: The extended path is not configured.                                                                                      |
   |                       |                       | -  **PodUID**: ID of a pod.                                                                                                            |
   |                       |                       | -  **PodName**: Name of a pod.                                                                                                         |
   |                       |                       | -  **PodUID/ContainerName**: ID of a pod or name of a container.                                                                       |
   |                       |                       | -  **PodName/ContainerName**: Name of a pod or container.                                                                              |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------+
