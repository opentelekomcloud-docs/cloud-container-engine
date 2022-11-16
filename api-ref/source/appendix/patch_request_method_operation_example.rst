:original_name: cce_02_0086.html

.. _cce_02_0086:

Patch Request Method Operation Example
======================================

This section provides examples of **Merge Patch** and **Strategic Merge Patch** method operations.

Operation Example
-----------------

By way of example, **Merge Patch** and **Strategic Merge Patch** method operations are performed at the time of creating a ReplicationController resource object.

**Sample request**

.. code-block::

   {
       "apiVersion": "v1",
       "kind": "ReplicationController",
       "metadata": {
           "name": "frontend-controller"
       },
       "spec": {
           "replicas": 2,
           "selector": {
               "app": "nginx"
           },
           "template": {
               "metadata": {
                   "labels": {
                       "app": "nginx"
                   }
               },
               "spec": {
                   "containers": [
                       {
                           "name": "redis",
                           "image": "redis:latest",
                           "ports": [
                               {
                                   "containerPort": 80
                               }
                           ]
                       }
                   ]
               }
           }
       }
   }

**Sample response**

.. code-block::

   {
       "kind": "ReplicationController",
       "apiVersion": "v1",
       "metadata": {
           "name": "frontend-controller",
           "namespace": "default",
           "selfLink": "/api/v1/namespaces/default/replicationcontrollers/nginx-controller",
           "uid": "549b2234-5d46-11e6-aeb9-286ed488fafe",
           "resourceVersion": "4110",
           "generation": 1,
           "creationTimestamp": "2016-08-08T08:58:52Z",
           "labels": {
               "app": "nginx"
           }
       },
       "spec": {
           "replicas": 2,
           "selector": {
               "app": "nginx"
           },
           "template": {
               "metadata": {
                   "creationTimestamp": null,
                   "labels": {
                       "app": "nginx"
                   }
               },
               "spec": {
                   "containers": [
                       {
                           "name": "redis",
                           "image": "redis:latest",
                           "ports": [
                               {
                                   "containerPort": 80,
                                   "protocol": "TCP"
                               }
                           ],
                           "resources": {},
                           "terminationMessagePath": "/dev/termination-log",
                           "imagePullPolicy": "Always"
                       }
                   ],
                   "restartPolicy": "Always",
                   "terminationGracePeriodSeconds": 30,
                   "dnsPolicy": "ClusterFirst",
                   "securityContext": {}
               }
           }
       },
       "status": {
           "replicas": 0
       }
   }

-  If you use **Merge Patch** to add a container to the **template** field of a specified ReplicationController, the list of existing containers is then replaced by the newly added container.

   **Merge Patch request**

   .. code-block::

      {
          "spec": {
              "template": {
                  "spec": {
                      "containers": [
                          {
                              "name": "hello-world",
                              "image": "busybox:latest"
                          }
                      ]
                  }
              }
          }
      }

   **Merge Patch response**

   .. code-block::

      {
          "kind": "ReplicationController",
          "apiVersion": "v1",
          "metadata": {
              "name": "frontend-controller",
              "namespace": "default",
              "selfLink": "/api/v1/namespaces/default/replicationcontrollers/nginx-controller",
              "uid": "549b2234-5d46-11e6-aeb9-286ed488fafe",
              "resourceVersion": "4159",
              "generation": 2,
              "creationTimestamp": "2016-08-08T08:58:52Z",
              "labels": {
                  "app": "nginx"
              }
          },
          "spec": {
              "replicas": 2,
              "selector": {
                  "app": "nginx"
              },
              "template": {
                  "metadata": {
                      "creationTimestamp": null,
                      "labels": {
                          "app": "nginx"
                      }
                  },
                  "spec": {
                      "containers": [
                          {
                              "name": "hello-world",
                              "image": "busybox:latest",
                              "resources": {},
                              "terminationMessagePath": "/dev/termination-log",
                              "imagePullPolicy": "Always"
                          }
                      ],
                      "restartPolicy": "Always",
                      "terminationGracePeriodSeconds": 30,
                      "dnsPolicy": "ClusterFirst",
                      "securityContext": {}
                  }
              }
          },
          "status": {
              "replicas": 2,
              "fullyLabeledReplicas": 2,
              "observedGeneration": 1
          }
      }

   The **containers** are replaced by the newly added container.

-  If you use **Strategic Merge Patch** to add metadata to a resource object, the new metadata then determines which list should b merged and which should not.

   **Strategic Merge Patch request**

   .. code-block::

      {
          "spec": {
              "template": {
                  "spec": {
                      "containers": [
                          {
                              "name": "hello-world",
                              "image": "busybox:latest"
                          }
                      ]
                  }
              }
          }
      }'

   **Strategic Merge Patch response**

   .. code-block::

      {
          "kind": "ReplicationController",
          "apiVersion": "v1",
          "metadata": {
              "name": "frontend-controller",
              "namespace": "default",
              "selfLink": "/api/v1/namespaces/default/replicationcontrollers/nginx-controller",
              "uid": "f2e048bb-5d46-11e6-aeb9-286ed488fafe",
              "resourceVersion": "4250",
              "generation": 2,
              "creationTimestamp": "2016-08-08T09:03:18Z",
              "labels": {
                  "app": "nginx"
              }
          },
          "spec": {
              "replicas": 2,
              "selector": {
                  "app": "nginx"
              },
              "template": {
                  "metadata": {
                      "creationTimestamp": null,
                      "labels": {
                          "app": "nginx"
                      }
                  },
                  "spec": {
                      "containers": [
                          {
                              "name": "redis",
                              "image": "redis:latest",
                              "ports": [
                                  {
                                      "containerPort": 80,
                                      "protocol": "TCP"
                                  }
                              ],
                              "resources": {},
                              "terminationMessagePath": "/dev/termination-log",
                              "imagePullPolicy": "Always"
                          },
                          {
                              "name": "hello-world",
                              "image": "busybox:latest",
                              "resources": {},
                              "terminationMessagePath": "/dev/termination-log",
                              "imagePullPolicy": "Always"
                          }
                      ],
                      "restartPolicy": "Always",
                      "terminationGracePeriodSeconds": 30,
                      "dnsPolicy": "ClusterFirst",
                      "securityContext": {}
                  }
              }
          },
          "status": {
              "replicas": 2,
              "fullyLabeledReplicas": 2,
              "observedGeneration": 1
          }
      }

   The **containers** merge with the new content according to the value of the **name** field.
