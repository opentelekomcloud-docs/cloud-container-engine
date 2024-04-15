:original_name: cce_10_0422.html

.. _cce_10_0422:

Converting a Release from Helm v2 to v3
=======================================

Context
-------

CCE fully supports Helm v3. This section guides you to convert a Helm v2 release to Helm v3. Helm v3 discards or reconstructs some Helm v2 functions at the bottom layer. Therefore, the conversion is risky to some extent. Simulation is required before conversion.

For details, see the `community documentation <https://github.com/helm/helm-2to3>`__.

Precautions
-----------

-  Helm v2 stores release information in ConfigMaps. Helm v3 does so in secrets.
-  When you query, update, or operate a Helm v2 release on the CCE console, CCE will attempt to convert the release to v3. If you operate in the background, convert the release by following the instructions below.

Conversion Process (Without Using the Helm v3 Client)
-----------------------------------------------------

#. Download the helm 2-to-3 conversion plugin on the CCE node.

   .. code-block::

      wget https://github.com/helm/helm-2to3/releases/download/v0.10.2/helm-2to3_0.10.2_linux_amd64.tar.gz

2. Decompress the plugin package.

   .. code-block::

      tar -xzvf helm-2to3_0.10.2_linux_amd64.tar.gz

3. Perform the simulated conversion.

   Take the test-convert release as an example. Run the following command to simulate the conversion: If the following information is displayed, the simulation is successful.

   .. code-block::

      # ./2to3 convert --dry-run --tiller-out-cluster -s configmaps test-convert
      NOTE: This is in dry-run mode, the following actions will not be executed.
      Run without --dry-run to take the actions described below:
      Release "test-convert" will be converted from Helm v2 to Helm v3.
      [Helm 3] Release "test-convert" will be created.
      [Helm 3] ReleaseVersion "test-convert.v1" will be created.

4. Perform the conversion. If the following information is displayed, the conversion is successful.

   .. code-block::

      # ./2to3 convert --tiller-out-cluster -s configmaps test-convert
      Release "test-convert" will be converted from Helm v2 to Helm v3.
      [Helm 3] Release "test-convert" will be created.
      [Helm 3] ReleaseVersion "test-convert.v1" will be created.
      [Helm 3] ReleaseVersion "test-convert.v1" created.
      [Helm 3] Release "test-convert" created.
      Release "test-convert" was converted successfully from Helm v2 to Helm v3.
      Note: The v2 release information still remains and should be removed to avoid conflicts with the migrated v3 release.
      v2 release information should only be removed using `helm 2to3` cleanup and when all releases have been migrated over.

5. After the conversion is complete, simulate the resource clearance. After the simulation, clear the v2 release resources.

   Simulated clearance:

   .. code-block::

      # ./2to3 cleanup --dry-run --tiller-out-cluster -s configmaps --name test-convert
      NOTE: This is in dry-run mode, the following actions will not be executed.
      Run without --dry-run to take the actions described below:
      WARNING: "Release 'test-convert' Data" will be removed.

      [Cleanup/confirm] Are you sure you want to cleanup Helm v2 data? [y/N]: y
      Helm v2 data will be cleaned up.
      [Helm 2] Release 'test-convert' will be deleted.
      [Helm 2] ReleaseVersion "test-convert.v1" will be deleted.

   Formal clearance:

   .. code-block::

      # ./2to3 cleanup --tiller-out-cluster -s configmaps --name test-convert
      WARNING: "Release 'test-convert' Data" will be removed.

      [Cleanup/confirm] Are you sure you want to cleanup Helm v2 data? [y/N]: y
      Helm v2 data will be cleaned up.
      [Helm 2] Release 'test-convert' will be deleted.
      [Helm 2] ReleaseVersion "test-convert.v1" will be deleted.
      [Helm 2] ReleaseVersion "test-convert.v1" d

Conversion Process (Using the Helm v3 Client)
---------------------------------------------

#. Install the Helm v3 client. For details, see :ref:`Installing Helm v3 <cce_10_0144__en-us_topic_0226102212_en-us_topic_0179003017_section3719193213815>`.

#. Install the conversion plugin.

   .. code-block::

      # helm plugin install https://github.com/helm/helm-2to3
      Downloading and installing helm-2to3 v0.10.2 ...
      https://github.com/helm/helm-2to3/releases/download/v0.10.2/helm-2to3_0.10.2_linux_amd64.tar.gz
      Installed plugin: 2to3

#. Check whether the plugin has been installed.

   .. code-block::

      # helm plugin list
      NAME VERSION DESCRIPTION
      2to3  0.10.2      migrate and cleanup Helm v2 configuration and releases in-place to Helm v3

#. Perform the simulated conversion.

   Take the test-convert release as an example. Run the following command to simulate the conversion: If the following information is displayed, the simulated conversion is successful.

   .. code-block::

      # helm 2to3 convert --dry-run --tiller-out-cluster -s configmaps test-convert
      NOTE: This is in dry-run mode, the following actions will not be executed.
      Run without --dry-run to take the actions described below:
      Release "test-convert" will be converted from Helm v2 to Helm v3.
      [Helm 3] Release "test-convert" will be created.
      [Helm 3] ReleaseVersion "test-convert.v1" will be created.

#. Perform the conversion. If the following information is displayed, the conversion is successful.

   .. code-block::

      # helm 2to3 convert --tiller-out-cluster -s configmaps test-convert
      Release "test-convert" will be converted from Helm v2 to Helm v3.
      [Helm 3] Release "test-convert" will be created.
      [Helm 3] ReleaseVersion "test-convert.v1" will be created.
      [Helm 3] ReleaseVersion "test-convert.v1" created.
      [Helm 3] Release "test-convert" created.
      Release "test-convert" was converted successfully from Helm v2 to Helm v3.
      Note: The v2 release information still remains and should be removed to avoid conflicts with the migrated v3 release.
      v2 release information should only be removed using `helm 2to3` cleanup and when all releases have been migrated over.

#. After the conversion, you can view the converted release by running **helm list**.

   .. code-block::

      # helm list
      NAME                NAMESPACE    REVISION UPDATED                           STATUS      CHART              APP VERSION
      test-convert      default   1                2022-08-29 06:56:28.166918487 +0000 UTC       deployed    test-helmold-1

#. After the conversion is complete, simulate the resource clearance. After the simulation, clear the v2 release resources.

   Simulated clearance:

   .. code-block::

      # helm 2to3 cleanup --dry-run --tiller-out-cluster -s configmaps --name test-convert
      NOTE: This is in dry-run mode, the following actions will not be executed.
      Run without --dry-run to take the actions described below:
      WARNING: "Release 'test-convert' Data" will be removed.

      [Cleanup/confirm] Are you sure you want to cleanup Helm v2 data? [y/N]: y
      Helm v2 data will be cleaned up.
      [Helm 2] Release 'test-convert' will be deleted.
      [Helm 2] ReleaseVersion "test-convert.v1" will be deleted.

   Formal clearance:

   .. code-block::

      # helm 2to3 cleanup --tiller-out-cluster -s configmaps --name test-convert
      WARNING: "Release 'test-convert' Data" will be removed.

      [Cleanup/confirm] Are you sure you want to cleanup Helm v2 data? [y/N]: y
      Helm v2 data will be cleaned up.
      [Helm 2] Release 'test-convert' will be deleted.
      [Helm 2] ReleaseVersion "test-convert.v1" will be deleted.
      [Helm 2] ReleaseVersion "test-convert.v1" deleted.
      [Helm 2] Release 'test-convert' deleted.
      Helm v2 data was cleaned up successfully.
