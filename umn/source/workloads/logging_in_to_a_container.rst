:original_name: cce_10_00356.html

.. _cce_10_00356:

Logging In to a Container
=========================

Scenario
--------

If you encounter unexpected problems when using a container, you can log in to the container to debug it.

Notes and Constraints
---------------------

-  When kubectl is used in CloudShell, permissions are determined by the logged-in user.
-  When using CloudShell to access a CCE cluster or container, you can open up to 15 instances concurrently.
-  The kubectl certificate in CloudShell is valid for one day. You can reset its validity period by accessing CloudShell through the CCE console.

Using kubectl
-------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Run the following command to view the created pod:

   .. code-block::

      kubectl get pod

   The example output is as follows:

   .. code-block::

      NAME                               READY   STATUS    RESTARTS       AGE
      nginx-59d89cb66f-mhljr             1/1     Running   0              11m

#. Query the container name in the pod.

   .. code-block::

      kubectl get po nginx-59d89cb66f-mhljr -o jsonpath='{range .spec.containers[*]}{.name}{end}{"\n"}'

   The example output is as follows:

   .. code-block::

      container-1

#. Run the following command to log in to the **container-1** container in the **nginx-59d89cb66f-mhljr** pod:

   .. code-block::

      kubectl exec -it nginx-59d89cb66f-mhljr -c container-1 -- /bin/sh

#. To exit the container, run the **exit** command.
