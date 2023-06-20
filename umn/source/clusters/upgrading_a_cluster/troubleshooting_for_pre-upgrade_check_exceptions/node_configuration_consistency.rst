:original_name: cce_10_0491.html

.. _cce_10_0491:

Node Configuration Consistency
==============================

Check Item
----------

When you upgrade a CCE cluster to v1.19 or later, the system checks whether the following configuration files have been modified in the background:

-  /opt/cloud/cce/kubernetes/kubelet/kubelet
-  /opt/cloud/cce/kubernetes/kubelet/kubelet_config.yaml
-  /opt/cloud/cce/kubernetes/kube-proxy/kube-proxy
-  /etc/containerd/default_runtime_spec.json
-  /etc/sysconfig/docker
-  /etc/default/docker
-  /etc/docker/daemon.json

If you modify some parameters in these files, the cluster upgrade may fail or services may be abnormal after the upgrade. If you confirm that the modification does not affect services, continue the upgrade.

.. note::

   CCE uses the standard image script to check node configuration consistency. If you use other custom images, the check may fail.

The expected modification will not be intercepted. The following table lists the parameters that can be modified.

.. table:: **Table 1** Parameters that can be modified

   +-----------+-------------------------------------------------------+-----------------------+------------------+
   | Component | Configuration File                                    | Parameter             | Upgrade Version  |
   +===========+=======================================================+=======================+==================+
   | kubelet   | /opt/cloud/cce/kubernetes/kubelet/kubelet_config.yaml | cpuManagerPolicy      | Later than v1.19 |
   +-----------+-------------------------------------------------------+-----------------------+------------------+
   | kubelet   | /opt/cloud/cce/kubernetes/kubelet/kubelet_config.yaml | maxPods               | Later than v1.19 |
   +-----------+-------------------------------------------------------+-----------------------+------------------+
   | kubelet   | /opt/cloud/cce/kubernetes/kubelet/kubelet_config.yaml | kubeAPIQPS            | Later than v1.19 |
   +-----------+-------------------------------------------------------+-----------------------+------------------+
   | kubelet   | /opt/cloud/cce/kubernetes/kubelet/kubelet_config.yaml | kubeAPIBurst          | Later than v1.19 |
   +-----------+-------------------------------------------------------+-----------------------+------------------+
   | kubelet   | /opt/cloud/cce/kubernetes/kubelet/kubelet_config.yaml | podPidsLimit          | Later than v1.19 |
   +-----------+-------------------------------------------------------+-----------------------+------------------+
   | kubelet   | /opt/cloud/cce/kubernetes/kubelet/kubelet_config.yaml | topologyManagerPolicy | Later than v1.19 |
   +-----------+-------------------------------------------------------+-----------------------+------------------+
   | kubelet   | /opt/cloud/cce/kubernetes/kubelet/kubelet_config.yaml | resolvConf            | Later than v1.19 |
   +-----------+-------------------------------------------------------+-----------------------+------------------+
   | kubelet   | /opt/cloud/cce/kubernetes/kubelet/kubelet_config.yaml | eventRecordQPS        | Later than v1.21 |
   +-----------+-------------------------------------------------------+-----------------------+------------------+
   | kubelet   | /opt/cloud/cce/kubernetes/kubelet/kubelet_config.yaml | topologyManagerScope  | Later than v1.21 |
   +-----------+-------------------------------------------------------+-----------------------+------------------+
   | kubelet   | /opt/cloud/cce/kubernetes/kubelet/kubelet_config.yaml | allowedUnsafeSysctls  | Later than v1.19 |
   +-----------+-------------------------------------------------------+-----------------------+------------------+
   | Docker    | /etc/docker/daemon.json                               | dm.basesize           | Later than v1.19 |
   +-----------+-------------------------------------------------------+-----------------------+------------------+

Solution
--------

If you modify some parameters in these files, exceptions may occur after the upgrade. If you are not sure whether the modified parameters will affect the upgrade, contact technical support.
