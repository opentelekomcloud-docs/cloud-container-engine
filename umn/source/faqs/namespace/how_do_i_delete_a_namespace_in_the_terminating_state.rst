:original_name: cce_faq_00277.html

.. _cce_faq_00277:

How Do I Delete a Namespace in the Terminating State?
=====================================================

A Kubernetes namespace is typically in the active or terminating state. If a namespace is deleted when there are still running resources, the namespace enters the terminating state. In this case, the namespace will be automatically deleted only after Kubernetes reclaims the resources in it.

However, in some cases, even if no resource is running in the namespace, the namespace in the terminating state still cannot be deleted.

To solve this problem, perform the following operations:

#. View the namespace details.

   .. code-block::

      $ kubectl  get ns  | grep rdb
      rdbms                  Terminating   6d21h

      $ kubectl  get ns rdbms -o yaml
      apiVersion: v1
      kind: Namespace
      metadata:
        annotations:
          kubectl.kubernetes.io/last-applied-configuration: |
            {"apiVersion":"v1","kind":"Namespace","metadata":{"annotations":{},"name":"rdbms"}}
        creationTimestamp: "2020-05-07T15:19:43Z"
        deletionTimestamp: "2020-05-07T15:33:23Z"
        name: rdbms
        resourceVersion: "84553454"
        selfLink: /api/v1/namespaces/rdbms
        uid: 457788ddf-53d7-4hde-afa3-1fertg21ewe1
      spec:
        finalizers:
        - kubernetes
      status:
        phase: Terminating

#. View resources in the namespace.

   .. code-block::

      # View resources that can be isolated using namespaces in the cluster.
      $ kubectl api-resources -o name --verbs=list --namespaced | xargs -n 1 kubectl get --show-kind --ignore-not-found -n rdbms

   The command output shows that no resource is occupied in the **rdbms** namespace.

#. Delete the namespace.

   Directly delete the **rdbms** namespace.

   .. code-block::

      $ kubectl  delete ns rdbms
      Error from server (Conflict): Operation cannot be fulfilled on namespaces "rdbms": The system is ensuring all content is removed from this namespace.  Upon completion, this namespace will automatically be purged by the system.

   The deletion fails and a message is displayed, indicating that the system will automatically delete the namespace after confirming that no resource is running in it.

#. Forcibly delete the namespace.

   .. code-block::

      $ kubectl  delete ns rdbms --force --grace-period=0
      warning: Immediate deletion does not wait for confirmation that the running resource has been terminated. The resource may continue to run on the cluster indefinitely.
      Error from server (Conflict): Operation cannot be fulfilled on namespaces "rdbms": The system is ensuring all content is removed from this namespace.  Upon completion, this namespace will automatically be purged by the system.

   After running this command, the namespace still cannot be deleted.

#. Call the Kubernetes native APIs to delete resources in the namespace. In most cases, resources in a namespace cannot be forcibly deleted. Use the Kubernetes native APIs instead.

   View the namespace details.

   .. code-block::

      $ kubectl  get ns rdbms  -o json > rdbms.json

   Check the JSON configuration defined by the namespace, edit the JSON file, and delete the **spec** part.

   .. code-block::

      $ cat rdbms.json
      {
          "apiVersion": "v1",
          "kind": "Namespace",
          "metadata": {
              "annotations": {
                  "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"Namespace\",\"metadata\":{\"annotations\":{},\"name\":\"rdbms\"}}\n"
              },
              "creationTimestamp": "2019-10-14T12:17:44Z",
              "deletionTimestamp": "2019-10-14T12:30:27Z",
              "name": "rdbms",
              "resourceVersion": "8844754",
              "selfLink": "/api/v1/namespaces/rdbms",
              "uid": "29067ddf-56d7-4cce-afa3-1fbdbb221ab1"
          },
          "spec": {
              "finalizers": [
                  "kubernetes"
              ]
          },
          "status": {
              "phase": "Terminating"
          }
      }

   After the PUT request is executed, the namespace is automatically deleted.

   .. code-block::

      $ curl --cacert /root/ca.crt --cert /root/client.crt --key /root/client.key -k -H "Content-Type:application/json" -X PUT --data-binary @rdbms.json https://x.x.x.x:5443/api/v1/namespaces/rdbms/finalize
      {
        "kind": "Namespace",
        "apiVersion": "v1",
        "metadata": {
          "name": "rdbms",
          "selfLink": "/api/v1/namespaces/rdbms/finalize",
          "uid": "29067ddf-56d7-4cce-afa3-1fbdbb221ab1",
          "resourceVersion": "8844754",
          "creationTimestamp": "2019-10-14T12:17:44Z",
          "deletionTimestamp": "2019-10-14T12:30:27Z",
          "annotations": {
            "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"Namespace\",\"metadata\":{\"annotations\":{},\"name\":\"rdbms\"}}\n"
          }
        },
        "spec": {

        },
        "status": {
          "phase": "Terminating"
        }

   If the namespace still cannot be deleted, check whether the **finalizers** field exists in the metadata. If the field exists, run the following command to access the namespace and delete the field:

   .. code-block::

      kubectl edit ns rdbms

   .. note::

      -  For details about how to obtain a cluster certificate, see :ref:`Accessing a Cluster Using an X.509 Certificate <cce_10_0175>`.
      -  **https://**\ *x.x.x.x*\ **:5443** indicates the address for accessing the cluster. To obtain the private IP address, log in to the CCE console, access the cluster console, and view the connection information.

#. Check whether the namespace has been deleted.

   .. code-block::

      $ kubectl  get ns  | grep rdb
