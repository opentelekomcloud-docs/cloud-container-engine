:original_name: cce_bulletin_0033.html

.. _cce_bulletin_0033:

Kubernetes Version Policy
=========================

CCE provides highly scalable, high-performance, enterprise-class Kubernetes clusters. This section describes the Kubernetes version policy of CCE clusters.

Lifecycle of CCE Cluster Versions
---------------------------------

+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| Kubernetes Version | Status                      | Community Release In | Commercial Use of CCE Clusters | EOS of CCE Clusters |
+====================+=============================+======================+================================+=====================+
| v1.32              | In commercial use\ :sup:`a` | December 2024        | October 2025                   | October 2027        |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.31              | In commercial use\ :sup:`a` | August 2024          | June 2025                      | June 2027           |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.30              | In commercial use\ :sup:`a` | April 2024           | February 2025                  | November 2026       |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.29              | In commercial use\ :sup:`b` | November 2023        | September 2024                 | June 2026           |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.28              | End of service (EOS)        | August 2023          | June 2024                      | October 2025        |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.27              | EOS                         | April 2023           | March 2024                     | June 2025           |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.25              | EOS                         | August 2022          | April 2023                     | February 2025       |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.23              | EOS                         | December 2021        | October 2022                   | September 2024      |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.21              | EOS                         | April 2021           | April 2022                     | June 2024           |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.19              | EOS                         | August 2020          | March 2021                     | March 2024          |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.17              | EOS                         | December 2019        | July 2020                      | January 2023        |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.15              | EOS                         | June 2019            | December 2019                  | September 2022      |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.13              | EOS                         | December 2018        | June 2019                      | March 2022          |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.11              | EOS                         | August 2018          | October 2018                   | March 2021          |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+
| v1.9               | EOS                         | December 2017        | March 2018                     | December 2020       |
+--------------------+-----------------------------+----------------------+--------------------------------+---------------------+

.. note::

   The CCE console supports clusters of the latest two commercially used versions:

   -  a: Clusters created using the console or APIs
   -  b: Clusters created only using APIs

Phases of CCE Cluster Versions
------------------------------

-  In commercial use: The cluster version has been fully verified and is stable and reliable. You can use clusters of this version in the production environment, and the CCE SLA is valid for such clusters.
-  EOS: After the cluster version EOS, CCE does not support the creation of new clusters or provide technical support including new feature updates, vulnerability or issue fixes, new patches, work order guidance, and online checks for the EOS cluster version. The CCE SLA is not valid for such clusters.

CCE Cluster Versions
--------------------

CCE clusters are updated according to the versions available in the Kubernetes community. This means that a CCE cluster version is made up of both the Kubernetes community version number and the CCE patch version number. The CCE cluster version is in the format for *vX.Y.Z-rN*, such as **v1.30.4-r0**.

-  A Kubernetes version is in the format of *X.Y.Z*, which inherits the community version policy. The major Kubernetes version is represented by *X*, the minor Kubernetes version is represented by *Y*, and the Kubernetes patch version is represented by *Z*. For details, see `the Kubernetes version policies <https://kubernetes.io/releases/version-skew-policy/#supported-versions>`__. For details about the Kubernetes versions supported by CCE, see :ref:`Kubernetes Version Release Notes <cce_10_0068>`.
-  A CCE patch version is in the format of, for example, v1.30.\ **4-rN**. New patches are released on an irregular basis for Kubernetes versions that are still in the maintenance period. If a new patch version provides new features, bug fixes, vulnerability fixes, or scenario optimizations compared with the previous version, the *N* version number increases. For details about the patch versions, see :ref:`Patch Versions <cce_10_0405>`.

Cluster Upgrade
---------------

Periodically upgrade CCE clusters for better user experience. Using an EOS version, you cannot obtain technical support and CCE SLA assurance. Upgrade CCE clusters in a timely manner.

On the CCE console, you can easily upgrade clusters in a visualized manner, improving the stability and reliability of clusters. For details, see :ref:`Upgrade Overview <cce_10_0197>`.
