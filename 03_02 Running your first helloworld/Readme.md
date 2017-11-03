# Running your first helloworld

## Chapter Goals

1. Starting up minikube
2. Set up your first Kubernetes helloworld application
3. Run your application in a minikube environment
4. Expose application via a load balancer and see it running

### Starting up minikube

First, get minikube up and running with the command `minikube start`. This command sets up a Kubernetes dev environment for you via VirtualBox.

The last statement in the output states that kubectl can talk to minikube. We can verify this by running the command `kubectl get nodes`

This will show you that minikube is ready to use.

### Set up your helloworld

We will run one of the most common Docker helloworld applications out there- [https://hub.docker.com/r/karthequian/helloworld/]

To run this, type:

```
kubectl run hw --image=karthequian/helloworld --port=80
```

This command runs a deployment called "hw", pulling from the image karthequian/helloworld, and exposes port 80 of the container to the pod.

Running this command will give you this output, stating that the deployment "hw" was created.

```
MacbookHome:~ karthik$ kubectl run hw --image=karthequian/helloworld --port=80
deployment "hw" created
```

We can run the command `kubectl get all` to see all our resources running, as shown in the output below.

```
MacbookHome:~ karthik$ kubectl get all
NAME                     READY     STATUS    RESTARTS   AGE
po/hw-4212494168-zkv1f   1/1       Running   0          24m

NAME             CLUSTER-IP   EXTERNAL-IP   PORT(S)          AGE
svc/hw           10.0.0.14    <pending>     8080:30218/TCP   7d
svc/kubernetes   10.0.0.1     <none>        443/TCP          7d

NAME        DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/hw   1         1         1            1           24m

NAME               DESIRED   CURRENT   READY     AGE
rs/hw-4212494168   1         1         1         24m
MacbookHome:~ karthik$
```

### Setting up a load balancer
You'll notice that in the `kubectl get all` command, the service has a port mapping defined; however, when you try to hit that port via your web browser, you won't be able to reach the service.

This is because by default, the pod is only accessible by its internal IP address within the cluster. To make the helloworld container accessible from outside the Kubernetes virtual network, you have to expose the pod as a Kubernetes service.

To do this, we can expose the pod to the public internet using the kubectl expose command 
`kubectl expose deployment hello-minikube --type=NodePort`

The --type=LoadBalancer flag exposes the deployment outside of the cluster. On cloud providers that support load balancers, an external IP address would be provisioned to access the service.

kubectl expose deployment hello-minikube --type=NodePort

To do this in the minikube environment, the nodeport or loadbalancer type makes the service accessible through the minikube service command.

`minikube service helloworld`

This will open your web browser to your application that is running in Kubernetes!
