:original_name: cce_10_0793.html

.. _cce_10_0793:

Collecting Kubernetes Events
============================

The Cloud Native Log Collection add-on of CCE works with LTS to collect and store Kubernetes events and works with AOM to generate alarms.

Constraints
-----------

During Kubernetes event collection, the events generated in the following scenarios will not be collected:

-  Before otel-collector-event is started
-  During the active/standby otel-collector-event switchover
-  During configuration changes, for example, the following changes:

   -  An event collection policy is created or deleted.
   -  The blacklist and whitelist of Kubernetes events reported to AOM is modified.

-  During otel-collector-event listening interruption. You need to restart the interrupted listening.

Reporting Kubernetes Events to LTS
----------------------------------

To enable Kubernetes event collection, take the following steps.

Logging Has Been Enabled for a Cluster
--------------------------------------

If logging has been enabled for a cluster but Kubernetes event collection has not been enabled, or the corresponding log collection policy has been deleted, you can manually create a log collection policy by taking the following steps:

#. Log in to the CCE console.

#. Click the cluster name to access the cluster console. In the navigation pane, choose **Logging**.

#. In the upper right corner, click **View Log Collection Policies**.

   All log collection policies are displayed.

#. Click **Create Log Collection Policy**. Then, select **Kubernetes events** and click **OK**.

#. (After the creation is complete, you can view logs on the **Logging** page.) Select the log stream configured in the log collection policy to view the events reported to LTS.

.. _cce_10_0793__section1626024011253:

Reporting Kubernetes Events to AOM
----------------------------------

In 1.3.2 and later versions, the Cloud Native Log Collection add-on reports all warning events and some normal events to AOM by default. The reported events can be used to configure alarms. If the cluster version is 1.19.16, 1.21.11, 1.23.9, 1.25.4, or later, after the Cloud Native Log Collection add-on is installed, events will be reported to AOM by this add-on instead of the control plane component. After this add-on is uninstalled, events will not be reported to AOM.

**Custom Event Reporting**

If the reported events cannot meet requirements, you can modify the settings for the events.

Using kubectl
-------------

#. Run the following command on the cluster to modify the event collection settings:

   **kubectl edit logconfig -n kube-system default-event-aom**

#. Modify the event collection settings as required.

   .. code-block::

      apiVersion: logging.openvessel.io/v1
      kind: LogConfig
      metadata:
        annotations:
          helm.sh/resource-policy: keep
        name: default-event-aom
        namespace: kube-system
      spec:
        inputDetail:    # Settings on CCE from which events are collected
          type: event    # Type of logs to be collected from CCE. Do not change the value.
          event:
            normalEvents:    # Used to configure normal events
              enable: true    # Whether to enable normal event collection
              includeNames:    # Names of events to be collected. If this parameter is not specified, all events will be collected.
              - NotTriggerScaleUp
              excludeNames:    # Names of events that are not collected. If this parameter is not specified, all events will be collected.
              - ScaleDown
            warningEvents:    # Used to configure warning events
              enable: true    # Whether to enable warning event collection
              includeNames:    # Names of events to be collected. If this parameter is not specified, all events will be collected.
              - NotTriggerScaleUp
              excludeNames:    # Names of events that are not collected. If this parameter is not specified, all events will be collected.
              - ScaleDown
        outputDetail:
          type: AOM    # Type of the system that receives the events. Do not change the value.
          AOM:
            events:
            - name: DeleteNodeWithNoServer    # Event name. This parameter is mandatory.
              resourceType: Namespace    # Type of the resource that operations are performed on.
              severity: Major    # Event severity after an event is reported to AOM, which can be Critical, Major, Minor, or Info. The default value is Major.
