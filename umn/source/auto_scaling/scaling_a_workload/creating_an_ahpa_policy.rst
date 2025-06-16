:original_name: cce_10_0934.html

.. _cce_10_0934:

Creating an AHPA Policy
=======================

The native Horizontal Pod Autoscaler (HPA) in Kubernetes can cause delays in scaling due to its passive triggering mechanism. To address this issue, the Advanced Horizontal Pod Autoscaler (AHPA) proactively identifies periods of pod scaling and predicts future fluctuations by analyzing historical service metrics.

Features
--------

AHPA monitors historical workload metrics and performs weekly modeling, making it particularly effective for workloads with clear periodic patterns.

After AHPA is enabled, it collects monitoring data for a specific workload over a period of one to eight weeks. It then analyzes and models the data using statistical principles. Then, AHPA uses historical monitoring data and future service trends to recommend the optimal number of pods for a workload every minute. This allows pods to be prepared in advance to handle anticipated increases in service volume, ensuring adequate resource availability.

AHPA can work with HPA and CronHPA to scale pods in complex scenarios.

AHPA allows you to change the maximum and minimum number of pods in an HPA policy based on the recommended result, or directly adjust the number of pods in a Deployment.

AHPA and CronHPA share the same approach for adjusting the maximum and minimum pod numbers specified in an HPA policy. For details, see :ref:`Using CronHPA to Adjust the HPA Scaling Scope <cce_10_0415__section858719277369>`.

|image1|

Prerequisites
-------------

-  :ref:`CCE Advanced HPA <cce_10_0240>` of v1.5.2 or later has been installed in the cluster.
-  The Cloud Native Cluster Monitoring add-on has been installed in the cluster, and the **monitoring data is reported to AOM**. For details, see :ref:`Cloud Native Cluster Monitoring <cce_10_0406>`.

Notes and Constraints
---------------------

-  AHPA policies apply only to clusters of v1.23 or later.
-  For clusters of v1.19.10 and later, if an HPA policy is used to scale out a workload with EVS volumes mounted, a new pod cannot be started because EVS disks cannot be attached.
-  The specifications of the CCE Advanced HPA add-on are determined based on the total number of containers in the cluster and the number of scaling policies. Configure 500m CPU cores and 1000 MiB of memory for every 5000 containers, and 100m CPU cores and 500 MiB of memory for every 1000 scaling policies.
-  AHPA needs extra memory as it analyzes and processes historical workload data. It is advised to allocate 100m CPU cores and 300 MiB of memory for every 100 AHPA policies.
-  After an AHPA policy is created, the type of its associated workload cannot be changed.
-  Either an AHPA policy or a CustomedHPA policy can be enabled.

Using AHPA
----------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Deploy a sample workload. If a workload already runs in the cluster, skip this step. It is advised to use a workload that has been monitored for over seven days, as AHPA requires a minimum of seven days of monitoring data.

   .. code-block::

      kubectl create -f hamster.yaml

   Example configuration of **hamster.yaml**:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: hamster
      spec:
        selector:
          matchLabels:
            app: hamster
        replicas: 2
        template:
          metadata:
            labels:
              app: hamster
          spec:
            containers:
              - name: hamster
                image: registry.k8s.io/ubuntu-slim:0.1
                resources:
                  requests:
                    cpu: 100m
                    memory: 50Mi
                command: ["/bin/sh"]
                args:
                  - "-c"
                  - "while true; do timeout 0.5s yes >/dev/null; sleep 0.5s; done"

#. Create an AHPA task.

   .. code-block::

      kubectl create -f hamster-ahpa.yaml

   Example configuration of **hamster-vpa.yaml**:

   .. code-block::

      apiVersion: autoscaling.cce.io/v1alpha1
      kind: AdvancedHorizontalPodAutoscaler
      metadata:
        name: hamster-ahpa
        namespace: default
      spec:
        scaleTargetRef:  # Associated workload, which can only be Deployment/HPA
          apiVersion: apps/v1
          kind: Deployment
          name: hamster
        minReplicas: 2  # Minimum number of pods
        maxReplicas: 10  # Maximum number of pods
        metrics:  # Metrics, whose format is the same as that of the community HPA
        - type: Resource  # Metric source type, which can only be Resource
          resource:
            name: cpu  # Metric source name, which can only be CPU or memory
            target:
              type: Utilization  # Metric source type, which can only be Utilization
              averageUtilization: 50
        predictConfig:
          predictWindowSeconds: 1800
          stabilizationWindowSeconds: 1800
          quantile: "0.97"
        effectiveTime:
        - '* * 11-22 ? * MON-FRI'  # Valid from 11:00 to 22:00, from Monday to Friday

   .. table:: **Table 1** Key AHPA parameters

      +------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                | Mandatory | Description                                                                                                                                                                                                                                                                                                                             |
      +==========================================+===========+=========================================================================================================================================================================================================================================================================================================================================+
      | scaleTargetRef                           | Yes       | Target Deployment/HPA.                                                                                                                                                                                                                                                                                                                  |
      +------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | metrics                                  | Yes       | Metrics for scaling, which can only be CPU or memory. Only one metric can be configured. Either CPU or memory can be configured.                                                                                                                                                                                                        |
      +------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | maxReplicas                              | Yes       | Maximum number of pods, which ranges from 0 to 2147483647.                                                                                                                                                                                                                                                                              |
      +------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | minReplicas                              | Yes       | Minimum number of pods, which ranges from 0 to 2147483647.                                                                                                                                                                                                                                                                              |
      +------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | predictConfig.predictWindowSeconds       | Yes       | Recommendation time window, which starts from the current time. The historical monitoring data within this time window will be used to calculate the recommended number of pods. The value can range from 1 to 3600.                                                                                                                    |
      +------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | predictConfig.stabilizationWindowSeconds | No        | Scale-in cooling duration. The value ranges from 0 to 3600.                                                                                                                                                                                                                                                                             |
      +------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | predictConfig.quantile                   | Yes       | Prediction quantile, which indicates the probability that a service metric's actual value will be lower than the preset target value. A higher value indicates a more conservative estimate. The value ranges from 0 to 1. Two decimal places are supported. The default value is 0.99. The recommended value ranges from 0.90 to 0.99. |
      +------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | effectiveTime                            | No        | If multiple cron expressions are specified, AHPA will take effect on the combined set of these expressions. The default setting is always valid.                                                                                                                                                                                        |
      +------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. After AOM has collected at least seven days of monitoring data for the target workload, AHPA can create a model and suggest the appropriate number of pods. Wait for the recommended number of pods to be provided and run the following command to check AHPA resource details:

   .. code-block::

      kubectl get ahpa hamster-ahpa -oyaml

   Command output:

   .. code-block::

      apiVersion: autoscaling.cce.io/v1alpha1
      kind: AdvancedHorizontalPodAutoscaler
      metadata:
        creationTimestamp: "2024-10-07T13:11:58Z"
        generation: 2
        name: hamster-ahpa
        namespace: default
        resourceVersion: "15529454"
        uid: e5ffbb01-50b0-4485-8cf5-bc2be884b1ee
      spec:
        effectiveTime:
        - '* * 11-22 ? * MON-FRI'
        maxReplicas: 10
        metrics:
        - resource:
            name: cpu
            target:
              averageUtilization: 50
              type: Utilization
          type: Resource
        minReplicas: 2
        predictConfig:
          predictWindowSeconds: 1800
          quantile: "0.97"
          stabilizationWindowSeconds: 1800
        scaleTargetRef:
          apiVersion: apps/v1
          kind: Deployment
          name: hamster
      status:
        conditions:
        - lastTransitionTime: "2024-10-07T13:24:19Z"
          message: the AHPA's model is ready
          reason: ModelIsReady
          status: "True"
          type: ModelAvailable
        - lastTransitionTime: "2024-10-07T13:24:19Z"
          message: the AHPA was able to successfully calculate a replica count
          reason: SucceededRunPrediction
          status: "True"
          type: ScalingActive
        - lastTransitionTime: "2024-10-07T13:24:19Z"
          message: ths ahpa checkpoint is fresh
          reason: CheckpointIsFresh
          status: "True"
          type: CheckpointAvailable
        - lastTransitionTime: "2024-10-07T13:24:19Z"
          message: recommended size matches current size
          reason: ReadyForNewScale
          status: "True"
          type: AbleToScale
        - lastTransitionTime: "2024-10-07T13:24:19Z"
          message: the desired replica count is more than the maximum replica count
          reason: TooManyReplicas
          status: "True"
          type: ScalingLimited
        currentReplicas: 10
        desiredReplicas: 10
        lastScaleTime: "2024-10-07T13:24:19Z"

#. If you no longer need an AHPA policy, run the following command to delete it:

   .. code-block::

      kubectl delete ahpa hamster-ahpa

   During the validity period of AHPA, you can use custom ahpacheckpoint resources to keep the recommended settings for the next 6 hours. If you do not need this configuration, manually delete it.

   .. code-block::

      kubectl delete ahpacheckpoint hamster-ahpa

.. |image1| image:: /_static/images/en-us_image_0000002218660018.png
