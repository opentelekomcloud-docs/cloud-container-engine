:original_name: cce_bestpractice_0349.html

.. _cce_bestpractice_0349:

Selecting a Proper Image
========================

The musl libc library of the Alpine container image differs from the standard glibc library in the following aspects:

-  Alpine 3.3 and earlier versions do not support the **search** parameter. As a result, search domains cannot be specified for discovering Services.
-  Multiple DNS servers configured in **/etc/resolve.conf** are concurrently requested. As a result, NodeLocal DNSCache cannot improve the DNS performance.
-  When the same Socket is used to request A and AAAA records concurrently, the Conntrack source port conflict is triggered in the kernel of an earlier version. As a result, packet loss occurs.
-  If the domain name cannot be resolved when Alpine is used as the base container image, update the base container image for testing.

For details about the functional differences from glibc, see `Functional differences from glibc <https://wiki.musl-libc.org/functional-differences-from-glibc.html>`__.
