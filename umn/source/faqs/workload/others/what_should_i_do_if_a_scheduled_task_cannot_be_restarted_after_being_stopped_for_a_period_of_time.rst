:original_name: cce_faq_00213.html

.. _cce_faq_00213:

What Should I Do If a Scheduled Task Cannot Be Restarted After Being Stopped for a Period of Time?
==================================================================================================

If a scheduled task is stopped during running, before its restart, the system calculates the difference between the last time the task was successfully executed and the current time and compares the time difference with the scheduled task period multiplied by 100. If the time difference is greater than the period multiplied by 100, the scheduled task will not be triggered again. For details, see `CronJob Limitations <https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/>`__.

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
