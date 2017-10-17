# Scaling the helloworld app

## Chapter Goal
1. Learn how to scale the helloworld application

### Learn how to scale the helloworld application

When we run the `kubectl get all` command, we notice that we have a single instance of the helloworld deployment running, as well as a single pod associated with the deployment.

There are many scenarios where this is undesired. If for some reason the pod crashes, or ends up in a crash loop we will not have any instances of the application running which will cause downtime until the pod can restart successfully.

Fortunately, we can use the inbuilt property of kubernetes called replica sets for deployments to solve this issue. To run a replica set for our helloworld deployment, run the command `kubectl scale --replicas=3 deploy/helloworld-deployment`. This will add 3 replicas for our deployment, which effectively means 3 pods running for a single deployment.

```
MacbookHome:03_03 Breaking down the helloworld app karthik$ kubectl scale --replicas=3 deploy/helloworld-deployment
deployment "helloworld-deployment" scaled
```

Now, if we run a `kubectl get all`, we'll see 3 pods instead of 1.

```
MacbookHome:03_03 Breaking down the helloworld app karthik$ kubectl get all
NAME                                        READY     STATUS    RESTARTS   AGE
po/helloworld-deployment-2148054017-6fc7f   1/1       Running   0          6h
po/helloworld-deployment-2148054017-88nd8   1/1       Running   0          53m
po/helloworld-deployment-2148054017-dvg47   1/1       Running   0          53m

NAME                     CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
svc/helloworld-service   10.0.0.244   <pending>     80:32138/TCP   6h
svc/kubernetes           10.0.0.1     <none>        443/TCP        9d

NAME                           DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/helloworld-deployment   3         3         3            3           6h

NAME                                  DESIRED   CURRENT   READY     AGE
rs/helloworld-deployment-2148054017   3         3         3         6h
```