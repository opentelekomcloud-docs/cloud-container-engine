:original_name: cce_bulletin_0003.html

.. _cce_bulletin_0003:

Kubernetes Version Support Mechanism
====================================

This section describes the Kubernetes version support mechanism of CCE.

Cluster Version Description
---------------------------

**Version number**: The format is **x.y.z-r{n}**, where **x.y** is the major version and **z** is the minor version. If the version number is followed by **-r{n}**, the version is a patch version, for example, v1.15.11-r1.

|image1|

.. note::

   Starting from Kubernetes 1.21, CCE only displays the major version number, for example, v1.21.

**Offline**: After a version is brought offline, a cluster of this version cannot be created on the CCE console and no new features will be released for the clusters of this version.

**Disuse**: After a version is disused, CCE will no longer provide technical support for the version, including supporting new features, backporting Kubernetes bug fixes, fixing vulnerabilities, and upgrading to new versions.

Lifecycle
---------

CCE releases only odd major Kubernetes versions, such as v1.23, v1.21, v1.19, and v1.17. The specific version support policies in different scenarios are as follows:

-  Cluster creation

   CCE provides you with two major Kubernetes versions of clusters, for example, v1.23 and v1.21. For example, after v1.23 is brought online for commercial use, v1.19 is brought offline synchronously. In this case, the cluster of this version cannot be created on the console.

-  Cluster maintenance

   CCE maintains four major Kubernetes versions, such as v1.17, v1.19, v1.21 and v1.23. For example, when v1.23 is put into commercial use, earlier versions such as v1.15 will be disused.

   |image2|

-  Cluster upgrade

   CCE supports the upgrade of three major Kubernetes versions, such as v1.21, v1.19, and v1.17. For example, after v1.23 is put into commercial use, clusters of versions earlier than v1.17, such as v1.15, cannot be upgraded any more.

Version Release Cycle
---------------------

CCE follows the Kubernetes community to release major versions. To be specific, a new CCE version is released about six months after a new Kubernetes version is released in the community.

Version Constraints
-------------------

After a cluster is upgraded, it cannot be rolled back to the source version.

.. |image1| image:: /_static/images/en-us_image_0000001178192666.png
.. |image2| image:: /_static/images/en-us_image_0000001223152415.png
