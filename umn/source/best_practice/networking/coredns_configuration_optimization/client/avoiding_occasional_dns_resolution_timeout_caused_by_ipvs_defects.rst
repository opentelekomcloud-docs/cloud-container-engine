:original_name: cce_bestpractice_0350.html

.. _cce_bestpractice_0350:

Avoiding Occasional DNS Resolution Timeout Caused by IPVS Defects
=================================================================

When the kube-proxy uses IPVS load balancing, you may encounter DNS resolution timeout occasionally during CoreDNS scale-in or restart. This problem is caused by a Linux kernel defect. For details, see https://github.com/torvalds/linux/commit/35dfb013149f74c2be1ff9c78f14e6a3cd1539d1.
