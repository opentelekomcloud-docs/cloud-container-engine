:original_name: cce_10_0492.html

.. _cce_10_0492:

Node Configuration File
=======================

Check Items
-----------

Check whether the configuration files of key components exist on the node.

The following table lists the files to be checked.

+-------------------------------------------------------+--------------------------------------------+------------------------------------------------------------------+
| File Name                                             | File Content                               | Remarks                                                          |
+=======================================================+============================================+==================================================================+
| /opt/cloud/cce/kubernetes/kubelet/kubelet             | kubelet command line startup parameters    | None                                                             |
+-------------------------------------------------------+--------------------------------------------+------------------------------------------------------------------+
| /opt/cloud/cce/kubernetes/kubelet/kubelet_config.yaml | kubelet startup parameters                 | None                                                             |
+-------------------------------------------------------+--------------------------------------------+------------------------------------------------------------------+
| /opt/cloud/cce/kubernetes/kube-proxy/kube-proxy       | kube-proxy command line startup parameters | None                                                             |
+-------------------------------------------------------+--------------------------------------------+------------------------------------------------------------------+
| /etc/sysconfig/docker                                 | Docker configuration file                  | Not checked when containerd or the Debain-Group machine is used. |
+-------------------------------------------------------+--------------------------------------------+------------------------------------------------------------------+
| /etc/default/docker                                   | Docker configuration file                  | Not checked when containerd or the Centos-Group machine is used. |
+-------------------------------------------------------+--------------------------------------------+------------------------------------------------------------------+

Solution
--------

Contact technical support to restore the configuration file and then perform the upgrade.
