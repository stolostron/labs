# Disaster Recovery 

In this use case we are going to define the deployment of our application as follows:

1. We always want a single replica of our application running across a set of clusters
2. If the cluster hosting that single replica goes down, another one should deploy the application

For this use case we're going to create a new `PlacementRule` that matches the clusters labeled as `finance: dev`, we will add this new label to `spoke` and `spoke2` clusters. This new `PlacementRule` will include only one of the clusters
since we defined `clusterReplicas: 1` within the `PlacementRule`.


1. To avoid app creation collisions we are going to delete previous subscriptions and applications

    ~~~sh
    oc --context hub delete -f https://github.com/mvazquezc/acm-testing/raw/master/acm-manifests/reversewords-kustomize/08_subscription-timewindow.yaml
    ~~~
2. Label the clusters

    > **NOTE**: We are using the command line, but labeling can be done using the ACM WebUI as well
    ~~~sh
    # Patch development cluster
    oc --context hub -n spoke patch cluster spoke -p '{"metadata":{"labels":{"finance":"dev"}}}' --type=merge
    # Patch production cluster
    oc --context hub -n spoke2 patch cluster spoke2 -p '{"metadata":{"labels":{"finance":"dev"}}}' --type=merge
    ~~~
3. Create the new `PlacementRule`

    ~~~sh
    oc --context hub create -f https://github.com/mvazquezc/acm-testing/raw/master/acm-manifests/reversewords-kustomize/09_placement_rule-finance.yaml
    ~~~
4. Before creating the `Subscription` let's check which cluster is matching the `PlacementRule` we just created

    ~~~sh
    oc --context hub -n gitops-apps get placementrule finance-dev-clusters -o jsonpath='{.status.decisions[]}'
    
    map[clusterName:spoke clusterNamespace:spoke]
    ~~~
    > **NOTE**: The application will be deployed to `spoke` cluster only based on the output above
5. Create the `Subscription`

    ~~~sh
    oc --context hub create -f https://github.com/mvazquezc/acm-testing/raw/master/acm-manifests/reversewords-kustomize/10_subscription-finance.yaml
    ~~~

Now we should have our application running on the `spoke` cluster and not in `spoke2` cluster:

> **NOTE**: We're using `oc` tool in order to verify the app deployment. Feel free to review the application on the ACM Console as well.

~~~sh
# Review app on development cluster
oc --context spoke -n gitops-apps get pods,svc

NAME                                 READY   STATUS    RESTARTS   AGE
pod/reverse-words-6b764457bc-9d7m4   1/1     Running   0          43s

NAME                    TYPE           CLUSTER-IP      EXTERNAL-IP                                                                  PORT(S)          AGE
service/reverse-words   LoadBalancer   172.30.231.31   a1a9f24191475401a9fbb69d127281ec-1698197835.eu-central-1.elb.amazonaws.com   8080:31684/TCP   41s

# Review app on production cluster
oc --context spoke2 -n gitops-apps get pods,svc

No resources found in gitops-apps namespace.
~~~

Now we are going to simulate that we lose one of the `finance: dev` clusters, in order to do so, we are going to remove the `finance: dev` label from `spoke` cluster, that way the application should be deployed onto `spoke2` cluster.

1. Remove `finance: dev` label from `spoke` cluster

    ~~~sh
    oc --context hub -n spoke patch cluster spoke -p '{"metadata":{"labels":{"finance":null}}}' --type=merge
    ~~~
2. If we look now at the `PlacementRule` matches:

    ~~~sh
    oc --context hub -n gitops-apps get placementrule finance-dev-clusters -o jsonpath='{.status.decisions[]}'
    map[clusterName:spoke2 clusterNamespace:spoke2]
    ~~~

The application should be moved to `spoke2` cluster and removed from `spoke` cluster

> **NOTE**: We're using `oc` tool in order to verify the app deployment. Feel free to review the application on the ACM Console as well.

~~~sh
# Review app on development cluster
oc --context spoke -n gitops-apps get pods,svc

No resources found in gitops-apps namespace.

# Review app on production cluster
oc --context spoke2 -n gitops-apps get pods,svc

NAME                                 READY   STATUS    RESTARTS   AGE
pod/reverse-words-6b764457bc-hdbjf   1/1     Running   0          32s

NAME                    TYPE           CLUSTER-IP      EXTERNAL-IP                                                               PORT(S)          AGE
service/reverse-words   LoadBalancer   172.30.132.90   ae00548c6b1e94922a72b5918b5a7a0b-2055590679.us-west-2.elb.amazonaws.com   8080:31571/TCP   32s
~~~

---

**Continue to [Infrastructure as Code](./05_infrastructure_as_code.md)**

**Back to [Using TimeWindows](./03_using_timewindows.md)** 

**Go [Home](./README.md)**