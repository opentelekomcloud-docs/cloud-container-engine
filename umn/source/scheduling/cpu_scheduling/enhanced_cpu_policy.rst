:original_name: cce_10_0552.html

.. _cce_10_0552:

Enhanced CPU Policy
===================

Kubernetes provides two `CPU policies <https://kubernetes.io/docs/tasks/administer-cluster/cpu-management-policies/>`__: none and static.

-  **none**: The CPU policy is disabled by default, indicating the existing scheduling behavior.
-  **static**: The static CPU core binding policy is enabled. This policy allows pods with certain resource characteristics to be granted enhanced CPU affinity and exclusivity on the node.

Based on the Kubernetes static core binding policy, the enhanced CPU policy (enhanced-static) supports burstable pods (whose CPU requests and limits must be positive integers) and allows them to preferentially use certain CPUs to ensure application stability. Example:

.. code-block::

   ...
   spec:
     containers:
     - name: nginx
       image: nginx
       resources:
         limits:
           memory: "300Mi"
           cpu: "2"
         requests:
           memory: "200Mi"
           cpu: "1"

This feature is built on the optimized CPU scheduling in the HCE OS 2.0 kernel. When the CPU usage preferentially used by a container exceeds 85%, the container is automatically allocated to other CPUs with low usage to ensure the response capability of applications.

|image1|

.. note::

   -  When enhanced CPU policy is enabled, the application performance is better than that of the **none**) policy but worse than that of the **static** policy.
   -  CPU would not be exclusively used by burstable pods, it is still in the shared CPU pool. When the burstable pods are in the low tide, other pods can share this CPU.

Constraints
-----------

To use this feature, the following conditions must be met:

-  The cluster version must be v1.23 or later.
-  The node OS is HCE OS 2.0.
-  The CPU management policy cannot take effect on physical cloud server nodes.

Procedure
---------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane and click the **Node Pools** tab on the right.
#. Select a node pool whose OS is HCE OS 2.0 and click **Manage** in the **Operation** column.
#. In the **Manage Components** window that is displayed, change the **cpu-manager-policy** value of the kubelet component to **enhanced-static**.
#. Click **OK**.

Verification
------------

Take a node with 8 vCPUs and 32 GB memory as an example. Deploy a workload whose CPU request is 1 and limit is 2 in the cluster in advance.

#. Log in to a node in the node pool and view the **/var/lib/kubelet/cpu_manager_state** output.

   .. code-block::

      cat /var/lib/kubelet/cpu_manager_state

   Command output:

   .. code-block::

       {"policyName":"enhanced-static","defaultCpuSet":"0,2-7","entries":{"6739f6f2-ebe5-48ae-945a-986d5d8919b9":{"container-1":"0-7,10001"}},"checksum":1638128523}

   -  If the value of **policyName** is **enhanced-static**, the policy is configured successfully.
   -  10000 is used as the base for the CPU ID. In this example, 10001 indicates that the affinity CPU ID used by the container is CPU 1, and 0-7 indicates the set of CPUs that can be used by the container in the pod.

#. Check the cgroup setting of **cpuset.preferred_cpus** of the container. The output is the ID of the CPU that is preferentially used.

   .. code-block::

      cat /sys/fs/cgroup/cpuset/kubepods/burstable/pod {pod uid} / {Container ID} /cpuset.preferred_cpus

   -  *{pod uid}* indicates the pod UID, which can be obtained by running the following command on the host that has been connected to the cluster using kubectl:

      .. code-block::

         kubectl get po {pod name} -n {namespace} -ojsonpath='{.metadata.uid}{"\n"}'

      In the preceding command, *{pod name}* and *{namespace}* indicate the pod name and the namespace to which the pod belongs.

   -  *{Container id}* must be a complete container ID. You can run the following command on the node where the container is running to obtain the container ID:

      Docker node pool: In the command, *{pod name}* indicates the pod name.

      .. code-block::

         docker ps --no-trunc | grep {pod name} | grep -v cce-pause | awk '{print $1}'

      containerd node pool: In the command, *{pod name}* indicates the pod name, *{pod id}* indicates the pod ID, and *{container name}* indicates the container name.

      .. code-block::

         # Obtain the pod ID.
         crictl pods | grep {pod name} | awk '{print $1}'
         # Obtain the complete container ID.
         crictl ps --no-trunc | grep {pod id} | grep {container name} | awk '{print $1}'

   A complete example is as follows:

   .. code-block::

      cat /sys/fs/cgroup/cpuset/kubepods/burstable/pod6739f6f2-ebe5-48ae-945a-986d5d8919b9/5ba5603434b95fd22d36fba6a5f1c44eba83c18c2e1de9b52ac9b52e93547a13/cpuset.preferred_cpus

   If the following command output is displayed, CPU 1 is preferentially used.

   .. code-block::

      1

.. |image1| image:: /_static/images/en-us_image_0000001851586476.png
