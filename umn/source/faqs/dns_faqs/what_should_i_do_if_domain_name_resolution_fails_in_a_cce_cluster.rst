:original_name: cce_faq_00197.html

.. _cce_faq_00197:

What Should I Do If Domain Name Resolution Fails in a CCE Cluster?
==================================================================

Check Item 1: Whether the CoreDNS Add-on Has Been Installed
-----------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Add-ons** and check whether the CoreDNS add-on has been installed.
#. If not, install the add-on. For details, see :ref:`Why Does a Container in a CCE Cluster Fail to Perform DNS Resolution? <cce_faq_00107>`

.. _cce_faq_00197__section19878113435:

Check Item 2: Whether the CoreDNS Instance Reaches the Performance Limit
------------------------------------------------------------------------

CoreDNS QPS is positively correlated with the CPU usage. If the QPS is high, adjust the CoreDNS instance specifications based on the QPS.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons** and verify that CoreDNS is running.

#. Click the CoreDNS add-on name to view the add-on pod list.

#. Click **Monitor** of the add-on pods to view the CPU and memory usage.

   If the add-on performance reaches the bottleneck, adjust the CoreDNS add-on specifications.

Check Item 3: Whether the External Domain Name Resolution Is Slow or Times Out
------------------------------------------------------------------------------

If the domain name resolution failure rate is lower than 1/10000, optimize parameters by referring to :ref:`How Do I Optimize the Configuration If the External Domain Name Resolution Is Slow or Times Out? <cce_faq_00195>` or add a retry policy in the service.

Check Item 4: Whether UnknownHostException Occurs
-------------------------------------------------

When service requests in the cluster are sent to an external DNS server, a domain name resolution error occurs due to occasional UnknownHostException. UnknownHostException is a common exception. When this exception occurs, check whether there is any domain name-related error or whether you have entered a correct domain name.

To locate the fault, perform the following operations:

#. Check the hostname carefully (spelling and extra spaces).

#. Check the DNS settings. Before running the application, run the **ping** *hostname* command to ensure that the DNS server has been started and running. If the hostname is new, you need to wait for a period of time before the DNS server is accessed.

#. Check the CPU and memory usage of the CoreDNS add-on to determine whether the performance bottleneck has been reached. For details, see :ref:`Check Item 2: Whether the CoreDNS Instance Reaches the Performance Limit <cce_faq_00197__section19878113435>`.

#. Check whether traffic limiting is performed on the CoreDNS add-on. If traffic limiting is triggered, the processing time of some requests may be prolonged. In this case, you need to adjust the CoreDNS add-on specifications.

   Log in to the node where the CoreDNS add-on is installed and view the following content:

   .. code-block::

      cat /sys/fs/cgroup/cpu/kubepods/pod<pod_uid>/<CoreDNS container ID>/cpu.stat

   -  *<pod uid>* indicates the pod UID of the CoreDNS add-on, which can be obtained by running the following command:

      .. code-block::

         kubectl get po <pod name> -nkube-system -ojsonpath='{.metadata.uid}{"\n"}'

      In the preceding command, *<pod name>* indicates the name of the CoreDNS add-on running on the current node.

   -  *<CoreDNS container ID>* must be a complete container ID, which can be obtained by running the following command:

      Docker nodes:

      .. code-block::

         docker ps --no-trunc | grep coredns | awk '{print $1}'

      containerd nodes:

      .. code-block::

         crictl ps --no-trunc | grep coredns | awk '{print $1}'

   Example:

   .. code-block::

      cat /sys/fs/cgroup/cpu/kubepods/pod27f58662-3979-448e-8f57-09b62bd24ea6/6aa98c323f43d689ac47190bc84cf4fadd23bd8dd25307f773df25003ef0eef0/cpu.stat

   Pay attention to the following metrics:

   -  **nr_throttled**: number of times that traffic is limited.
   -  **throttled_time**: total duration of traffic limiting, in nanoseconds.

If the hostname and DNS settings are correct, you can use the following optimization policies.

**Optimization policies:**

#. Change the CoreDNS cache time.
#. Configure the stub domain.
#. Modify the value of **ndots**.

.. note::

   -  **Increasing the cache time of CoreDNS** helps resolve the same domain name for the *N* time, reducing the number of cascading DNS requests.
   -  **Configuring the stub domain** can reduce the number of DNS request links.

**How to modify:**

#. Modifying the CoreDNS cache time and configuring the stub domain:

   Restart the CoreDNS add-on after you modify the configurations.

#. Modifying **ndots**:

   :ref:`How Do I Optimize the Configuration If the External Domain Name Resolution Is Slow or Times Out? <cce_faq_00195>`

   Example:

   .. code-block::

            dnsConfig:
              options:
                - name: timeout
                  value: '2'
                - name: ndots
                  value: '5'
                - name: single-request-reopen

   You are advised to change the value of **ndots** to **2**.
