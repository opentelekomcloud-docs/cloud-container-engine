:original_name: cce_10_0493.html

.. _cce_10_0493:

CoreDNS Configuration Consistency
=================================

Check Items
-----------

Check whether the current CoreDNS key configuration Corefile is different from the Helm release record. The difference may be overwritten during the add-on upgrade, **affecting domain name resolution in the cluster**.

Solution
--------

You can upgrade CoreDNS separately after confirming the configuration differences.

#. Configure the **kubectl** command. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. .. _cce_10_0493__li1178291934910:

   Obtain the Corefile that takes effect currently.

   .. code-block::

      kubectl get cm -nkube-system coredns -o jsonpath='{.data.Corefile}' > corefile_now.txt
      cat corefile_now.txt

#. .. _cce_10_0493__li111544111811:

   Obtain the Corefile in the Helm Release records.

   .. code-block::

      latest_release=`kubectl get secret -nkube-system -l owner=helm -l name=cceaddon-coredns --sort-by=.metadata.creationTimestamp | awk 'END{print $1}'`
      kubectl get secret -nkube-system $latest_release -o jsonpath='{.data.release}' | base64 -d | base64 -d | gzip -d | python -m json.tool | python -c "
      from __future__ import print_function
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

#. Compare the output differences between :ref:`2 <cce_10_0493__li1178291934910>` and :ref:`3 <cce_10_0493__li111544111811>`.

   .. code-block::

      diff corefile_now.txt corefile_record.txt -y;


   .. figure:: /_static/images/en-us_image_0000001851587144.png
      :alt: **Figure 1** Viewing output differences

      **Figure 1** Viewing output differences

#. Return to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane, select CoreDNS, and click **Upgrade**.

   To retain custom configurations, use either of the following methods:

   -  (Recommended) Set **parameterSyncStrategy** to **inherit**. In this case, custom settings are automatically inherited. The system automatically parses, identifies, and inherits custom parameters.
   -  Set **parameterSyncStrategy** to **force**. Manually enter the differential configuration. For details, see :ref:`CoreDNS <cce_10_0129>`.

#. Click **OK**. After the add-on upgrade is complete, check whether all CoreDNS instances are available and whether Corefile meets the expectation.

   .. code-block::

      kubectl get cm -nkube-system coredns -o jsonpath='{.data.Corefile}'

#. Change the value of **parameterSyncStrategy** to **ensureConsistent** to enable configuration consistency verification.

   In addition, it is a good practice to use the parameter configuration function of CCE add-ons to modify the Corefile configuration for consistency.
