:original_name: cce_10_0139.html

.. _cce_10_0139:

Common kubectl Commands
=======================

Getting Started
---------------

**get**

The **get** command displays one or many resources of a cluster.

This command prints a table of the most important information about all resources, including cluster nodes, running pods, Deployments, and Services.

.. important::

   A cluster can have multiple namespaces. If no namespace is specified, this command will run with the **--namespace=default** flag.

Examples:

To list all pods with detailed information:

.. code-block::

   kubectl get po -o wide

To display pods in all namespaces:

.. code-block::

   kubectl get po --all-namespaces

To list labels of pods in all namespaces:

.. code-block::

   kubectl get po --show-labels

To list all namespaces of the node:

.. code-block::

   kubectl get namespace

.. note::

   To list information of other nodes, run this command with the **-s** flag. To list a specified type of resources, add the resource type to this command, for example, **kubectl get svc**, **kubectl get nodes**, and **kubectl get deploy**.

To list a pod with a specified name in YAML output format:

.. code-block::

   kubectl get po <podname> -o yaml

To list a pod with a specified name in JSON output format:

.. code-block::

   kubectl get po <podname> -o json

.. code-block::

   kubectl get po rc-nginx-2-btv4j -o=custom-columns=LABELS:.metadata.labels.app

.. note::

   **LABELS** indicates a comma separated list of user-defined column titles. **metadata.labels.app** indicates the data to be listed in either YAML or JSON output format.

**create**

The **create** command creates a cluster resource from a file or input.

If there is already a resource descriptor (a YAML or JSON file), you can create the resource from the file by running the following command:

.. code-block::

   kubectl create -f filename

**expose**

The **expose** command exposes a resource as a new Kubernetes service. Possible resources include a pod, Service, and Deployment.

.. code-block::

   kubectl expose deployment deployname --port=81 --type=NodePort --target-port=80 --name=service-name

.. note::

   In the preceding command, **--port** indicates the port exposed by the Service, **--type** indicates the Service type, and **--target-port** indicates the port of the pod backing the Service. Visiting *ClusterIP*:*Port* allows you to access the applications in the cluster.

**run**

Examples:

To run a particular image in the cluster:

.. code-block::

   kubectl run deployname --image=nginx:latest

To run a particular image using a specified command:

.. code-block::

   kubectl run deployname -image=busybox --command -- ping baidu.com

**set**

The **set** command configures object resources.

Example:

To change the image of a deployment with the name specified in **deployname** to image 1.0:

.. code-block::

   kubectl set image deploy deployname containername=containername:1.0

**edit**

The **edit** command edits a resource from the default editor.

Examples:

To update a pod:

.. code-block::

   kubectl edit po po-nginx-btv4j

The example command yields the same effect as the following command:

.. code-block::

   kubectl get po po-nginx-btv4j -o yaml >> /tmp/nginx-tmp.yaml
   vim /tmp/nginx-tmp.yaml
   /*do some changes here */
   kubectl replace -f /tmp/nginx-tmp.yaml

**explain**

The **explain** command views documents or reference documents.

Example:

To get documentation of pods:

.. code-block::

   kubectl explain pod

**delete**

The **delete** command deletes resources by resource name or label.

Example:

To delete a pod with minimal delay:

.. code-block::

   kubectl delete po podname --now

.. code-block::

   kubectl delete -f nginx.yaml
   kubectl delete deployment deployname

Deployment Commands
-------------------

**rolling-update\***

**rolling-update** is a very important command. It updates a running service with zero downtime. Pods are incrementally replaced by new ones. One pod is updated at a time. The old pod is deleted only after the new pod is up. New pods must be distinct from old pods by name, version, and label. Otherwise, an error message will be reported.

.. code-block::

   kubectl rolling-update poname -f newfilename
   kubectl rolling-update poname -image=image:v2

If any problem occurs during the rolling update, run the command with the **-rollback** flag to abort the rolling update and revert to the previous pod.

.. code-block::

   kubectl rolling-update poname -rollback

**rollout**

The **rollout** command manages the rollout of a resource.

Examples:

To check the rollout status of a particular deployment:

.. code-block::

   kubectl rollout status deployment/deployname

To view the rollout history of a particular deployment:

.. code-block::

   kubectl rollout history deployment/deployname

To roll back to the previous deployment: (by default, a resource is rolled back to the previous version)

.. code-block::

   kubectl rollout undo deployment/test-nginx

**scale**

The **scale** command sets a new size for a resource by adjusting the number of resource replicas.

.. code-block::

   kubectl scale deployment deployname --replicas=newnumber

**autoscale**

The **autoscale** command automatically chooses and sets the number of pods. This command specifies the range for the number of pod replicas maintained by a replication controller. If there are too many pods, the replication controller terminates the extra pods. If there is too few, the replication controller starts more pods.

.. code-block::

   kubectl autoscale deployment deployname --min=minnumber --max=maxnumber

Cluster Management Commands
---------------------------

**cordon, drain, uncordon\***

If a node to be upgraded is running many pods or is already down, perform the following steps to prepare the node for maintenance:

#. Run the **cordon** command to mark a node as unschedulable. This means that new pods will not be scheduled onto the node.

   .. code-block::

      kubectl cordon nodename

   Note: In CCE, **nodename** indicates the private network IP address of a node.

#. Run the **drain** command to smoothly migrate the running pods from the node to another node.

   .. code-block::

      kubectl drain nodename --ignore-daemonsets --ignore-emptydir

   **ignore-emptydir** ignores the pods that use emptyDirs.

#. Perform maintenance operations on the node, such as upgrading the kernel and upgrading Docker.

#. After node maintenance is completed, run the **uncordon** command to mark the node as schedulable.

   .. code-block::

      kubectl uncordon nodename

**cluster-info**

To display the add-ons running in the cluster:

.. code-block::

   kubectl cluster-info

To dump current cluster information to stdout:

.. code-block::

   kubectl cluster-info dump

**top\***

The **top** command displays resource (CPU/memory/storage) usage. This command requires Heapster to be correctly configured and working on the server.

**taint\***

The **taint** command updates the taints on one or more nodes.

**certificate\***

The **certificate** command modifies the certificate resources.

Fault Diagnosis and Debugging Commands
--------------------------------------

**describe**

The **describe** command is similar to the **get** command. The difference is that the **describe** command shows details of a specific resource or group of resources, whereas the **get** command lists one or more resources in a cluster. The **describe** command does not support the **-o** flag. For resources of the same type, resource details are printed out in the same format.

.. note::

   If the information about a resource is queried, you can use the get command to obtain more detailed information. If you want to check the status of a specific resource, for example, to check if a pod is in the running state, run the **describe** command to show more detailed status information.

   .. code-block::

      kubectl describe po <podname>

**logs**

The **logs** command prints logs for a container in a pod or specified resource to stdout. To display logs in the **tail -f** mode, run this command with the **-f** flag.

.. code-block::

   kubectl logs -f podname

**exec**

The kubectl **exec** command is similar to the Docker **exec** command and executes a command in a container. If there are multiple containers in a pod, use the **-c** flag to choose a container.

.. code-block::

   kubectl exec -it podname bash
   kubectl exec -it podname -c containername bash

**port-forward\***

The **port-forward** command forwards one or more local ports to a pod.

Example:

To listen on ports 5000 and 6000 locally, forwarding data to/from ports 5000 and 6000 in the pod:

.. code-block::

   kubectl port-forward podname 5000:6000

**proxy\***

The **proxy** command creates a proxy server between localhost and the Kubernetes API server.

Example:

To enable the HTTP REST APIs on the master node:

.. code-block::

   kubectl proxy -accept-hosts= '.*' -port=8001 -address= '0.0.0.0'

**cp**

The **cp** command copies files and directories to and from containers.

.. code-block::

   cp filename newfilename

**auth\***

The **auth** command inspects authorization.

**attach\***

The **attach** command is similar to the **logs -f** command and attaches to a process that is already running inside an existing container. To exit, run the **ctrl-c** command. If a pod contains multiple containers, to view the output of a specific container, use the **-c** flag and *containername* following *podname* to specify a container.

.. code-block::

   kubectl attach podname -c containername

Advanced Commands
-----------------

**replace**

The **replace** command updates or replaces an existing resource by attributes including the number of replicas, labels, image versions, and ports. You can directly modify the original YAML file and then run the **replace** command.

.. code-block::

   kubectl replace -f filename

.. important::

   Resource names cannot be updated.

**apply\***

The **apply** command provides a more strict control on resource updating than **patch** and **edit** commands. The **apply** command applies a configuration to a resource and maintains a set of configuration files in source control. Whenever there is an update, the configuration file is pushed to the server, and then the kubectl **apply** command applies the latest configuration to the resource. The Kubernetes compares the new configuration file with the original one and updates only the changed configuration instead of the whole file. The configuration that is not contained in the **-f** flag will remain unchanged. Unlike the **replace** command which deletes the resource and creates a new one, the **apply** command directly updates the original resource. Similar to the git operation, the **apply** command adds an annotation to the resource to mark the current apply.

.. code-block::

   kubectl apply -f

**patch**

If you want to modify attributes of a running container without first deleting the container or using the **replace** command, the **patch** command is to the rescue. The **patch** command updates field(s) of a resource using strategic merge patch, a JSON merge patch, or a JSON patch. For example, to change a pod label from **app=nginx1** to **app=nginx2** while the pod is running, use the following command:

.. code-block::

   kubectl patch pod podname -p '{"metadata":{"labels":{"app":"nginx2"}}}'

**convent\***

The **convert** command converts configuration files between different API versions.

Configuration Commands
----------------------

**label**

The **label** command update labels on a resource.

.. code-block::

   kubectl label pods my-pod new-label=newlabel

**annotate**

The **annotate** command update annotations on a resource.

.. code-block::

   kubectl annotate pods my-pod icon-url=http://......

**completion**

The **completion** command provides autocompletion for shell.

Other Commands
--------------

**api-versions**

The **api-versions** command prints the supported API versions.

.. code-block::

   kubectl api-versions

**api-resources**

The **api-resources** command prints the supported API resources.

.. code-block::

   kubectl api-resources

**config\***

The **config** command modifies kubeconfig files. An example use case of this command is to configure authentication information in API calls.

**help**

The **help** command gets all command references.

**version**

The **version** command prints the client and server version information for the current context.

.. code-block::

   kubectl version
