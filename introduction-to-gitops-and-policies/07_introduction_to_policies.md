# Policies

In ACM, policies are composed by different components, we are going to explore these components.

**Policy**

Defines the different "checks" clusters will be validated against. `Policies` have `Specifications`. A Specification is a set of rules that will be validated against the clusters.

[Learn More](https://access.redhat.com/documentation/en-us/red_hat_advanced_cluster_management_for_kubernetes/2.0/html/security/security#governance-and-risk)

**PlacementBinding**

Similar to `RoleBindings`, it ensures which `Policies` are assigned to which `Clusters`. In order to do so `PlacementRules` are put into the mix.

---

**Continue to [Applying Policies](./08_applying_policies.md)**

**Back to [Infrastructure as Code](./06_infrastructure_as_code.md)** 

**Go [Home](./README.md)**