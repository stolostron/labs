# **Time Windows**

When deploying to some environments, like `production` you might want to decide when those deployments should or should not occur, in order to configure that we're going to use `Time Windows` within our `Subscriptions`.

For this example, we're reutilizing the `Production` subscription, let's try to keep it RealWorld™.

~~~yaml
apiVersion: apps.open-cluster-management.io/v1
kind: Subscription
metadata:
  name: reversewords-prod-app-subscription
  namespace: gitops-apps
  labels:
    app: reversewords-pro-app
  annotations:
    apps.open-cluster-management.io/github-path: apps/reversewords/
    apps.open-cluster-management.io/github-branch: prod
spec:
  channel: gitops-apps/mvazquez-gitops-github
  placement:
    placementRef:
      kind: PlacementRule
      name: production-clusters
  timewindow:
    windowtype: "active"
    location: "Europe/Madrid"
    daysofweek: [ "Friday", "Saturday", "Sunday" ]
    hours:
      - start: "05:00PM"
        end: "10:00PM"
~~~

There are two types of `Time Windows`:

* `Active`  -> The subscription can be applied only on the days and hours defined
* `Blocked` -> The subscription can't be applied on the days and hours defined

Above `Time Window` will deploy our application Fridays, Saturdays or Sundays from `03:00AM` till `06:00AM` (Madrid Timezone)

Hours are optional, a `Time Window` without hours can be defined as well, in that case the `Time Window` will be blocked or active for the whole day.

Back to the example:

1. To avoid app creation collisions we are going to delete previous subscriptions and applications

    ~~~sh
    oc --context hub delete -f https://github.com/mvazquezc/acm-testing/raw/master/acm-manifests/reversewords-kustomize/07_subscription-all-okay.yaml
    ~~~
2. Create the `Application` and the `Subscription` for deploying the production release of our application to our `production` clusters (labeled as `env: pro`)

    ~~~sh
    oc --context hub create -f https://github.com/mvazquezc/acm-testing/raw/master/acm-manifests/reversewords-kustomize/08_subscription-timewindow.yaml
    ~~~

Now we should have our application running on the production cluster:

> **NOTE**: We're using `oc` tool in order to verify the app deployment. Feel free to review the application on the ACM Console as well.

~~~sh
date
Fri Apr  3 15:24:21 UTC 2020

oc --context spoke2 -n gitops-apps get pods,svc
NAME                                 READY   STATUS    RESTARTS   AGE
pod/reverse-words-65d5c5df86-gwm69   1/1     Running   0          45s

NAME                    TYPE           CLUSTER-IP       EXTERNAL-IP                                                              PORT(S)          AGE
service/reverse-words   LoadBalancer   172.30.220.225   a7dc9c827d0b9459e999f8be485f60d1-428946270.us-west-2.elb.amazonaws.com   8080:30442/TCP   47s
~~~

Since we're inside the `Time Window` the deployment was done. If the subscription was changed to `windowtype: blocked` the subscription won't be applied, since we would be inside the blocked `Time Window`.

In this last scenario, the change will be cached and will be applied when the `TimeWindow` exits the `blocked` time.

---

**Continue to [Disaster Recovery](./04_disaster_recovery.md)**

**Back to [Deploying Applications to Multiple Clusters](./02_deploying_apps_to_clusters.md)** 

**Go [Home](./README.md)**