# medical-diagnosis + UDN demo
A hodgepodge demo to stress various elements of a cluster

## WARNING ** SIZING AND TECH PREVIEW FEATURES **
A strict order of operations is required to get this to boot.
  
1. Install a cluster. Make sure you have metal nodes available if in AWS.
   1. Control plane on AWS `m5.4xlarge` or larger are known to work (most likely `m5.2xlarge` are sufficient)
   2. Use `m5.metal` on aws for virt control plane.
2. Ensure you are on 4.17 - Note that `openshift-installer` increments per release. Download the latest to avoid upgrades.
3. First enable the featuregate to enable UDNs
  ```
  apiVersion: config.openshift.io/v1
  kind: FeatureGate
  metadata:
    name: cluster
  spec:
    featureSet: TechPreviewNoUpgrade
  ```
1. Enable multi-network policy 
   1. Do so in the UI -> Network -> Network policies.

2. Buckets: 
   1. Buckets are global and therefore not necessarily reproduceable. Currently creating by hand and update config
      1. One bucket for loki
      2. Another for the xrays
3. Update `values-global.yaml` including:
   1. `global.datacenter.region`
   2. `global.datacenter.clustername`
   3. `global.datacenter.domain`
   4. `global.xraylab.s3.bucketsource`

4. Install the validated pattern.

This is primarily as with odf layered into the environment there are more constrains on updates which make your life painful.








# See below for baseline information

# Medical Diagnosis Validated Pattern

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[Live build status](https://validatedpatterns.io/ci/?pattern=medicaldiag)

## XRay analysis automated pipeline

This Validated Pattern is based on a demo implemetation of an automated data pipeline for chest Xray
analysis previously developed by Red Hat.  The original demo can be found [here](https://github.com/red-hat-data-services/jumpstart-library]).

The Validated Pattern includes the same functionality as the original demonstration.  The difference is
that we use the *GitOps* to deploy most of the components which includes operators, creation of namespaces,
and cluster configuration.

The Validated Pattern includes:

* Ingest chest Xrays into an object store based on Ceph.
* The Object store sends notifications to a Kafka topic.
* A KNative Eventing Listener to the topic triggers a KNative Serving function.
* An ML-trained model running in a container makes a risk of Pneumonia assessment for incoming images.
* A Grafana dashboard displays the pipeline in real time, along with images incoming, processed and anonymized, as well as full metrics.

This pipeline is showcased [in this video](https://www.youtube.com/watch?v=zja83FVsm14).

![Pipeline dashboard](doc/dashboard.png)

## Check the values files before deployment

You can run a check before deployment to make sure that you have the required variables to deploy the
Medical Diagnosis Validated Pattern.

You can run `make predeploy` to check your values. This will allow you to review your values and changed them in
the case there are typos or old values.  The values files that should be reviewed prior to deploying the
Medical Diagnosis Validated Pattern are:

| Values File | Description |
| ----------- | ----------- |
| values-secret.yaml | This is the values file that will include the xraylab section with all the database secrets |
| values-global.yaml | File that is used to contain all the global values used by Helm |

Make sure you have the correct domain, clustername, externalUrl, targetBucket and bucketSource values.

[![asciicast](https://github.com/claudiol/medical-diagnosis/blob/claudiol-xray-deployment/doc/predeploy.svg)](https://github.com/claudiol/medical-diagnosis/blob/claudiol-xray-deployment/doc/predeploy.svg)

Then you can run `make install` to deploy the Medical Diagnosis Validated Pattern.

[![asciicast](https://github.com/claudiol/medical-diagnosis/blob/claudiol-xray-deployment/doc/xray-deployment.svg)](https://github.com/claudiol/medical-diagnosis/blob/claudiol-xray-deployment/doc/xray-deployment.svg)

This validated pattern is still being developed.  More to come in the next few weeks. Any questions or concerns
please contact [Jonny Rickard](jrickard@redhat.com) or [Lester Claudio](claudiol@redhat.com).
