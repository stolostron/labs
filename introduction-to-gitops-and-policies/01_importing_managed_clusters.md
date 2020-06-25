# OC Tool Context Configuration

> ![WARNING](assets/warning-icon.png) **NOTE:** You must complete this section before importing the clusters on ACM

The following steps will leverage the usage of `contexts` within the `oc` tool.

We will be using three contexts:

* `hub`    -> Context for accessing HUB Cluster 
* `spoke`  -> Context for accessing Development Managed Cluster
* `spoke2` -> Context for accessing Production Managed Cluster

## RHPDS Users

You should have three emails with the access details for the different environments, the `Hub` cluster and the two `Managed` clusters. Now, you need to create a new Kubeconfig and merge all credentials. You can follow the example below using the information received by RHPDS.

### Credentials Example

You can run these steps from your laptop if you have the `oc tool` installed, or from the `bastion` host provided by RHPDS.

![TIP](assets/tip-icon.png) **NOTE:** Remember to use the credentials provided by the RHPDS platform

![WARNING](assets/warning-icon.png) **NOTE:** Pay special attention when creating the HUB cluster context, it needs to match the cluster where ACM is running, managed clusters order is not important

1. Create the new Kubeconfig file and export it

    ~~~sh
    touch /var/tmp/acm-lab-kubeconfig
    export KUBECONFIG=/var/tmp/acm-lab-kubeconfig
    ~~~
2. Login into the HUB cluster and rename the context

    ~~~sh
    oc login --insecure-skip-tls-verify=true --username=<admin_user> --password=<admin_password> https://api.<hub_cluster_name>.<base_domain>:6443
    oc config rename-context $(oc config current-context) hub
    sed -i "s/admin$/admin-hub/" /var/tmp/acm-lab-kubeconfig
    ~~~
3. Login into one of the managed clusters (this will be your first spoke cluster)

    ~~~sh
    oc login --insecure-skip-tls-verify=true --username=<admin_user> --password=<admin_password> https://api.<managed1_cluster_name>.<base_domain>:6443
    oc config rename-context $(oc config current-context) spoke
    sed -i "s/admin$/admin-spoke/" /var/tmp/acm-lab-kubeconfig
    ~~~
4. Login into the other managed cluster (this will be your second spoke cluster)

    ~~~sh
    oc login --insecure-skip-tls-verify=true --username=<admin_user> --password=<admin_password> https://api.<managed1_cluster_name>.<base_domain>:6443
    oc config rename-context $(oc config current-context) spoke2
    sed -i "s/admin$/admin-spoke2/" /var/tmp/acm-lab-kubeconfig
    ~~~

## Bring Your Own Environment Users

If you have been provided with Kubeconfig files for accessing the clusters you need to merge those into a single one. If you don't know how to merge your Kubeconfigs and create the contexts you can refer to [this documentation](https://openshift.tips/oc/#merge-multiple-kubeconfigs).

If you have been provided with user/password credentials for accessing the clusters, you can create the different contexts following the example above with the credentials you got.

# Importing Managed Clusters into ACM

## Hub and Managed Clusters

During the lab we will refer to Hub and Managed Clusters. **Hub** cluster is where ACM is running, **Managed** clusters are being managed by a **Hub** cluster.

## Importing Clusters into ACM

At this point, you should have 1 x HUB Cluster with ACM running and 2 x Managed Clusters. We are going to login into the ACM Console and import the clusters.

1. Login into the ACM WebUI with the admin credentials for the HUB Cluster, the url should be something like this: `https://multicloud-console.apps.<cluster_name>.<base_domain>`
2. On the left menu click on `Automate infrastructure` -> `Clusters`
3. Click on `Add cluster` -> `Import and existing cluster`
4. Import the first managed cluster with the following details
    1. `Cluster name`: spoke
    2. `Namespace`: spoke
    3. Click on `Edit cluster import YAML file`
    4. Modify the `clusterLabels` section to look like this:

        ~~~yaml
        clusterLabels:
          cloud: "AWS"
          vendor: "Amazon"
          env: "dev"
          region: "EU"
        ~~~
    5. Leave the rest of the yaml as it is
    6. Click on `Generate command`
    7. Click on the `Copy command icon`

        ![Copy command](./assets/copy_cluster_command.png)
    8. Change the `oc tool` context to `spoke` and run the command
        1. Change to the `spoke` context on your oc tool

            ~~~sh
            oc config use spoke
            ~~~
        2. Paste the command you just copied into your terminal
    9. The cluster will be imported in a few minutes
5. Import the second managed cluster with the following details
    1. `Cluster name`: spoke2
    2. `Namespace`: spoke2
    3. Click on `Edit cluster import YAML file`
    4. Modify the `clusterLabels` section to look like this:

        ~~~yaml
        clusterLabels:
          cloud: "AWS"
          vendor: "Amazon"
          env: "pro"
          region: "US"
        ~~~
    5. Leave the rest of the yaml as it is
    6. Click on `Generate command`
    7. Click on the `Copy command icon`

        ![Copy command](./assets/copy_cluster_command.png)
    8. Change the `oc tool` context to `spoke2` and run the command
        1. Change to the `spoke2` context on your oc tool

            ~~~sh
            oc config use spoke2
            ~~~
        2. Paste the command you just copied into your terminal
    9. The cluster will be imported in a few minutes
6. After the two clusters are imported you should see them on the ACM WebUI under `Automate infrastructure` -> `Clusters`:

    ![Clusters Imported](./assets/clusters_imported.png)

---

**Continue to [Introduction to GitOps on ACM](./02_introduction_to_gitops.md)**

**Back to [Environment Overview](./00_environment_overview.md)**

**Go [Home](./README.md)**
