:original_name: cce_10_0637.html

.. _cce_10_0637:

Overview
========

Introduction
------------

Some applications require additional storage, but whether the data is still available after a restart is not important. For example, although cache services are limited by memory size, cache services can move infrequently used data to storage slower than memory. As a result, overall performance is not impacted significantly. Other applications require read-only data injected as files, such as configuration data or secrets.

`Ephemeral volumes <https://kubernetes.io/docs/concepts/storage/ephemeral-volumes/>`__ (EVs) in Kubernetes are designed for the above scenario. EVs are created and deleted together with pods following the pod lifecycle.

Common EVs in Kubernetes:

-  :ref:`emptyDir <cce_10_0638>`: empty at pod startup, with storage coming locally from the kubelet base directory (usually the root disk) or memory. emptyDir is allocated from the `EV of the node <https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#setting-requests-and-limits-for-local-ephemeral-storage>`__. If data from other sources (such as log files or image tiering data) occupies the ephemeral storage, the storage capacity may be insufficient.
-  :ref:`ConfigMap <cce_10_0015>`: Kubernetes data of the ConfigMap type is mounted to pods as data volumes.
-  :ref:`Secret <cce_10_0016>`: Kubernetes data of the Secret type is mounted to pods as data volumes.

emptyDir Types
--------------

CCE provides the following emptyDir types:

-  :ref:`Using a Temporary Path <cce_10_0638>`: Kubernetes-native emptyDir type. Its lifecycle is the same as that of a pod. Memory can be specified as the storage medium. When the pod is deleted, the emptyDir volume is deleted and its data is lost.
-  :ref:`Using a Local EV <cce_10_0726>`: Local data disks in a node form a :ref:`storage pool <cce_10_0725>` (VolumeGroup) through LVM. LVs are created as the storage medium of emptyDir and mounted to containers. LVs deliver better performance than the default storage medium of emptyDir.

Constraints
-----------

-  Local EVs are supported only when the cluster version is v1.21.2-r0 or later and the Everest add-on version is 1.2.29 or later.
-  Do not manually delete the corresponding storage pool or detach data disks from the node. Otherwise, exceptions such as data loss may occur.
-  Ensure that the **/var/lib/kubelet/pods/** directory is not mounted to the pod on the node. Otherwise, the pod, mounted with such volumes, may fail to be deleted.
