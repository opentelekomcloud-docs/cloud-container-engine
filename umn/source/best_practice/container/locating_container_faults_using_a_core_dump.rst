:original_name: cce_bestpractice_0325.html

.. _cce_bestpractice_0325:

Locating Container Faults Using a Core Dump
===========================================

Application Scenarios
---------------------

A core dump is when the Linux OS saves the memory status to a file after a program crashes or stops unexpectedly. You can analyze the file to locate the fault.

Generally, when a service application crashes, its container exits and is reclaimed and destroyed. Therefore, container core files need to be permanently stored on the node or cloud storage. This section describes how to configure a container core dump.

Notes and Constraints
---------------------

When a container core dump is persistently stored in OBS (parallel file system or object bucket), the default mount option **umask=0** is used. As a result, although the core dump file is generated, its information cannot be written to the core file.

Enabling a Node Core Dump
-------------------------

Log in to the node, run the following command to enable a core dump, and configure the path and format for storing core files:

**echo "/tmp/cores/core.%h.%e.%p.%t" > /proc/sys/kernel/core_pattern**

**%h**, **%e**, **%p**, and **%t** are placeholders, which are described as follows:

-  **%h**: hostname (or pod name). You are advised to configure this parameter.
-  **%e**: program file name. You are advised to configure this parameter.
-  **%p**: (Optional) process ID
-  **%t**: (Optional) time of the core dump

After the core dump is enabled by running the command, the generated core file is named in the format of **core.{Host name}.{Program file name}.{Process ID}.{Time}**.

You can also configure a pre-installation or post-installation script to automatically run this command when creating a node.

Permanently Storing a Core Dump
-------------------------------

A core file can be stored in your host (using a hostPath volume) or cloud storage (using a PVC). The following is an example YAML file (**pod.yaml**) for using a hostPath volume:

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

Create the pod.

.. code-block::

   kubectl create -f pod.yaml

Verification
------------

#. Obtain the pod name:

   .. code-block::

      kubectl get pod

   Information similar to the following is displayed:

   .. code-block::

      NAME        READY   STATUS    RESTARTS   AGE
      coredump    1/1     Running   0          56s

#. Access the container:

   .. code-block::

      kubectl exec -it coredump -- /bin/sh

   Trigger a segmentation fault of the current shell terminal.

   .. code-block::

      kill -s SIGSEGV $$

   Information similar to the following is displayed:

   .. code-block::

      command terminated with exit code 139

#. Log in to the node and check whether the core file has been generated in **/home/coredump**.

   .. code-block::

      ls /home/coredump

   If information similar to the following is displayed, the core file has been generated.

   .. code-block::

      core.coredump.bash.18.1650438992
