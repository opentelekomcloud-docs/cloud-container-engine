:original_name: cce_10_0493.html

.. _cce_10_0493:

Checking CoreDNS Configuration Consistency
==========================================

Check Item
----------

Check whether the current CoreDNS key configuration Corefile is different from the Helm Release record. The difference may be overwritten during the add-on upgrade, affecting domain name resolution in the cluster.

Solution
--------

You can upgrade the coredns add-on separately after confirming the configuration differences.

#. For details about how to configure kubectl, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. .. _cce_10_0493__en-us_topic_0000001548755413_li1178291934910:

   Obtain the Corefile that takes effect currently.

   .. code-block::

      kubectl get cm -nkube-system coredns -o jsonpath='{.data.Corefile}' > corefile_now.txt
      cat corefile_now.txt

#. .. _cce_10_0493__en-us_topic_0000001548755413_li111544111811:

   Obtain the Corefile in the Helm Release record (depending on Python 3).

   .. code-block::

      latest_release=`kubectl get secret -nkube-system -l owner=helm -l name=cceaddon-coredns --sort-by=.metadata.creationTimestamp | awk 'END{print $1}'`
      kubectl get secret -nkube-system $latest_release -o jsonpath='{.data.release}' | base64 -d | base64 -d | gzip -d | python -m json.tool | python -c "
      import json,sys,re,yaml;
      manifests = json.load(sys.stdin)['manifest']
      files = re.split('(?:^|\s*\n)---\s*',manifests)
      for file in files:
          if 'coredns/templates/configmap.yaml' in file and 'Corefile' in file:
            corefile = yaml.safe_load(file)['data']['Corefile']
            print(corefile,end='')
            exit(0);
      print('error')
      exit(1);
      " > corefile_record.txt
      cat corefile_record.txt

#. Compare the output information of :ref:`2 <cce_10_0493__en-us_topic_0000001548755413_li1178291934910>` and :ref:`3 <cce_10_0493__en-us_topic_0000001548755413_li111544111811>`.

   .. code-block::

      diff corefile_now.txt corefile_record.txt -y;

   |image1|

#. Return to the CCE console and click the cluster name to go to the cluster console. On the **Add-ons** page, select the coredns add-on and click **Upgrade**.

   To retain the differentiated configurations, use either of the following methods:

   -  Set **parameterSyncStrategy** to **force**. You need to manually enter the differentiated configurations. For details, see :ref:`coredns (System Resource Add-On, Mandatory) <cce_10_0129>`.
   -  If **parameterSyncStrategy** is set to **inherit**, differentiated configurations are automatically inherited. The system automatically parses, identifies, and inherits differentiated parameters.

   |image2|

#. Click **OK**. After the add-on upgrade is complete, check whether all CoreDNS instances are available and whether the Corefile meets the expectation.

   .. code-block::

      kubectl get cm -nkube-system coredns -o jsonpath='{.data.Corefile}'

#. Change the value of **parameterSyncStrategy** to **ensureConsistent** to enable configuration consistency verification.

   Use the parameter configuration function of CCE add-on management to modify the Corefile configuration to avoid differences.

.. |image1| image:: /_static/images/en-us_image_0000001628843805.png
.. |image2| image:: /_static/images/en-us_image_0000001578443828.png
