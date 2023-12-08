:original_name: cce_faq_00208.html

.. _cce_faq_00208:

Can the Resources Created Using APIs or kubectl Be Displayed on the CCE Console?
================================================================================

The CCE console does not support the display of the following Kubernetes resources: DaemonSets, ReplicationControllers, ReplicaSets, and endpoints.

To query these resources, run the kubectl commands.

In addition, Deployments, StatefulSets, Services, and pods can be displayed on the console only when the following conditions are met:

-  Deployments and StatefulSets: At least one label uses **app** as its key.

-  Pods: Pods are displayed on the **Pods** tab page in the workload details only after a Deployment or StatefulSet has been created.

-  Services: Services are displayed on the **Access Mode** tab page in the Deployment or StatefulSet details.

   The Services displayed on this tab page are associated with the workload.

   #. At lease one label of the workload uses **app** as its key.
   #. The label of a Service is the same as that of the workload.
