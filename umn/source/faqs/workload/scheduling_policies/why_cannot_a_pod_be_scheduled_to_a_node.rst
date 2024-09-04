:original_name: cce_faq_00293.html

.. _cce_faq_00293:

Why Cannot a Pod Be Scheduled to a Node?
========================================

#. Check whether the node and Docker are normal. For details, see :ref:`Check Item 7: Whether Internal Components Are Normal <cce_faq_00120__section89551837167>`.
#. If the node and Docker are normal, check whether an affinity policy is configured for the pod. For details, see :ref:`Check Item 3: Affinity and Anti-Affinity Configuration of the Workload <cce_faq_00098__section794092214205>`.
#. Check whether the resources on the node are sufficient. If the resources are insufficient, expand the capacity or add nodes.
