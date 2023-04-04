:original_name: cce_bulletin_0003.html

.. _cce_bulletin_0003:

Kubernetes Version Support Mechanism
====================================

This section explains versioning in CCE, and the policies for Kubernetes version support.

Version Description
-------------------

**Version number**: The format is **x.y.z**, where **x.y** is the major version and **z** is the minor version. If the version number is followed by **-r**, the version is a patch version, for example, v1.15.6-r1.

|image1|

Version Requirements
--------------------

.. important::

   **Offline**: After a version is brought offline, a cluster of this version cannot be created on the CCE console and no new features will be released for the clusters of this version.

   **Obsolete**: CCE will no longer provide support for this version, including release of new functions, community bug fixes, vulnerability management, and upgrade.

CCE releases only odd major Kubernetes versions, such as v1.25, v1.23, and v1.21. The specific version support policies in different scenarios are as follows:

-  Cluster creation

   CCE allows you to create clusters of two latest major Kubernetes versions, for example, v1.25 and v1.23. When v1.25 is commercially available, support for earlier versions (such as v1.21) will be removed. In this case, you will not be able to create clusters of v1.21 on the CCE console.

-  Cluster maintenance

   CCE maintains clusters of four major Kubernetes versions at most, such as v1.25, v1.23, v1.21, and v1.19. For example, after v1.25 is commercially available, support for v1.17 will be removed.

   |image2|

-  Cluster upgrade

   CCE allows you to upgrade clusters of **three major versions** at the same time. Clusters of 1.19 and later versions can be upgraded skipping one major version at most (for example, from 1.19 directly to 1.23). Each version is maintained for one year. For example, after v1.25 is available, support for earlier versions (such as v1.17) will be removed. You are advised to upgrade your Kubernetes cluster before the maintenance period ends.

   -  Cluster version upgrade: After the latest major version (for example, v1.25) is available, CCE allows you to upgrade clusters to the last stable version of the second-latest major version, for example, v1.23. For details, see :ref:`Upgrade Overview <cce_10_0197>`.
   -  Cluster patch upgrade: For existing clusters running on the live network, if there are major Kubernetes issues or vulnerabilities, CCE will perform the patch upgrade on these clusters in the background. Users are unaware of the patch upgrade. If the patch upgrade has adverse impact on user services, CCE will release a notice one week in advance.

Version Release Cycle
---------------------

Kubernetes releases a major version in about four months. CCE will provide support to mirror the new Kubernetes version in about seven months after the version release.

Version Constraints
-------------------

After a cluster is upgraded, it cannot be rolled back to the source version.

.. |image1| image:: /_static/images/en-us_image_0000001460905374.png
.. |image2| image:: /_static/images/en-us_image_0000001461224886.png
