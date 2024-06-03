:original_name: cce_bestpractice_00284.html

.. _cce_bestpractice_00284:

Enabling Automatic Topology for EVS Disks When Nodes Are Deployed in Different AZs (csi-disk-topology)
======================================================================================================

Background
----------

EVS disks cannot be attached to a node deployed in another AZ. For example, the EVS disks in AZ 1 cannot be attached to a node in AZ 2. If the storage class csi-disk is used for StatefulSets, when a StatefulSet is scheduled, a PVC and a PV are created immediately (an EVS disk is created along with the PV), and then the PVC is bound to the PV. However, when the cluster nodes are located in multiple AZs, the EVS disk created by the PVC and the node to which the pods are scheduled may be in different AZs. As a result, the pods fail to be scheduled.

|image1|

Solution
--------

CCE provides a storage class named **csi-disk-topology**, which is a late-binding EVS disk type. When you use this storage class to create a PVC, no PV will be created in pace with the PVC. Instead, the PV is created in the AZ of the node where the pod will be scheduled. An EVS disk is then created in the same AZ to ensure that the EVS disk can be attached and the pod can be successfully scheduled.

|image2|

Failed Pod Scheduling Due to csi-disk Used in Cross-AZ Node Deployment
----------------------------------------------------------------------

Create a cluster with three nodes in different AZs.

Use the csi-disk storage class to create a StatefulSet and check whether the workload is successfully created.

.. code-block::

   apiVersion: apps/v1
   kind: StatefulSet
   metadata:
     name: nginx
   spec:
     serviceName: nginx                             # Name of the headless Service
     replicas: 4
     selector:
       matchLabels:
         app: nginx
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
           - name: container-0
             image: nginx:alpine
             resources:
               limits:
                 cpu: 600m
                 memory: 200Mi
               requests:
                 cpu: 600m
                 memory: 200Mi
             volumeMounts:                           # Storage mounted to the pod
             - name:  data
               mountPath: /usr/share/nginx/html      # Mount the storage to /usr/share/nginx/html.
         imagePullSecrets:
           - name: default-secret
     volumeClaimTemplates:
     - metadata:
         name: data
         annotations:
           everest.io/disk-volume-type: SAS
       spec:
         accessModes:
         - ReadWriteOnce
         resources:
           requests:
             storage: 1Gi
         storageClassName: csi-disk

The StatefulSet uses the following headless Service.

.. code-block::

   apiVersion: v1
   kind: Service       # Object type (Service)
   metadata:
     name: nginx
     labels:
       app: nginx
   spec:
     ports:
       - name: nginx     # Name of the port for communication between pods
         port: 80        # Port number for communication between pods
     selector:
       app: nginx        # Select the pod whose label is app:nginx.
     clusterIP: None     # Set this parameter to None, indicating the headless Service.

After the creation, check the PVC and pod status. In the following output, the PVC has been created and bound successfully, and a pod is in the Pending state.

.. code-block::

   # kubectl get pvc -owide
   NAME           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE   VOLUMEMODE
   data-nginx-0   Bound    pvc-04e25985-fc93-4254-92a1-1085ce19d31e   1Gi        RWO            csi-disk       64s   Filesystem
   data-nginx-1   Bound    pvc-0ae6336b-a2ea-4ddc-8f63-cfc5f9efe189   1Gi        RWO            csi-disk       47s   Filesystem
   data-nginx-2   Bound    pvc-aa46f452-cc5b-4dbd-825a-da68c858720d   1Gi        RWO            csi-disk       30s   Filesystem
   data-nginx-3   Bound    pvc-3d60e532-ff31-42df-9e78-015cacb18a0b   1Gi        RWO            csi-disk       14s   Filesystem

   # kubectl get pod -owide
   NAME      READY   STATUS    RESTARTS   AGE     IP             NODE            NOMINATED NODE   READINESS GATES
   nginx-0   1/1     Running   0          2m25s   172.16.0.12    192.168.0.121   <none>           <none>
   nginx-1   1/1     Running   0          2m8s    172.16.0.136   192.168.0.211   <none>           <none>
   nginx-2   1/1     Running   0          111s    172.16.1.7     192.168.0.240   <none>           <none>
   nginx-3   0/1     Pending   0          95s     <none>         <none>          <none>           <none>

The event information of the pod shows that the scheduling fails due to no available node. Two nodes (in AZ 1 and AZ 2) do not have sufficient CPUs, and the created EVS disk is not in the AZ where the third node (in AZ 3) is located. As a result, the pod cannot use the EVS disk.

.. code-block::

   # kubectl describe pod nginx-3
   Name:           nginx-3
   ...
   Events:
     Type     Reason            Age   From               Message
     ----     ------            ----  ----               -------
     Warning  FailedScheduling  111s  default-scheduler  0/3 nodes are available: 3 pod has unbound immediate PersistentVolumeClaims.
     Warning  FailedScheduling  111s  default-scheduler  0/3 nodes are available: 3 pod has unbound immediate PersistentVolumeClaims.
     Warning  FailedScheduling  28s   default-scheduler  0/3 nodes are available: 1 node(s) had volume node affinity conflict, 2 Insufficient cpu.

Check the AZ where the EVS disk created from the PVC is located. It is found that data-nginx-3 is in AZ 1. In this case, the node in AZ 1 has no resources, and only the node in AZ 3 has CPU resources. As a result, the scheduling fails. Therefore, there should be a delay between creating the PVC and binding the PV.

Storage Class for Delayed Binding
---------------------------------

If you check the cluster storage class, you can see that the binding mode of csi-disk-topology is **WaitForFirstConsumer**, indicating that a PV is created and bound when a pod uses the PVC. That is, the PV and the underlying storage resources are created based on the pod information.

.. code-block::

   # kubectl get storageclass
   NAME                PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
   csi-disk            everest-csi-provisioner         Delete          Immediate              true                   156m
   csi-disk-topology   everest-csi-provisioner         Delete          WaitForFirstConsumer   true                   156m
   csi-nas             everest-csi-provisioner         Delete          Immediate              true                   156m
   csi-obs             everest-csi-provisioner         Delete          Immediate              false                  156m

**VOLUMEBINDINGMODE** is displayed if your cluster is v1.19. It is not displayed in clusters of v1.17 or v1.15.

You can also view the binding mode in the csi-disk-topology details.

.. code-block::

   # kubectl describe sc csi-disk-topology
   Name:                  csi-disk-topology
   IsDefaultClass:        No
   Annotations:           <none>
   Provisioner:           everest-csi-provisioner
   Parameters:            csi.storage.k8s.io/csi-driver-name=disk.csi.everest.io,csi.storage.k8s.io/fstype=ext4,everest.io/disk-volume-type=SAS,everest.io/passthrough=true
   AllowVolumeExpansion:  True
   MountOptions:          <none>
   ReclaimPolicy:         Delete
   VolumeBindingMode:     WaitForFirstConsumer
   Events:                <none>

Create PVCs of the csi-disk and csi-disk-topology classes. Observe the differences between these two types of PVCs.

-  csi-disk

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name:  disk
        annotations:
          everest.io/disk-volume-type: SAS
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 10Gi
        storageClassName: csi-disk        # StorageClass

-  csi-disk-topology

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name:  topology
        annotations:
          everest.io/disk-volume-type: SAS
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 10Gi
        storageClassName: csi-disk-topology        # StorageClass

View the PVC details. As shown below, the csi-disk PVC is in Bound state and the csi-disk-topology PVC is in Pending state.

.. code-block::

   # kubectl create -f pvc1.yaml
   persistentvolumeclaim/disk created
   # kubectl create -f pvc2.yaml
   persistentvolumeclaim/topology created
   # kubectl get pvc
   NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS        AGE
   disk           Bound     pvc-88d96508-d246-422e-91f0-8caf414001fc   10Gi       RWO            csi-disk            18s
   topology       Pending                                                                        csi-disk-topology   2s

View details about the csi-disk-topology PVC. You can see that "waiting for first consumer to be created before binding" is displayed in the event, indicating that the PVC is bound after the consumer (pod) is created.

.. code-block::

   # kubectl describe pvc topology
   Name:          topology
   Namespace:     default
   StorageClass:  csi-disk-topology
   Status:        Pending
   Volume:
   Labels:        <none>
   Annotations:   everest.io/disk-volume-type: SAS
   Finalizers:    [kubernetes.io/pvc-protection]
   Capacity:
   Access Modes:
   VolumeMode:    Filesystem
   Used By:       <none>
   Events:
     Type    Reason                Age               From                         Message
     ----    ------                ----              ----                         -------
     Normal  WaitForFirstConsumer  5s (x3 over 30s)  persistentvolume-controller  waiting for first consumer to be created before binding

Create a workload that uses the PVC. Set the PVC name to **topology**.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 1
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - image: nginx:alpine
           name: container-0
           volumeMounts:
           - mountPath: /tmp                                # Mount path
             name: topology-example
         restartPolicy: Always
         volumes:
         - name: topology-example
           persistentVolumeClaim:
             claimName:  topology                       # PVC name

After the PVC is created, check the PVC details. You can see that the PVC is bound successfully.

.. code-block::

   # kubectl describe pvc topology
   Name:          topology
   Namespace:     default
   StorageClass:  csi-disk-topology
   Status:        Bound
   ....
   Used By:       nginx-deployment-fcd9fd98b-x6tbs
   Events:
     Type    Reason                 Age                   From                                                                                                  Message
     ----    ------                 ----                  ----                                                                                                  -------
     Normal  WaitForFirstConsumer   84s (x26 over 7m34s)  persistentvolume-controller                                                                           waiting for first consumer to be created before binding
     Normal  Provisioning           54s                   everest-csi-provisioner_everest-csi-controller-7965dc48c4-5k799_2a6b513e-f01f-4e77-af21-6d7f8d4dbc98  External provisioner is provisioning volume for claim "default/topology"
     Normal  ProvisioningSucceeded  52s                   everest-csi-provisioner_everest-csi-controller-7965dc48c4-5k799_2a6b513e-f01f-4e77-af21-6d7f8d4dbc98  Successfully provisioned volume pvc-9a89ea12-4708-4c71-8ec5-97981da032c9

Using csi-disk-topology in Cross-AZ Node Deployment
---------------------------------------------------

The following uses csi-disk-topology to create a StatefulSet with the same configurations used in the preceding example.

.. code-block::

     volumeClaimTemplates:
     - metadata:
         name: data
         annotations:
           everest.io/disk-volume-type: SAS
       spec:
         accessModes:
         - ReadWriteOnce
         resources:
           requests:
             storage: 1Gi
         storageClassName: csi-disk-topology

After the creation, check the PVC and pod status. As shown in the following output, the PVC and pod can be created successfully. The nginx-3 pod is created on the node in AZ 3.

.. code-block::

   # kubectl get pvc -owide
   NAME           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS        AGE   VOLUMEMODE
   data-nginx-0   Bound    pvc-43802cec-cf78-4876-bcca-e041618f2470   1Gi        RWO            csi-disk-topology   55s   Filesystem
   data-nginx-1   Bound    pvc-fc942a73-45d3-476b-95d4-1eb94bf19f1f   1Gi        RWO            csi-disk-topology   39s   Filesystem
   data-nginx-2   Bound    pvc-d219f4b7-e7cb-4832-a3ae-01ad689e364e   1Gi        RWO            csi-disk-topology   22s   Filesystem
   data-nginx-3   Bound    pvc-b54a61e1-1c0f-42b1-9951-410ebd326a4d   1Gi        RWO            csi-disk-topology   9s    Filesystem

   # kubectl get pod -owide
   NAME      READY   STATUS    RESTARTS   AGE   IP             NODE            NOMINATED NODE   READINESS GATES
   nginx-0   1/1     Running   0          65s   172.16.1.8     192.168.0.240   <none>           <none>
   nginx-1   1/1     Running   0          49s   172.16.0.13    192.168.0.121   <none>           <none>
   nginx-2   1/1     Running   0          32s   172.16.0.137   192.168.0.211   <none>           <none>
   nginx-3   1/1     Running   0          19s   172.16.1.9     192.168.0.240   <none>           <none>

.. |image1| image:: /_static/images/en-us_image_0000001898024061.png
.. |image2| image:: /_static/images/en-us_image_0000001897904533.png
