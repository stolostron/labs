# Configuring Infrastructure using Apps

> **Current `Subscription Operator` version (as April 3rd 2020) doesn't support patching existing resources on the cluster, that means that the OAuth config won't be applied. This use-case is documented here for reference only.**

---

As you know GitOps pattern can be used as well for configuring infrastructure related things, in this use case we're going to add a new authentication method to our clusters.

We're adding `HTPasswd` authentication to our clusters, in order to do so we've generated a very secure password, `changeme`, and defined a user `admin` which will be able to login.

We uploaded a `Secret` containing the user credentials to Git (please, don't try this at home, school or anywhere. Keep your plain secrets away from Git), as well as the `OAuth` config required
for configuring this new authentication method.

* [Secret](https://github.com/RHsyseng/acm-app-lifecycle-policies-lab/blob/infra/authentication/htpass-credentials.yaml)
* [OAuth Config](https://github.com/RHsyseng/acm-app-lifecycle-policies-lab/blob/infra/authentication/htpasswd-oauth-config.yaml)

We're going to add this authentication method to our `Development` clusters, there is one RFE opened to allow setting different `namespaces` for the objects created by `Subscription` rather than defaulting to
the namespace where the subscription is created. Since the RFE is still not implemented, we are going to create ACM manifest in the `openshift-config` namespace. You can track the RFE status [here](https://github.com/open-cluster-management/backlog/issues/1295).

1. Create the `PlacementRule` for matching the `development` clusters

    ~~~sh
    oc --context hub create -f https://github.com/RHsyseng/acm-app-lifecycle-policies-lab/raw/master/acm-manifests/infra-gitops/00_placement_rule-dev.yaml
    ~~~
2. Create the `Subscription` for deploying the OAuth config onto `development` clusters

    ~~~sh
    oc --context hub create -f https://github.com/RHsyseng/acm-app-lifecycle-policies-lab/raw/master/acm-manifests/infra-gitops/01_subscription-oauth-htpasswd-config-dev.yaml
    ~~~

---

**Continue to [Introduction to Policies on ACM](./07_introduction_to_policies.md)**

**Back to [Disaster Recovery](./05_disaster_recovery.md)** 

**Go [Home](./README.md)**