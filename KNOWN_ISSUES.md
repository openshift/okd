# Frequent issues in latest releases

## CannotRetrieveUpdates alert

  **Effected Versions:** All

  **Description:** Installed clusters throw `CannotRetrieveUpdates` alert, origin-release.svc.ci.openshift.org is unreachable.
  This domain was deprecated during CI Infra cleanup. Unfortunately we didn't rollout the fix to change this URL in OKD.

  **Workaround:** `oc patch clusterversion/version --patch '{"spec":{"upstream":"https://amd64.origin.releases.ci.openshift.org/graph"}}' --type=merge`

## [Provision node fails with fcos image 33.20210301.3.1 and later](https://github.com/openshift/okd/issues/566)
  **Effected Versions:** 4.7.0-0.okd-2021-02-25-144700 - 4.7.0-0.okd-2021-06-04-191031

  **Description:** Update Fedora CoreOS images are using a new method of extended attributes check, breaking extraction of embedded OS extensions RPMs.

  **Workaround:** Use FCOS 33.20210217.3.0 - [ISO](https://builds.coreos.fedoraproject.org/prod/streams/stable/builds/33.20210217.3.0/x86_64/fedora-coreos-33.20210217.3.0-live.x86_64.iso)

  Resolved in 4.7.0-0.okd-2021-06-13-090745 - latest stable FCOS can be used to create a cluster.

## [Invalid signature error when attempting cluster updates](https://github.com/openshift/okd/issues/605)
  **Effected Versions:** 4.7.0-0.okd-2021-04-11-124433 and 4.7.0-0.okd-2021-05-22-050008

  **Description:** Cluster upgrades will not start due to incorrect image signatures.

  **Workaround:** Perform the upgrade in the terminal using the --force and --allow-upgrade-with-warnings flags after first resetting any in-process upgrades.
  ```bash
  oc adm upgrade --reset
  oc adm upgrade --to-latest=true --force=true --allow-upgrade-with-warnings
  ```
  You can also patch the clusterversion configuration
  ```bash
  oc patch clusterversion version --type="merge" -p '{"spec":{"desiredUpdate":{"force":true}}}'
  ```

  New signing key is used in `4.7.0-0.okd-2021-05-22-050008`, so newer releases would pass signature check correctly.

## [Some optional operators from OCP are not available in OKD](https://github.com/openshift/okd/issues/456)
  **Effected Versions:** All

  **Description:** OCP users can install Logging/Serverless/GPU and other operators from Red Hat OperatorHub. This requires redhat.io pull secret, which may not be available for OKD users.

  **Workaround:** Start installation with a pull secret from cloud.redhat.com, enable new source in operatorhub settings.

  OKD-specific catalog with community versions of mentioned operators is in progress.

## [Systemd-resolved is not configured properly during install](https://github.com/openshift/okd/issues/690)
  **Effected Versions:**  4.7.0-0.okd-2021-06-13-090745 - 4.7.0-0.okd-2021-06-13-090745

  **Description:** With the newest stable release, systemd-resolved is misconfigured due to a missing directory.  This prevents nodes from installing properly.
  
  **Workaround** from each affected node do the following
* sudo su -
* mkdir /etc/systemd/resolved.conf.d
* reboot

