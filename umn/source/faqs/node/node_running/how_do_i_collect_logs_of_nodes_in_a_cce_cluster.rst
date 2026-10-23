:original_name: cce_faq_00201.html

.. _cce_faq_00201:

How Do I Collect Logs of Nodes in a CCE Cluster?
================================================

Paths of Node Logs
------------------

The following tables list log files of CCE nodes.

.. table:: **Table 1** Node logs

   +-----------------------------------+--------------------------------------------------------------------------------------+
   | Name                              | Path                                                                                 |
   +===================================+======================================================================================+
   | kubelet logs                      | -  For clusters of v1.21 or later: **/var/log/cce/kubernetes/kubelet.log**           |
   |                                   | -  For clusters of v1.19 or earlier: **/var/paas/sys/log/kubernetes/kubelet.log**    |
   +-----------------------------------+--------------------------------------------------------------------------------------+
   | kube-proxy logs                   | -  For clusters of v1.21 or later: **/var/log/cce/kubernetes/kube-proxy.log**        |
   |                                   | -  For clusters of v1.19 or earlier: **/var/paas/sys/log/kubernetes/kube-proxy.log** |
   +-----------------------------------+--------------------------------------------------------------------------------------+
   | yangtse logs (networking)         | -  For clusters of v1.21 or later: **/var/log/cce/yangtse**                          |
   |                                   | -  For clusters of v1.19 or earlier: **/var/paas/sys/log/yangtse**                   |
   +-----------------------------------+--------------------------------------------------------------------------------------+
   | canal logs                        | -  For clusters of v1.21 or later: **/var/log/cce/canal**                            |
   |                                   | -  For clusters of v1.19 or earlier: **/var/paas/sys/log/canal**                     |
   +-----------------------------------+--------------------------------------------------------------------------------------+
   | System logs                       | **/var/log/messages**                                                                |
   +-----------------------------------+--------------------------------------------------------------------------------------+
   | Container runtime logs            | -  Docker nodes: **/var/lib/docker**                                                 |
   |                                   |                                                                                      |
   |                                   | -  containerd nodes:                                                                 |
   |                                   |                                                                                      |
   |                                   |    Version 1.23 or later: **/var/log/cce/containerd**                                |
   +-----------------------------------+--------------------------------------------------------------------------------------+

.. table:: **Table 2** Add-on logs

   +--------------------------------------+-------------------------------------------------------------------------------+
   | Name                                 | Path                                                                          |
   +======================================+===============================================================================+
   | CCE Container Storage (Everest) logs | -  For v2.1.41 or later:                                                      |
   |                                      |                                                                               |
   |                                      |    -  everest-csi-driver: **/var/log/cce/kubernetes**                         |
   |                                      |    -  everest-csi-controller: **/var/paas/sys/log/kubernetes**                |
   |                                      |                                                                               |
   |                                      | -  For versions earlier than v2.1.41:                                         |
   |                                      |                                                                               |
   |                                      |    -  everest-csi-driver: **/var/log/cce/everest-csi-driver**                 |
   |                                      |    -  everest-csi-controller: **/var/paas/sys/log/everest-csi-controller**    |
   +--------------------------------------+-------------------------------------------------------------------------------+
   | CCE Node Problem Detector logs       | -  For v1.18.16 or later: **/var/paas/sys/log/kubernetes**                    |
   |                                      | -  For versions earlier than v1.18.16: **/var/paas/sys/log/cceaddon-npd**     |
   +--------------------------------------+-------------------------------------------------------------------------------+
   | CCE Advanced HPA logs                | -  For v1.3.12 or later: **/var/paas/sys/log/kubernetes**                     |
   |                                      | -  For versions earlier than v1.3.12: **/var/paas/sys/log/ccehpa-controller** |
   +--------------------------------------+-------------------------------------------------------------------------------+
