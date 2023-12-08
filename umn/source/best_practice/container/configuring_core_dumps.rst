:original_name: cce_bestpractice_0325.html

.. _cce_bestpractice_0325:

Configuring Core Dumps
======================

Application Scenarios
---------------------

Linux allows you to create a core dump file if an application crashes, which contains the data the application had in memory at the time of the crash. You can analyze the file to locate the fault.

Generally, when a service application crashes, its container exits and is reclaimed and destroyed. Therefore, container core files need to be permanently stored on the host or cloud storage. This topic describes how to configure container core dumps.

Constraints
-----------

When a container core dump is persistently stored to OBS (parallel file system or object bucket), the default mount option **umask=0** is used. As a result, although the core dump file is generated, the core dump information cannot be written to the core file.

Enabling Core Dump on a Node
----------------------------

Log in to the node, run the following command to enable core dump, and set the path and format for storing core files:

**echo "/tmp/cores/core.%h.%e.%p.%t" > /proc/sys/kernel/core_pattern**

**%h**, **%e**, **%p**, and **%t** are placeholders, which are described as follows:

-  **%h**: hostname (or pod name). You are advised to configure this parameter.
-  **%e**: program file name. You are advised to configure this parameter.
-  **%p**: (optional) process ID.
-  **%t**: (optional) time of the core dump.

After the core dump function is enabled by running the preceding command, the generated core file is named in the format of **core.{Host name}.{Program file name}.{Process ID}.{Time}**.

You can also configure a pre-installation or post-installation script to automatically run this command when creating a node.

Permanently Storing Core Dumps
------------------------------

A core file can be stored in your host (using a hostPath volume) or cloud storage (using a PVC). The following is an example YAML file for using a hostPath volume.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: coredump
   spec:
     volumes:
     - name: coredump-path
       hostPath:
         path: /home/coredump
     containers:
     - name: ubuntu
       image: ubuntu:12.04
       command: ["/bin/sleep","3600"]
       volumeMounts:
       - mountPath: /tmp/cores
         name: coredump-path

Create a pod using kubectl.

**kubectl create -f pod.yaml**

Verification
------------

After the pod is created, access the container and trigger a segmentation fault of the current shell terminal.

.. code-block::

   $ kubectl get pod
   NAME                          READY   STATUS    RESTARTS   AGE
   coredump                      1/1     Running   0          56s
   $ kubectl exec -it coredump -- /bin/bash
   root@coredump:/# kill -s SIGSEGV $$
   command terminated with exit code 139

Log in to the node and check whether a core file is generated in the **/home/coredump** directory. The following example indicates that a core file is generated.

.. code-block::

   # ls /home/coredump
   core.coredump.bash.18.1650438992
