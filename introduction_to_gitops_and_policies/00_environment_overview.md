# Environment Overview

**OpenShift Clusters**

* 1 HUB Cluster Running OCP 4.3.9
  * ACM Snapshot Used: 1.0.0-SNAPSHOT-2020-04-07-08-16-29
* 1 SPOKE Cluster Running OCP 4.4.0
  * Cluster Labels: 
    * cloud: AWS
    * env: **dev**
    * name: spoke
    * region: EU
    * vendor: Amazon
* 1 SPOKE Cluster Running OCP 4.4.0
  * Cluster Labels: 
    * cloud: AWS
    * env: **pro**
    * name: spoke2
    * region: US
    * vendor: Amazon

![ACM Env Overview](assets/acm-env.png)

**GitOps Repository**

* Repo URL: https://github.com/mvazquezc/acm-testing
* Repo Branches:
  * master -> Stores the ACM Manifests used during this write-up
  * config -> Stores the base files for our APPs that apply to every environment
  * stage  -> Stores the overlay files for our APPs that apply to staging environments
  * prod   -> Stores the overlay files for our APPs that apply to production environments
  * infra  -> Stores manifests related to cluster infrastructure

# OC Tool Context Configuration

The following steps will leverage the usage of `contexts` within the `oc` tool.

We will be using three contexts:

* `hub`    -> Context for accesing HUB Cluster 
* `spoke`  -> Context for accesing SPOKE Cluster (dev environment)
* `spoke2` -> Context for accesing SPOKE Cluster (pro environment)

If you don't know how to merge your Kubeconfigs and create the context you can refer to [this documentation](https://openshift.tips/oc/#merge-multiple-kubeconfigs)

---

**Continue to [Introduction to GitOps on ACM](./01_introduction_to_gitops.md)**

**Go [Home](./README.md)**