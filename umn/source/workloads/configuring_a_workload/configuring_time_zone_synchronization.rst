:original_name: cce_10_0354.html

.. _cce_10_0354:

Configuring Time Zone Synchronization
=====================================

When creating a workload, you can configure containers to use the same time zone as the node. You can enable time zone synchronization when creating a workload.

The time zone synchronization function depends on the local disk (hostPath) mounted to the container. After time zone synchronization is enabled, **/etc/localtime** of the node is mounted to **/etc/localtime** of the container in HostPath mode, in this way, the node and container use the same time zone configuration file.

.. code-block::

   kind: Deployment
   apiVersion: apps/v1
   metadata:
     name: test
     namespace: default
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: test
     template:
       metadata:
         labels:
           app: test
       spec:
         volumes:
           - name: vol-162979628557461404
             hostPath:
               path: /etc/localtime
               type: ''
         containers:
           - name: container-0
             image: 'nginx:alpine'
             volumeMounts:
               - name: vol-162979628557461404
                 readOnly: true
                 mountPath: /etc/localtime
             imagePullPolicy: IfNotPresent
         imagePullSecrets:
           - name: default-secret
