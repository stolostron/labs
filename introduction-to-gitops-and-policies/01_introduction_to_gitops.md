# Application Lifecycle

In ACM, applications are composed by different components, we are going to explore them.

**Channels**

Channels define a deployable of multiple types within the hub cluster and point to a physical place where resources are stored for deployment. For this write-up we are going to use Channels of type `GitHub`, there are different channel types for Helm, Namespaces, etc.

[Learn More](https://github.com/open-cluster-management/rhacm-docs/blob/doc_stage/manage_applications/managing_channels.md)

**PlacementRules**

You can create and manage placement rules to define where and how deployables, such as Kubernetes resources and Helm releases, are deployed. Use placement rules to help you facilitate multi-cluster deployments of your deployables.

[Learn More](https://github.com/open-cluster-management/rhacm-docs/blob/doc_stage/manage_applications/managing_placement_rules.md)

**Subscriptions**

Subscriptions serve as sets of definitions for identifying deployables within channels by using annotations, labels, and versions. Subscription resources reside on the managed cluster and can point to a channel or storage location for identifying new and updated deployables, such as Helm releases or Kubernetes resources for deployment. The subscription controller watches the channel for new and updated deployables. When a new or updated deployable is detected, the subscription controller can download the deployables directly from the storage location to target managed clusters without checking the Hub cluster first. With a subscription, the subscription controller can monitor the channel for new or updated packages.

Subscriptions can filter the deployables that are promoted to a channel to select specific deployables. For instance, the subscription can filter the deployables to select a specific deployable version. For this case, the subscription controller checks the version parameter to identify the deployable version to select.

[Learn More](https://github.com/open-cluster-management/rhacm-docs/blob/doc_stage/manage_applications/managing_subscriptions.md)

**Applications**

An Application object provides a way for you to aggregate individual Kubernetes components (e.g. Services, Deployments, StatefulSets, Ingresses, CRDs), and manage them as a group. It provides tooling and UI with a resource that allows for the aggregation and display of all the components in the Application.

[Learn More](https://github.com/open-cluster-management/rhacm-docs/blob/doc_stage/manage_applications/managing_apps.md)

---

**Continue to [Deploying Applications to Multiple Clusters](./02_deploying_apps_to_clusters.md)**

**Back to [Environment Overview](./00_environment_overview.md)** 

**Go [Home](./README.md)**