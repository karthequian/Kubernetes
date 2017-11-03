# Adding labels to the application

## Chapter Goal
1. Adding labels during build time
2. Viewing labels
2. Adding labels to running pods
3. Deleting a label
4. Searching by labels
5. Extending the label concept to deployments/services

### Adding labels during build time

You can add labels to pods, services and deployments either at build time or at run time. If you're adding labels at build time, you can add a label section in the metadata portion of the YAML as shown below:

```
```

You can deploy the code above by using the command `kubectl create -f helloworld-pod-with-labels.yml`


### Viewing labels

You have a pod with labels. Super! But how do you see them? You can add the `--show-labels` option to your kubectl get command as shown here: `kubectl get pods --show-labels`.

```
```

### Adding labels to running pods
To add labels to a running pod, you can use the `kubectl label` command as follows: `kubectl label po/helloworld app=helloworld`. This adds the label `app` with the value `helloworld` to the pod.

To update the value of a label, use the `--overwrite` flag in the command as follows: `kubectl label po/helloworld app=helloworldapp --overwrite` 

### Deleting a label
To remove an existing label, just add a `-` to the end of the label key as follows: `kubectl label po/helloworld app-`. This will remove the app label from the helloworld pod.

```
```

### Searching by labels
Creating, getting and deleting labels is nice, but the ability to search using labels helps us identify what's going on in our infrastructure better. Let's take a look. First, we're going to deploy a few pods that will constitute what a small org might have. 

`kubectl create -f kubectl create -f sample-infrastructure-with-labels.yml`

```
```

Looking at these applications running from a high level makes it hard to see what's going on with the infrastructure.

`kubectl get pods`

`kubectl get pods --show-labels`

```
```

You can search for labels with the flag `--selector` (or `-l`). If you want to search for all the pods that are running in production, you can run `kubectl get pods --selector env=production` as shown below:

`kubectl get pods --selector env=production`


```
```

Similarly, to get all pods by dev lead Karthik, you'd add `dev-lead=karthik` to the selector as shown below.

`kubectl get pods --selector dev-lead=karthik`

```
```

You can also do more complicated searches, like finding any pods owned by Karthik in the development tier, by the following query `dev-lead=karthik,env=staging`:

`kubectl get pods -l dev-lead=karthik,env=staging`

```
```

Or, any apps not owned by Karthik in staging (using the ! construct):

`kubectl get pods -l dev-lead!=karthik,env=staging`

```
```

Querying also supports the `in` keyword

`kubectl get pods -l 'release-version in (1.0,2.0)'`

```
```

Or a more complicated example:

`kubectl get pods -l "release-version in (1.0,2.0),team in (marketing,ecommerce)"`

```
```

The opposite of "in" is "notin". Surprise. "Notin" is supported as well, as shown in this example:

`kubectl get pods -l 'release-version notin (1.0,2.0)'`

```
```

Finally, sometimes your label might not have a value assigned to it, but you can still search by label name.

`kubectl get pods -l 'release-version'`  

```
```

### Extending the label concept to deployments/services

As a bonus, labels will also work with `kubectl get services/deployments/all --show-labels` and will return labels for your services, deployments or all objects.

```
```

And, you can delete pods, services or deployments by label as well! For example `kubectl delete pods -l dev-lead=karthik` will delete all pods who's dev-lead was Karthik. 

```
```

To summarize, labels in Kubernetes is a powerful concept! Use the labeling feature to your advantage to build your infrastructure in an organized fashion.
