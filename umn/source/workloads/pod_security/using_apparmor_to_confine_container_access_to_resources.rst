:original_name: cce_10_1006.html

.. _cce_10_1006:

Using AppArmor to Confine Container Access to Resources
=======================================================

Application Armor (AppArmor) is a security module of the Linux kernel, which is usually used in OSs such as Ubuntu. AppArmor allows system administrators to associate each program with a security profile to confine program functions. AppArmor is an access control system similar to SELinux. It can be used to specify which files a program can read, write, or run, and whether the program can access network ports. AppArmor supplements the traditional Unix discretionary access control (DAC) model by providing mandatory access control (MAC). It has been integrated into the Linux kernel since version 2.6.

This section describes how to use AppArmor to confine container access to resources on Ubuntu nodes in a CCE cluster.

Constraints
-----------

-  This feature is supported only in clusters of v1.31.6-r0 or later, and the nodes must run Ubuntu. Ensure that your cluster version and node OS meet the requirements.
-  The version of the Everest add-on in the cluster must be 2.4.158 or later.
-  After AppArmor is enabled on a node, Docker or containerd applies the default AppArmor profile to pods scheduled to the node even if the **appArmorProfile** parameters are not configured for the pods. The AppArmor profile is not enforced when the **appArmorProfile.type** parameter is explicitly set to **Unconfined** for the pods. For details, see :ref:`Using AppArmor Profiles to Secure Pods <cce_10_1006__section64546101475>`.
-  Operations that trigger node reset, such as :ref:`resetting a node <cce_10_0003>` or :ref:`synchronizing a node pool <cce_10_0654>`, will disable AppArmor. If you need to disable AppArmor on a node, reset the node. Currently, AppArmor cannot be manually disabled.

Enabling AppArmor on a Node
---------------------------

#. Log in to the Ubuntu node and install AppArmor.

   .. code-block::

      sudo DEBIAN_FRONTEND=noninteractive apt-get -y install apparmor

   .. caution::

      If a private Ubuntu image is used, pre-install AppArmor in the image.

#. Check the AppArmor status.

   .. code-block::

      sudo aa-status

   If the following information is displayed, AppArmor has been loaded:

   .. code-block::

      apparmor module is loaded.

#. Start AppArmor and set it to be automatically started upon system boot. When the node is restarted, the AppArmor profile is automatically reloaded.

   .. code-block::

      sudo systemctl enable apparmor
      sudo systemctl start apparmor

#. Check whether the system is started. If **Y** is returned, the system is started.

   .. code-block::

      sudo cat /sys/module/apparmor/parameters/enabled

   The parameters in the command output are read by kubelet to check whether AppArmor is enabled on the node. If AppArmor is not enabled on the node, pods with AppArmor settings will be intercepted when they are scheduled to this node.

   Restart kubelet to read the parameters in the command output. (AppArmor is enabled by default in kubelet 1.31 GA.)

   .. code-block::

      sudo systemctl restart kubelet

Enabling AppArmor for the Container Engine
------------------------------------------

**If the container engine is containerd, take the following steps:**

#. Modify the containerd configuration.

   .. code-block::

      vim /etc/containerd/config.toml

#. Modify the following content and save the modification:

   .. code-block::

      [plugins."io.containerd.grpc.v1.cri"]
        disable_apparmor = false

#. Restart containerd.

   .. code-block::

      sudo systemctl restart containerd

**If the container engine is Docker, take the following steps:**

#. Modify the Docker configuration.

   .. code-block::

      vim /etc/default/docker

#. Use # to comment out **container='disable apparmor'**.

   .. code-block::

      # container='disable apparmor'

#. Restart Docker.

   .. code-block::

      sudo systemctl restart docker

.. _cce_10_1006__section64546101475:

Using AppArmor Profiles to Secure Pods
--------------------------------------

AppArmor profiles can be specified at the pod or container level. If a pod-level profile is set, it will be used as the default profile for all containers (including init, sidecar, and ephemeral containers) in the pod. If both pod- and container-level profiles are set, the container-level profile is preferentially used. The following shows an example configuration:

.. code-block::

   ...
   securityContext:
     appArmorProfile:
       type: <profile_type>
   ...

The options of *<profile_type>* are as follows:

-  **Localhost**: The profile loaded on the node is used. For details, see :ref:`Example: Using Custom AppArmor Profile to Confine a Pod <cce_10_1006__section91680231675>`.
-  **RuntimeDefault**: The default profile is used.
-  **Unconfined**: The AppArmor profile is not enforced.

For details about the AppArmor profile API, see `Specifying AppArmor Confinement <https://kubernetes.io/docs/tutorials/security/apparmor/>`__.

.. caution::

   When privileged containers are enabled for a pod (**privileged: true**), configure rules for the AppArmor configuration to take effect. For details, see :ref:`Rules for AppArmor Configurations to Take Effect After AppArmor Is Enabled for Privileged Containers <cce_10_1006__section826519419568>`.

To verify that a container's root process is running with the correct profile, you can check its process attributes.

.. code-block::

   kubectl exec <pod_name> -- cat /proc/1/attr/current

An example output is as follows:

.. code-block::

   cri-containerd.apparmor.d (enforce)

.. _cce_10_1006__section91680231675:

Example: Using Custom AppArmor Profile to Confine a Pod
-------------------------------------------------------

#. Load the custom AppArmor profile onto the node. You can define the profile name.

   .. code-block::

      vim /etc/apparmor.d/k8s-apparmor-example-deny-write

   The following is an example. The profile denies all file write operations. For details about the profile writing syntax, see the `AppArmor Profile Quick Guide <https://gitlab.com/apparmor/apparmor/-/wikis/QuickProfileLanguage>`__.

   .. code-block::

      #include <tunables/global>
      profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
        #include <abstractions/base>
        file,
        # Deny all file writes.
        deny /** w,
      }

   Load the profile.

   .. code-block::

      apparmor_parser -r /etc/apparmor.d/k8s-apparmor-example-deny-write

   .. caution::

      The AppArmor profile needs to be loaded onto each node that needs to use AppArmor.

#. Create a simple pod as follows and add the AppArmor rules for the pod to reject file writing as set in the previous step.

   Create the **hello-apparmor.yaml** file and add the following content to the file:

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        name: hello-apparmor
      spec:
        securityContext:
          appArmorProfile:
            type: Localhost  # Use the AppArmor profile loaded on the node.
            localhostProfile: k8s-apparmor-example-deny-write  # Define the name of the AppArmor profile loaded on the node.
        containers:
        - name: hello
          image: busybox:1.28
          command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]

   .. caution::

      If the cluster contains nodes that do not support AppArmor, use affinity scheduling to schedule the pod to a node that supports AppArmor. For details, see :ref:`Configuring Node Affinity Scheduling (nodeAffinity) <cce_10_0892>`.

#. Create a sample pod.

   .. code-block::

      kubectl create -f hello-apparmor.yaml

#. When the pod is running, check the **/proc/1/attr/current** file of the container to verify that the container is running with the AppArmor profile.

   .. code-block::

      kubectl exec hello-apparmor -- cat /proc/1/attr/current

   If the following information is displayed, the AppArmor profile has been applied to the container:

   .. code-block::

      k8s-apparmor-example-deny-write (enforce)

#. Write files to violate the AppArmor profile and check whether the profile takes effect.

   .. code-block::

      kubectl exec hello-apparmor -- touch /tmp/test

   If the following information is displayed, the write operation is rejected, and the profile takes effect:

   .. code-block::

      touch: /tmp/test: Permission denied
      command terminated with exit code 1

.. _cce_10_1006__section826519419568:

Rules for AppArmor Configurations to Take Effect After AppArmor Is Enabled for Privileged Containers
----------------------------------------------------------------------------------------------------

If privileged containers are enabled for a pod (**privileged: true**), the rules for AppArmor configurations to take effect are as follows.

+----------------------------+-------------------------------------------------+-----------------------------------------------+
| Pod AppArmor Configuration | Privileged Containers Not Used                  | Privileged Containers Used (privileged: true) |
+============================+=================================================+===============================================+
| AppArmor not enabled       | Use the default policy of the container engine. | The AppArmor profile is not enforced.         |
+----------------------------+-------------------------------------------------+-----------------------------------------------+
| RuntimeDefault             | Use the default policy of the container engine. | The AppArmor profile is not enforced.         |
+----------------------------+-------------------------------------------------+-----------------------------------------------+
| Unconfined                 | The AppArmor profile is not enforced.           | The AppArmor profile is not enforced.         |
+----------------------------+-------------------------------------------------+-----------------------------------------------+
| Localhost                  | Use the AppArmor profile loaded on the node.    | Use the AppArmor profile loaded on the node.  |
+----------------------------+-------------------------------------------------+-----------------------------------------------+

Helpful Links
-------------

-  `AppArmor official website <https://apparmor.net/>`__
-  `Using AppArmor in Ubuntu <https://documentation.ubuntu.com/server/how-to/security/apparmor/index.html>`__
-  `Using AppArmor to Confine Container Access to Resources <https://kubernetes.io/docs/tutorials/security/apparmor/>`__
-  `AppArmor Profile Quick Guide <https://gitlab.com/apparmor/apparmor/-/wikis/QuickProfileLanguage>`__
