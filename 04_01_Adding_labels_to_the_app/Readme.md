# Adding labels to the application

## Chapter Goal
1. Adding labels during build time
2. Viewing labels
2. Adding labels to running pods
3. Deleting labels
4. Searching by labels
5. Extending this concept to deployments/services

### Adding labels during build time

Labels can be added to pods, services and deployments either at build time or at run time. If you're adding labels at build time, you can add this to the yaml file and add a label section in the metadata portion of the yaml as shown below:

```
apiVersion: v1
kind: Pod
metadata:
  name: helloworld
  labels:
    env: production
    author: karthequian
    application_type: ui
    release-version: "1.0"
spec:
  containers:
  - name: helloworld
    image: karthequian/helloworld:latest
```

You can deploy the code above by using the command `kubectl create -f helloworld-pod-with-labels.yml`


### Viewing labels

You have a pod with labels. Super! But how do you see them? You can add the `--show-labels` option to your kubectl get command as shown here: `kubectl get pods --show-labels`.

```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get pods --show-labels
NAME                                     READY     STATUS    RESTARTS   AGE       LABELS
helloworld                               1/1       Running   0          7m        application_type=ui,author=karthequian,env=production,release-version=1.0
helloworld-deployment-2148054017-6fc7f   1/1       Running   0          2d        app=helloworld,pod-template-hash=2148054017
helloworld-deployment-2148054017-88nd8   1/1       Running   0          2d        app=helloworld,pod-template-hash=2148054017
helloworld-deployment-2148054017-dvg47   1/1       Running   0          2d        app=helloworld,pod-template-hash=2148054017
hw-4212494168-s56qj                      1/1       Running   0          2d        pod-template-hash=4212494168,run=hw
MacbookHome:04_01_Adding_labels_to_the_app karthik$
```

### Adding labels to running pods
To add labels to a running pod, you can use the `kubectl label` command as follows: `kubectl label po/helloworld app=helloworld`. This adds the label `app` with the value `helloworld` to the pod.

To update a value of a label, use the `--overwrite` flag to the command as follows: `kubectl label po/helloworld app=helloworldd --overwrite`

### Deleting a label
To remove an existing label, just add a `-` to the end of the label key as follows: `kubectl label po/helloworld app-`. This will remove the app label from the helloworld pod.

```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl label po/helloworld app=helloworld
pod "helloworld" labeled
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get po/helloworld --show-labels
NAME         READY     STATUS    RESTARTS   AGE       LABELS
helloworld   1/1       Running   1          38m       app=helloworld,application_type=ui,author=karthequian,env=production,release-version=1.0
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl label po/helloworld app-
pod "helloworld" labeled
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get po/helloworld --show-labels
NAME         READY     STATUS    RESTARTS   AGE       LABELS
helloworld   1/1       Running   1          38m       application_type=ui,author=karthequian,env=production,release-version=1.0
MacbookHome:04_01_Adding_labels_to_the_app karthik$
```

### Searching by labels
Creating, getting and deleting labels is nice, but the ability to search using labels helps us identify what's going on in our infrastructure better. Let's take a look. First, we're going to deploy a few pods that will constitute what a small org might have.

```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl create -f sample-infrastructure-with-labels.yml
pod "homepage-dev" created
pod "homepage-staging" created
pod "homepage-prod" created
pod "login-dev" created
pod "login-staging" created
pod "login-prod" created
pod "cart-dev" created
pod "cart-staging" created
pod "cart-prod" created
pod "social-dev" created
pod "social-staging" created
pod "social-prod" created
pod "catalog-dev" created
pod "catalog-staging" created
pod "catalog-prod" created
pod "quote-dev" created
pod "quote-staging" created
pod "quote-prod" created
pod "ordering-dev" created
pod "ordering-staging" created
pod "ordering-prod" created
```

Looking at these applications running from a high level makes it hard to see what's going on with the infrastructure.

```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get pods
NAME               READY     STATUS    RESTARTS   AGE
cart-dev           1/1       Running   0          4m
cart-prod          1/1       Running   0          4m
cart-staging       1/1       Running   0          4m
catalog-dev        1/1       Running   0          4m
catalog-prod       1/1       Running   0          4m
catalog-staging    1/1       Running   0          4m
homepage-dev       1/1       Running   0          4m
homepage-prod      1/1       Running   0          4m
homepage-staging   1/1       Running   0          4m
login-dev          1/1       Running   0          4m
login-prod         1/1       Running   0          4m
login-staging      1/1       Running   0          4m
ordering-dev       1/1       Running   0          4m
ordering-prod      1/1       Running   0          4m
ordering-staging   1/1       Running   0          4m
quote-dev          1/1       Running   0          4m
quote-prod         1/1       Running   0          4m
quote-staging      1/1       Running   0          4m
social-dev         1/1       Running   0          4m
social-prod        1/1       Running   0          4m
social-staging     1/1       Running   0          4m

MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get pods --show-labels
NAME               READY     STATUS    RESTARTS   AGE       LABELS
cart-dev           1/1       Running   0          4m        application_type=api,dev-lead=carisa,env=development,release-version=1.0,team=ecommerce
cart-prod          1/1       Running   0          4m        application_type=api,dev-lead=carisa,env=production,release-version=1.0,team=ecommerce
cart-staging       1/1       Running   0          4m        application_type=api,dev-lead=carisa,env=staging,release-version=1.0,team=ecommerce
catalog-dev        1/1       Running   0          4m        application_type=api,dev-lead=daniel,env=development,release-version=4.0,team=ecommerce
catalog-prod       1/1       Running   0          4m        application_type=api,dev-lead=daniel,env=production,release-version=4.0,team=ecommerce
catalog-staging    1/1       Running   0          4m        application_type=api,dev-lead=daniel,env=staging,release-version=4.0,team=ecommerce
homepage-dev       1/1       Running   0          4m        application_type=ui,dev-lead=karthik,env=development,release-version=12.0,team=web
homepage-prod      1/1       Running   0          4m        application_type=ui,dev-lead=karthik,env=production,release-version=12.0,team=web
homepage-staging   1/1       Running   0          4m        application_type=ui,dev-lead=karthik,env=staging,release-version=12.0,team=web
login-dev          1/1       Running   0          4m        application_type=api,dev-lead=jim,env=development,release-version=1.0,team=auth
login-prod         1/1       Running   0          4m        application_type=api,dev-lead=jim,env=production,release-version=1.0,team=auth
login-staging      1/1       Running   0          4m        application_type=api,dev-lead=jim,env=staging,release-version=1.0,team=auth
ordering-dev       1/1       Running   0          4m        application_type=backend,dev-lead=chen,env=development,release-version=2.0,team=purchasing
ordering-prod      1/1       Running   0          4m        application_type=backend,dev-lead=chen,env=production,release-version=2.0,team=purchasing
ordering-staging   1/1       Running   0          4m        application_type=backend,dev-lead=chen,env=staging,release-version=2.0,team=purchasing
quote-dev          1/1       Running   0          4m        application_type=api,dev-lead=amy,env=development,release-version=2.0,team=ecommerce
quote-prod         1/1       Running   0          4m        application_type=api,dev-lead=amy,env=production,release-version=1.0,team=ecommerce
quote-staging      1/1       Running   0          4m        application_type=api,dev-lead=amy,env=staging,release-version=2.0,team=ecommerce
social-dev         1/1       Running   0          4m        application_type=api,dev-lead=carisa,env=development,release-version=2.0,team=marketing
social-prod        1/1       Running   0          4m        application_type=api,dev-lead=marketing,env=production,release-version=1.0,team=marketing
social-staging     1/1       Running   0          4m        application_type=api,dev-lead=marketing,env=staging,release-version=1.0,team=marketing
```

You can search for labels with the flag `--selector` (or `-l`). If you want to search for all the pods that are running in production, you can run `kubectl get pods --selector env=production` as shown below:

```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get pods --selector env=production
NAME            READY     STATUS    RESTARTS   AGE
cart-prod       1/1       Running   0          9m
catalog-prod    1/1       Running   0          9m
homepage-prod   1/1       Running   0          9m
login-prod      1/1       Running   0          9m
ordering-prod   1/1       Running   0          9m
quote-prod      1/1       Running   0          9m
social-prod     1/1       Running   0          9m
```

Similarly, to get all pods by dev lead karthik, you'd add `dev-lead=karthik` to the selector as shown below.

```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get pods --selector dev-lead=karthik
NAME               READY     STATUS    RESTARTS   AGE
homepage-dev       1/1       Running   0          12m
homepage-prod      1/1       Running   0          12m
homepage-staging   1/1       Running   0          12m
```

You can also do more complicated searches, like finding any pods owned by karthik in the development tier by the following query `dev-lead=karthik,env=staging`,

```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get pods -l dev-lead=karthik,env=staging
NAME               READY     STATUS    RESTARTS   AGE
homepage-staging   1/1       Running   0          20m
```

Or, any apps not owned by Karthik in staging (using the ! construct):

```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get pods -l dev-lead!=karthik,env=staging
NAME               READY     STATUS    RESTARTS   AGE
cart-staging       1/1       Running   2          27m
catalog-staging    1/1       Running   2          27m
login-staging      1/1       Running   2          27m
ordering-staging   1/1       Running   2          27m
quote-staging      1/1       Running   2          27m
social-staging     1/1       Running   2          27m
```

Querying also supports the `in` keyword
```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get pods -l 'release-version in (1.0,2.0)'
NAME               READY     STATUS    RESTARTS   AGE
cart-dev           1/1       Running   2          23m
cart-prod          1/1       Running   2          23m
cart-staging       1/1       Running   2          23m
login-dev          1/1       Running   2          23m
login-prod         1/1       Running   2          23m
login-staging      1/1       Running   2          23m
ordering-dev       1/1       Running   2          23m
ordering-prod      1/1       Running   2          23m
ordering-staging   1/1       Running   2          23m
quote-dev          1/1       Running   2          23m
quote-prod         1/1       Running   2          23m
quote-staging      1/1       Running   2          23m
social-dev         1/1       Running   2          23m
social-prod        1/1       Running   2          23m
social-staging     1/1       Running   2          23m
```

Or a more complicated example:
```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get pods -l "release-version in (1.0,2.0),team in (marketing,ecommerce)"
NAME             READY     STATUS    RESTARTS   AGE
cart-dev         1/1       Running   3          37m
cart-prod        1/1       Running   3          37m
cart-staging     1/1       Running   3          37m
quote-dev        1/1       Running   3          37m
quote-prod       1/1       Running   3          37m
quote-staging    1/1       Running   3          37m
social-dev       1/1       Running   3          37m
social-prod      1/1       Running   3          37m
social-staging   1/1       Running   3          37m
```

The oppositre of "in" is "notin", surprise, that is supported as well, shown in this example:

```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get pods -l 'release-version notin (1.0,2.0)'
NAME               READY     STATUS    RESTARTS   AGE
catalog-dev        1/1       Running   4          44m
catalog-prod       1/1       Running   4          44m
catalog-staging    1/1       Running   4          44m
homepage-dev       1/1       Running   0          44m
homepage-prod      1/1       Running   0          44m
homepage-staging   1/1       Running   0          44m
```

Finally, sometimes your label might not have a value assigned to it, but you can still search for this by passing the label name

```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get pods -l 'release-version'
NAME               READY     STATUS    RESTARTS   AGE
cart-dev           1/1       Running   4          46m
cart-prod          1/1       Running   4          46m
cart-staging       1/1       Running   4          46m
catalog-dev        1/1       Running   4          46m
catalog-prod       1/1       Running   4          46m
catalog-staging    1/1       Running   4          46m
homepage-dev       1/1       Running   0          46m
homepage-prod      1/1       Running   0          46m
homepage-staging   1/1       Running   0          46m
login-dev          1/1       Running   4          46m
login-prod         1/1       Running   4          46m
login-staging      1/1       Running   4          46m
ordering-dev       1/1       Running   4          46m
ordering-prod      1/1       Running   4          46m
ordering-staging   1/1       Running   4          46m
quote-dev          1/1       Running   4          46m
quote-prod         1/1       Running   4          46m
quote-staging      1/1       Running   4          46m
social-dev         1/1       Running   4          46m
social-prod        1/1       Running   4          46m
social-staging     1/1       Running   4          46m
```

### Extending this concept

As a bonus, this will also work with `kubectl get services/deployments/all --show-labels`, and will return labels for your services, deployments or all objects.

```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl get all --show-labels
NAME                                        READY     STATUS    RESTARTS   AGE       LABELS
po/helloworld                               1/1       Running   0          8m        application_type=ui,author=karthequian,env=production,release-version=1.0
po/helloworld-deployment-2148054017-6fc7f   1/1       Running   0          2d        app=helloworld,pod-template-hash=2148054017
po/helloworld-deployment-2148054017-88nd8   1/1       Running   0          2d        app=helloworld,pod-template-hash=2148054017
po/helloworld-deployment-2148054017-dvg47   1/1       Running   0          2d        app=helloworld,pod-template-hash=2148054017
po/hw-4212494168-s56qj                      1/1       Running   0          2d        pod-template-hash=4212494168,run=hw

NAME                     CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE       LABELS
svc/helloworld-service   10.0.0.244   <pending>     80:32138/TCP   2d        <none>
svc/kubernetes           10.0.0.1     <none>        443/TCP        11d       component=apiserver,provider=kubernetes

NAME                           DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE       LABELS
deploy/helloworld-deployment   3         3         3            3           2d        app=helloworld
deploy/hw                      1         1         1            1           2d        run=hw

NAME                                  DESIRED   CURRENT   READY     AGE       LABELS
rs/helloworld-deployment-2148054017   3         3         3         2d        app=helloworld,pod-template-hash=2148054017
rs/hw-4212494168                      1         1         1         2d        pod-template-hash=4212494168,run=hw
MacbookHome:04_01_Adding_labels_to_the_app karthik$
```

And, you can delete pods, services or deployments by label as well! For example ` kubectl delete pods -l dev-lead=karthik` will delete all pods who's dev-lead was Karthik.

```
MacbookHome:04_01_Adding_labels_to_the_app karthik$ kubectl delete pods -l dev-lead=karthik
pod "homepage-dev" deleted
pod "homepage-prod" deleted
pod "homepage-staging" deleted
```

To summarize, labels in kubernetes is a powerful concept! Use the labeling feature to your advantage to build your infrastructure in an organized fashion.