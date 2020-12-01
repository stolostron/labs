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
    apps.open-cluster-management.io/git-path: apps/reversewords/
    apps.open-cluster-management.io/git-branch: prod
spec:
  channel: gitops-apps/acm-gitops-github
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

Above `Time Window` will deploy our application Fridays, Saturdays or Sundays from `03:00AM` till `05:00AM` (Madrid Timezone)

Hours are optional, a `Time Window` without hours can be defined as well, in that case the `Time Window` will be blocked or active for the whole day.

Keep in mind that that if you have a `Subscription` with an Active/Blocked `TimeWindow`, once the Subscription is deployed or updated the application won't be removed until the next `TimeWindow`. You can think of `TimeWindows` like a maintenance window, a period of time where you can/can't deploy/update your applications.

Back to the example:

1. To avoid app creation collisions we are going to delete previous subscriptions and applications

    ~~~sh
    oc --context hub delete -f https://github.com/RHsyseng/acm-app-lifecycle-policies-lab/raw/master/acm-manifests/reversewords-kustomize/07_subscription-all-okay.yaml
    ~~~

> ![WARNING](assets/warning-icon.png) **NOTE:** The TimeWindow below has `Friday`, `Saturday` and `Sunday` configured as active days, make sure to modify it (change the active days) as needed so you can see the effect.

2. Create the `Application` and the `Subscription` for deploying the production release of our application to our `production` clusters (labeled as `env: pro`)

    ~~~sh
    oc --context hub create -f https://github.com/RHsyseng/acm-app-lifecycle-policies-lab/raw/master/acm-manifests/reversewords-kustomize/08_subscription-timewindow.yaml
    ~~~

3. As mentioned before, edit the subscription so the TimeWindow matches your current schedule if needed.

    ~~~sh
    oc --context hub -n gitops-apps edit subscriptions.apps.open-cluster-management.io reversewords-prod-app-subscription
    ~~~

Now we should have our application running on the production cluster:

> ![TIP](assets/tip-icon.png) **NOTE:** We're using `oc` tool in order to verify the app deployment. Feel free to review the application on the ACM Console as well.

~~~sh
date
oc --context managed-prod -n gitops-apps get pods,svc,route
~~~

~~~sh
Fri Apr  3 15:24:21 UTC 2020

NAME                                READY   STATUS    RESTARTS   AGE
pod/reverse-words-7dd94446c-v55dj   1/1     Running   0          2m3s

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/reverse-words   ClusterIP   172.30.23.99   <none>        8080/TCP   2m4s

NAME                                     HOST/PORT                                                         PATH   SERVICES        PORT   TERMINATION   WILDCARD
route.route.openshift.io/reverse-words   reverse-words-gitops-apps.apps.cluster-8aca.red.osp.opentlc.com          reverse-words   8080                 None
~~~

Since we're inside the `Time Window` the deployment was done. If the subscription was changed to `windowtype: blocked` the subscription won't be applied, since we would be inside the blocked `Time Window`.

In this last scenario, the change will be cached and will be applied when the `TimeWindow` exits the `blocked` time.

---

**Continue to [Disaster Recovery](./05_disaster_recovery.md)**

**Back to [Deploying Applications to Multiple Clusters](./03_deploying_apps_to_clusters.md)** 

**Go [Home](./README.md)**
