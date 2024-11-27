:original_name: cce_bulletin_0061.html

.. _cce_bulletin_0061:

CCE Console Upgrade
===================

**Released**: Sep 3, 2023

Dear users,

We are pleased to announce that a brand-new CCE console is available. The new console is modern, visually appealing, and concise, providing a more comfortable and enjoyable user experience.

We have optimized the UI, including the colors, fonts, and icons. This makes the entire console clearer and easier to operate. Additionally, we have added some new interaction pages to help you use your clusters more efficiently and conveniently.

We believe that the new CCE console will improve your user experience. If you have any questions or suggestions, feel free to contact our customer service. Thank you for your support and trust.

The upgrade involves the following:

-  :ref:`Optimized Cluster Management Page <cce_bulletin_0061__en-us_topic_0000001647761836_section189561513122914>`
-  :ref:`New Cluster Settings Page <cce_bulletin_0061__en-us_topic_0000001647761836_section17807605324>`
-  :ref:`Optimized Add-ons Page <cce_bulletin_0061__en-us_topic_0000001647761836_section2752113338>`

.. _cce_bulletin_0061__en-us_topic_0000001647761836_section189561513122914:

Optimized Cluster Management Page
---------------------------------

The cluster management page has been optimized:

-  New cluster classification

   The CCE service is upgraded. It now provides CCE standard and CCE Turbo clusters. The original CCE clusters are renamed the CCE standard clusters. This kind of cluster is not a new cluster type.

-  New design of the cluster panes

   Cluster panes are now simpler and more practical. The button icons on the original cluster panes are canceled. All entries are labeled in words, and infrequently used buttons are hidden.

-  New classification of cluster functions

   The **Resources** and **O&M** categories in the navigation pane of the cluster console have been removed. The new categories are classified based on the Kubernetes native functions and the CCE cluster management and O&M observation functions.

.. _cce_bulletin_0061__en-us_topic_0000001647761836_section17807605324:

New Cluster Settings Page
-------------------------

This is a brand-new page for cluster settings that provides a unified portal for configuring the control plane in an efficient and convenient manner.

More capabilities will be added to the **Settings** page. Stay tuned for more.

.. _cce_bulletin_0061__en-us_topic_0000001647761836_section2752113338:

Optimized Add-ons Page
----------------------

CCE has made the following changes to the **Add-ons** page:

-  The classification of CCE add-ons is now more concise, helping you quickly locate the needed add-ons.
-  The new add-on names and introductions will help you understand the scenarios where these add-ons can be used and provide effective usage guidance.

.. table:: **Table 1** Add-on classification and names

   +--------------------------------------+------------------------------------+------------------------------+----------------------+
   | Category                             | New Name                           | Original Name                | Remarks              |
   +======================================+====================================+==============================+======================+
   | Scheduling and elasticity            | CCE Cluster Autoscaler             | autoscaler                   | CCE proprietary      |
   +--------------------------------------+------------------------------------+------------------------------+----------------------+
   |                                      | CCE Advanced HPA                   | cce-hpa-controller           | CCE proprietary      |
   +--------------------------------------+------------------------------------+------------------------------+----------------------+
   |                                      | Volcano Scheduler                  | volcano                      | CCE proprietary      |
   +--------------------------------------+------------------------------------+------------------------------+----------------------+
   | Cloud native observability           | CCE Node Problem Detector          | npd                          | CCE proprietary      |
   +--------------------------------------+------------------------------------+------------------------------+----------------------+
   |                                      | Cloud Native Cluster Monitoring    | kube-prometheus-stack        | CCE proprietary      |
   +--------------------------------------+------------------------------------+------------------------------+----------------------+
   |                                      | Kubernetes Metrics Server          | metrics-server               | Featured open source |
   +--------------------------------------+------------------------------------+------------------------------+----------------------+
   | Cloud native heterogeneous computing | CCE AI Suite (NVIDIA GPU)          | gpu-device-plugin (gpu-beta) | CCE proprietary      |
   +--------------------------------------+------------------------------------+------------------------------+----------------------+
   | Network                              | CoreDNS                            | coredns                      | CCE proprietary      |
   +--------------------------------------+------------------------------------+------------------------------+----------------------+
   |                                      | NGINX Ingress Controller           | nginx-ingress                | Featured open source |
   +--------------------------------------+------------------------------------+------------------------------+----------------------+
   | Storage                              | CCE Container Storage (Everest)    | everest                      | CCE proprietary      |
   +--------------------------------------+------------------------------------+------------------------------+----------------------+
   |                                      | CCE Container Storage (FlexVolume) | storage-driver               | CCE proprietary      |
   +--------------------------------------+------------------------------------+------------------------------+----------------------+
