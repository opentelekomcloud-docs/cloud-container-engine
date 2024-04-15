:original_name: cce_10_0685.html

.. _cce_10_0685:

Setting the Pod Ready Status Through the ELB Health Check
=========================================================

The ready status of the pod is associated with the ELB health check. After the health check is successful, the pod is ready. This association works with the **strategy.rollingUpdate.maxSurge** and **strategy.rollingUpdate.maxUnavailable** parameters of the pod to implement graceful rolling upgrade.

Constraints
-----------

-  This feature takes effect only in the following versions:

   -  v1.19: v1.19.16-r5 or later
   -  v1.21: v1.21.8-r0 or later
   -  v1.23: v1.23.6-r0 or later
   -  v1.25: v1.25.2-r0 or later

-  This function applies only to passthrough scenarios, that is, scenarios where dedicated load balancers are used in CCE Turbo clusters.
-  To use this function, configure the readinessGates field in the pod and specify the label **target-health.elb.k8s.cce/**\ *{serviceName}*, where *{serviceName}* indicates the service name.
-  The pod ready status takes effect only when the ELB backend is initially connected. The subsequent health check status does not affect the pod ready status.


Setting the Pod Ready Status Through the ELB Health Check
---------------------------------------------------------

To use Pod readiness Gates, perform the following steps:

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

#. Click **OK**. On the workload list, you can check the workload status and find that the pod is not ready.

   An event is displayed as follows:

   .. code-block::

      Pod not ready: correspondingcondition of pod readinessgate "target-health.elb.k8s.cce/specific-service-name" does not exist.

#. In the navigation pane, choose **Services & Ingresses**. In the upper right corner, click **Create Service** and configure the following parameters:

   -  **Service Name**: The value must be the same as the value of **readinessGates** in the pod.
   -  **Service Type**: Select **LoadBalancer**.
   -  **Selector**: Click **Reference Workload Label**, select the workload created in the previous step, and click **OK**.
   -  **Load Balancer**: Dedicated load balancers must be used. You can select an existing load balancer or automatically create a load balancer.
   -  **Health Check**: Whether to enable health check. (If it is not configured, the health check is enabled by default.)

#. Go to the ELB console and check the backend server group. The health check status is normal.

#. On the CCE console, the workload is in the **Running** status.
