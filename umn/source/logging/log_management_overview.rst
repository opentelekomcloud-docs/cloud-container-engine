:original_name: cce_10_0557.html

.. _cce_10_0557:

Log Management Overview
=======================

CCE allows you to configure policies for collecting, managing, and analyzing workload logs periodically to prevent logs from being over-sized.

-  Using ICAgent:

   By default, the ICAgent collects container standard outputs (stdout logs). No configuration required.

   You can also configure the path for storing container logs when creating a workload so that the ICAgent collects logs from this path.

   You can select either of the following modes for container logs:

   -  hostPath: A host path is mounted to the specified container path (mount path). In the node host path, you can view the container logs output into the mount path.
   -  emptyDir: A temporary path of the node is mounted to the specified path (mount path). Log data that exists in the temporary path but is not reported by the collector to AOM will disappear after the pod is deleted.
