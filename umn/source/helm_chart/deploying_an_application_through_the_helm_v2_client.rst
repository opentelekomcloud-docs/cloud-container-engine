:original_name: cce_10_0420.html

.. _cce_10_0420:

Deploying an Application Through the Helm v2 Client
===================================================

Prerequisites
-------------

The Kubernetes cluster created on CCE has been connected to kubectl. For details, see :ref:`Using kubectl <cce_10_0107__section37321625113110>`.

Installing Helm v2
------------------

This section uses Helm v2.17.0 as an example.

For other versions, visit https://github.com/helm/helm/releases.

#. Download the Helm client from the VM connected to the cluster.

   .. code-block::

      wget https://get.helm.sh/helm-v2.17.0-linux-amd64.tar.gz

#. Decompress the Helm package.

   .. code-block::

      tar -xzvf helm-v2.17.0-linux-amd64.tar.gz

#. Copy Helm to the system path, for example, **/usr/local/bin/helm**.

   .. code-block::

      mv linux-amd64/helm /usr/local/bin/helm

#. RBAC is enabled on the Kubernetes API server. Create the service account name **tiller** for the tiller and assign cluster-admin, a system ClusterRole, to the tiller. Create a tiller resource account as follows:

   **vim tiller-rbac.yaml**

   .. code-block::

      apiVersion: v1
      kind: ServiceAccount
      metadata:
        name: tiller
        namespace: kube-system
      ---
      apiVersion: rbac.authorization.k8s.io/v1
      kind: ClusterRoleBinding
      metadata:
        name: tiller
      roleRef:
        apiGroup: rbac.authorization.k8s.io
        kind: ClusterRole
        name: cluster-admin
      subjects:
        - kind: ServiceAccount
          name: tiller
          namespace: kube-system

#. Deploy the tiller resource account.

   .. code-block::

      kubectl apply -f tiller-rbac.yaml

#. Initialize the Helm and deploy the pod of tiller.

   .. code-block::

      helm init --service-account tiller --skip-refresh

#. Query the status.

   .. code-block::

      kubectl get pod -n kube-system -l app=helm

   Command output:

   .. code-block::

      NAME                             READY   STATUS    RESTARTS   AGE
      tiller-deploy-7b56c8dfb7-fxk5g   1/1     Running   1          23h

#. Query the Helm version.

   .. code-block::

      # helm version
      Client: &version.Version{SemVer:"v2.17.0", GitCommit:"a690bad98af45b015bd3da1a41f6218b1a451dbe", GitTreeState:"clean"}
      Server: &version.Version{SemVer:"v2.17.0", GitCommit:"a690bad98af45b015bd3da1a41f6218b1a451dbe", GitTreeState:"clean"}

Installing the Helm Chart
-------------------------

If the charts provided by CCE do not meet requirements, download a chart and install it.

You can obtain the required chart in the **stable** directory on this `website <https://github.com/helm/charts>`__, download the chart, and upload it to the node.

#. Download and decompress the obtained chart. Generally, the chart is in ZIP format.

   .. code-block::

      unzip chart.zip

#. Install the Helm chart.

   .. code-block::

      helm install aerospike/

#. After the installation is complete, run the **helm list** command to check the status of the chart releases.

Common Issues
-------------

-  The following error message is displayed after the **Helm version** command is run:

   .. code-block::

      Client:
      &version.Version{SemVer:"v2.17.0",
      GitCommit:"a690bad98af45b015bd3da1a41f6218b1a451dbe", GitTreeState:"clean"}
      E0718 11:46:10.132102    7023 portforward.go:332] an error occurred
      forwarding 41458 -> 44134: error forwarding port 44134 to pod
      d566b78f997eea6c4b1c0322b34ce8052c6c2001e8edff243647748464cd7919, uid : unable
      to do port forwarding: socat not found.
      Error: cannot connect to Tiller

   The preceding information is displayed because the socat is not installed. Run the following command to install the socat:

   .. code-block::

      yum install socat -y

-  When you run the **yum install socat -y** command on a node running EulerOS 2.9, the following error message is displayed:

   No match for argument: socat

   Error: Unable to find a match: socat

   The image does not contain socat. In this case, manually download the RPM chart and run the following command to install it (replace the RPM chart name with the actual one):

   .. code-block::

      rpm -i socat-1.7.3.2-8.oe1.x86_64.rpm

-  When the socat has been installed and the following error message is displayed after the **helm version** command is run:

   .. code-block::

      test@local:~/k8s/helm/test$ helm version
      Client: &version.Version{SemVer:"v3.3.0", GitCommit:"021cb0ac1a1b2f888144ef5a67b8dab6c2d45be6", GitTreeState:"clean"}
      Error: cannot connect to Tiller

   The Helm chart reads the configuration certificate from the **.Kube/config** file to communicate with Kubernetes. The preceding error indicates that the kubectl configuration is incorrect. In this case, reconnect the cluster to kubectl. For details, see :ref:`Using kubectl <cce_10_0107__section37321625113110>`.

-  Storage fails to be created after you have connected to cloud storage services.

   This issue may be caused by the **annotation** field in the created PVC. Change the chart name and install the chart again.

-  If kubectl is not properly configured, the following error message is displayed after the **helm install** command is run:

   .. code-block:: console

      [root@prometheus-57046 ~]# helm install prometheus/ --generate-name
      WARNING: This chart is deprecated
      Error: Kubernetes cluster unreachable: Get "http://localhost:8080/version?timeout=32s": dial tcp [::1]:8080: connect: connection refused

   **Solution**: Configure kubeconfig for the node. For details, see :ref:`Using kubectl <cce_10_0107__section37321625113110>`.
