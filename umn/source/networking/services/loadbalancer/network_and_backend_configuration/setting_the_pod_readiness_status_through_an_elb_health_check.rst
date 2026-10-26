:original_name: cce_10_0685.html

.. _cce_10_0685:

Setting the Pod Readiness Status Through an ELB Health Check
============================================================

During a rolling upgrade, a load balancer's backend server group may momentarily lag behind the new pod set, causing a brief traffic drop. To resolve this issue, CCE allows you to associate the readiness status of a pod with the ELB health check. The pod is declared ready only after its ELB health check is successful. This association works with the **strategy.rollingUpdate.maxSurge** and **strategy.rollingUpdate.maxUnavailable** parameters of the pod for graceful rolling upgrades.

Working Principles
------------------

If a LoadBalancer Service accesses ELB and its associated pods are being upgraded gradually, some requests may still be routed to the terminated pods before ELB adds the new ones to the backend. This is because the new pods often start faster than ELB updates its backend configuration. This delay often results in temporary access failures.

Readiness gates are an extended readiness check strategy provided by Kubernetes for pods. They ensure that a pod is marked as ready and starts to receive traffic only when all custom conditions are met.

After **readinessGates** is configured for a pod, the pod readiness check process is as follows:

#. kubelet checks if containers are ready. After the containers are started, kubelet will consider them ready (**ContainersReady=True**). However, the pod remains unavailable until other custom readiness gates pass checks.
#. kubelet checks the health check result of the Service specified by **readinessGates** configured for the pod. The next step is performed only when the health check is successful.
#. kubelet sets **Ready** to **True** for the pod only after confirming that all **readinessGates** conditions of the ELB health check are **True**. The next step is to terminate the old pod to complete the rolling upgrade.

Notes and Constraints
---------------------

-  This function is available in the following versions:

   -  v1.19: v1.19.16-r5 or later
   -  v1.21: v1.21.8-r0 or later
   -  v1.23: v1.23.6-r0 or later
   -  v1.25: v1.25.2-r0 or later
   -  Versions later than v1.25

-  This function is only supported in CCE Turbo clusters where dedicated load balancers are used.
-  To use this function, configure the readinessGates field in the pod and specify the label **target-health.elb.k8s.cce/**\ *{serviceName}*, where *{serviceName}* indicates the service name.
-  The pod readiness status takes effect only when the ELB backend is initially connected. The subsequent health check result does not affect the pod readiness status.


Setting the Pod Readiness Status Through an ELB Health Check
------------------------------------------------------------

To use pod readiness gates, take the following steps:

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Workloads**. In the upper right corner, click **Create from YAML**.

   YAML content:

   .. code-block::

      kind: Deployment
      apiVersion: apps/v1
      metadata:
        name: nginx
        namespace: default
        labels:
          version: v1
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: nginx
            version: v1
        template:
          metadata:
            labels:
              app: nginx
              version: v1
          spec:
            containers:
              - name: container-1
                image: nginx:latest
            imagePullSecrets:
              - name: default-secret
            readinessGates:
              - conditionType: target-health.elb.k8s.cce/specific-service-name  # Specifies the ServiceName.
        strategy:
          type: RollingUpdate
          rollingUpdate:
            maxUnavailable: 25%           # Works with the following two parameters to control the number of ELB backends and implement graceful rolling upgrade.
            maxSurge: 25%

#. Click **OK** to create the workload. In the workload list, check the workload status. You can find that the workload is not ready and the pod status is abnormal.

   An event is displayed as follows:

   .. code-block::

      Pod not ready: corresponding condition of pod readiness gate "target-health.elb.k8s.cce/specific-service-name" does not exist.

#. In the navigation pane, choose **Services and Ingresses**. In the upper right corner, click **Create Service** and configure the following parameters:

   -  **Service Name**: The value must be the same as the value of **readinessGates** in the pod.
   -  **Service Type**: Select **LoadBalancer**.
   -  **Selector**: Click **Reference Workload Label**, select the workload created in the previous step, and click **OK**.
   -  **Load Balancer**: Dedicated load balancers must be used. You can select an existing load balancer or automatically create a load balancer.
   -  **Health Check**: Whether to enable health check. (If it is not configured, the health check is enabled by default.)

#. Go to the ELB console and check the backend server group. The health check result is normal.

#. On the CCE console, the workload is in the **Running** status.
