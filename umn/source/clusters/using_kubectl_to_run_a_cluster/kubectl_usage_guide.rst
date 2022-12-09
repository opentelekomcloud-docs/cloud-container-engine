:original_name: cce_01_0023.html

.. _cce_01_0023:

kubectl Usage Guide
===================

Before running kubectl commands, you should have the kubectl development skills and understand the kubectl operations. For details, see `Kubernetes API <https://kubernetes.io/docs/concepts/overview/kubernetes-api/>`__ and `kubectl CLI <https://kubernetes.io/docs/reference/kubectl/overview/>`__.

Go to the `Kubernetes release page <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/README.md>`__ to download kubectl corresponding to the cluster version or a later version.

Cluster Connection
------------------

-  :ref:`Connecting to a Kubernetes cluster using kubectl <cce_01_0107>`

Workload Creation
-----------------

-  :ref:`Creating a Deployment using kubectl <cce_01_0047__section155246177178>`
-  :ref:`Creating a StatefulSet using kubectl <cce_01_0048__section113441881214>`

Workload Affinity/Anti-affinity Scheduling
------------------------------------------

-  :ref:`Example YAML for workload-node affinity <cce_01_0225__section711574271117>`
-  :ref:`Example YAML for workload-node anti-affinity <cce_01_0226__section1361482522712>`
-  :ref:`Example YAML for workload-workload affinity <cce_01_0220__section5140193643912>`
-  :ref:`Example YAML for workload-workload anti-affinity <cce_01_0227__section1894310152317>`
-  :ref:`Example YAML for workload-AZ affinity <cce_01_0228__section4201420133117>`
-  :ref:`Example YAML for workload-AZ anti-affinity <cce_01_0229__section102822029173111>`

Workload Access Mode Settings
-----------------------------

-  :ref:`Implementing intra-cluster access using kubectl <cce_01_0011__section9813121512319>`
-  :ref:`Implementing node access using kubectl <cce_01_0142__section7114174773118>`
-  :ref:`Implementing Layer 4 load balancing using kubectl <cce_01_0014__section1984211714368>`
-  :ref:`Implementing Layer 7 load balancing using kubectl <cce_01_0252>`

Advanced Workload Settings
--------------------------

-  :ref:`Example YAML for setting the container lifecycle <cce_01_0105__section151181981167>`

Job Management
--------------

-  :ref:`Creating a job using kubectl <cce_01_0150__section450152719412>`
-  :ref:`Creating a cron job using kubectl <cce_01_0151__section13519162224919>`

Configuration Center
--------------------

-  :ref:`Creating a ConfigMap using kubectl <cce_01_0152__section639712716372>`
-  :ref:`Creating a secret using kubectl <cce_01_0153__section821112149514>`

Storage Management
------------------

-  :ref:`Creating a PV using kubectl <cce_01_0379>`
-  :ref:`Creating a PVC using kubectl <cce_01_0378>`
