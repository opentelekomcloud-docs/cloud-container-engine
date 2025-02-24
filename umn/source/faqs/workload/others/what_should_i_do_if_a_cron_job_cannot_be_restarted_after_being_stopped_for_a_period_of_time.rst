:original_name: cce_faq_00213.html

.. _cce_faq_00213:

What Should I Do If a Cron Job Cannot Be Restarted After Being Stopped for a Period of Time?
============================================================================================

When a cron job is paused mid-execution and later resumed, the controller checks the number of missed scheduling times between the last scheduled time and the current time. If this number exceeds 100, the controller will not start the job and logs the error. For details, see `CronJob limitations <https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/#cronjob-limitations>`__.

.. code-block::

   Cannot determine if job needs to be started. Too many missed start time (> 100). Set or decrease .spec.startingDeadlineSeconds or check clock skew.

For example, assume that a cron job is set to create a job every minute from 08:30:00 and the **startingDeadlineSeconds** field is not set. If the cron job controller stops running from 08:29:00 to 10:21:00, the job will not be started because the time difference between 08:29:00 and 10:21:00. 00 exceeds 100 minutes, that is, the number of missed scheduling times exceeds 100 (in the example, a scheduling period is 1 minute).

If the **startingDeadlineSeconds** field is set, the controller calculates the number of missed jobs in the last *x* seconds (*x* indicates the value of **startingDeadlineSeconds**). For example, if **startingDeadlineSeconds** is set to **200**, the controller counts the number of jobs missed in the last 200 seconds. In this case, if the cron job controller stops running from 08:29:00 to 10:21:00, the job will start again at 10:22:00, because only three scheduling requests are missed in the last 200 seconds (in the example, one scheduling period is 1 minute).

Solution
--------

Configure the **startingDeadlineSeconds** parameter in a cron job. This parameter can be created or modified only by using kubectl or APIs.

Example YAML:

.. code-block::

   apiVersion: batch/v1
   kind: CronJob
   metadata:
     name: hello
   spec:
     startingDeadlineSeconds: 200
     schedule: "* * * * *"
     jobTemplate:
       spec:
         template:
           spec:
             containers:
             - name: hello
               image: busybox:1.28
               imagePullPolicy: IfNotPresent
               command:
               - /bin/sh
               - -c
               - date; echo Hello
             restartPolicy: OnFailure

If you create a cron job again, you can temporarily avoid this issue.
