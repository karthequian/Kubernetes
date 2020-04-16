# Running your first helloworld

## Chapter Goals

1. Starting up minikube
2. Set up your first Kubernetes helloworld application
3. Run your application in a minikube environment
4. Expose application via a nodeport and see it running

### Starting up minikube

First, get minikube up and running with the command `minikube start`. This command sets up a Kubernetes dev environment for you via VirtualBox.

The last statement in the output states that kubectl can talk to minikube. We can verify this by running the command `kubectl get nodes`

This will show you that minikube is ready to use.

### Set up your helloworld

Make sure you have your files unzipped to your local machine (for example /documents/Kubernetes). You should be in your existing directory with the exercise files for chapter 03_04 as shown below.

```
$ pwd
/Users/kgaekwad/Documents/Kubernetes/03_04
$ ls -al
total 16
drwxr-xr-x   4 kgaekwad  staff   128 Apr 14 03:41 .
drwxr-xr-x  22 kgaekwad  staff   704 Apr 14 03:25 ..
-rw-r--r--   1 kgaekwad  staff  3035 Apr 14 03:55 Readme.md
-rw-r--r--@  1 kgaekwad  staff   448 Apr 14 08:01 helloworld.yaml
```

We will run one of the most common Docker helloworld applications out there- [https://hub.docker.com/r/karthequian/helloworld/]

To run this, type:

```
kubectl create -f helloworld.yaml
```

This command creates a deployment resource from the file helloworld.yaml, which, in this case, contains a deployment called "hellworld", pulling from the image karthequian/helloworld, and exposes port 80 of the container to the pod.

Running this command will give you this output, stating that the deployment "hw" was created.

```
$ kubectl create -f helloworld.yaml 
deployment.apps/helloworld created
```

We can run the command `kubectl get all` to see all our resources running, as shown in the output below.

```
$ kubectl get all
NAME                              READY   STATUS    RESTARTS   AGE
pod/helloworld-7cf6df685c-kpnjv   1/1     Running   0          99s

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        53m

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/helloworld   1/1     1            1           99s

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/helloworld-7cf6df685c   1         1         1       99s
$ 

```

### Setting up a load balancer
You'll notice that in the `kubectl get all` command, the service has a port mapping defined; however, when you try to hit that port via your web browser, you won't be able to reach the service.

This is because by default, the pod is only accessible by its internal IP address within the cluster. To make the helloworld container accessible from outside the Kubernetes virtual network, you have to expose the pod as a Kubernetes service.

To do this, we can expose the pod to the public internet using the kubectl expose command 
`kubectl expose deployment helloworld --type=NodePort`

The `--type=NodePort` flag exposes the deployment outside of the cluster. If you're using this on a cloud provider, you can use a `--type=LoadBalancer` that will provision an external IP address would be provisioned to access the service.

To view the final user interface, use the minikube service command.

`minikube service helloworld`

This will open your web browser to your application that is running in Kubernetes!


#### Commands run in this section
```
pwd
ls -al
kubectl get all
kubectl create -f helloworld.yaml
kubectl expose deployment helloworld --type=NodePort
minikube service helloworld
```