# Application health checks

## Chapter Goal
1. Add HTTP health checks to the helloworld deployment
2. Simulate a failing deployment that fails a readiness probe
3. Simulate a failing deployment that fails a liveness probe

### Add HTTP health check to the helloworld deployment

We will take our existing hellworld deployment, and add a readiness and liveness probe healthchecks.

```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment
spec:
  selector:
    matchLabels:
      app: helloworld
  replicas: 1 # tells deployment to run 1 pods matching the template
  template: # create pods using pod definition in this template
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: helloworld
        image: karthequian/helloworld:latest
        ports:
        - containerPort: 80
```

A readiness probe is used to know when a container is ready to start accepting traffic.

The yaml has the following parameters:
```
readinessProbe:
  # length of time to wait for a pod to initialize
  # after pod startup, before applying health checking
  initialDelaySeconds: 10
  # Amount of time to wait before timing out
  initialDelaySeconds: 1
  # Probe for http
  httpGet:
    # Path to probe
    path: /
    # Port to probe
    port: 80
```

A liveness probe is used to know when a container might need to be restarted.

A liveness probe yaml has the following parameters that need to be filled out:

```
livenessProbe:
  # length of time to wait for a pod to initialize
  # after pod startup, before applying health checking
  initialDelaySeconds: 10
  # Amount of time to wait before timing out
  timeoutSeconds: 1
  # Probe for http
  httpGet:
    # Path to probe
    path: /
    # Port to probe
    port: 80
```

Thus, our deployment yaml now becomes:

```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment-with-probe
spec:
  selector:
    matchLabels:
      app: helloworld
  replicas: 1 # tells deployment to run 1 pods matching the template
  template: # create pods using pod definition in this template
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: helloworld
        image: karthequian/helloworld:latest
        ports:
        - containerPort: 80
        readinessProbe:
          # length of time to wait for a pod to initialize
          # after pod startup, before applying health checking
          initialDelaySeconds: 10
          # Amount of time to wait before timing out
          initialDelaySeconds: 1
          # Probe for http
          httpGet:
            # Path to probe
            path: /
            # Port to probe
            port: 80
        livenessProbe:
          # length of time to wait for a pod to initialize
          # after pod startup, before applying health checking
          initialDelaySeconds: 10
          # Amount of time to wait before timing out
          timeoutSeconds: 1
          # Probe for http
          httpGet:
            # Path to probe
            path: /
            # Port to probe
            port: 80

```

We run this yaml the same as we had done before: `kubectl create -f helloworld-deployment-with-probes`


### Simulate a failing deployment that fails a readiness probe
We will now try to simulate a bad helloworld pod that fails a readiness probe. Instead of checking port 80 like the last example, we will run a readiness check on port 90 to simulate a failing scenario. Thus, our yaml now is:

```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment-with-bad-readiness-probe
spec:
  selector:
    matchLabels:
      app: helloworld
  replicas: 1 # tells deployment to run 1 pods matching the template
  template: # create pods using pod definition in this template
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: helloworld
        image: karthequian/helloworld:latest
        ports:
        - containerPort: 80
        readinessProbe:
          # length of time to wait for a pod to initialize
          # after pod startup, before applying health checking
          initialDelaySeconds: 10
          # Amount of time to wait before timing out
          initialDelaySeconds: 1
          # Probe for http
          httpGet:
            # Path to probe
            path: /
            # Port to probe
            port: 90
```

We will run this yaml with the command `kubectl create -f helloworld-with-bad-readiness-probe.yaml`. After about a minute, we will notice that our pod is still not in a read state when we run the `kubectl get pods` command.

```
MacbookHome:04_02 Application health checks karthik$ kubectl get pods
NAME                                                              READY     STATUS    RESTARTS   AGE
helloworld-deployment-with-bad-readiness-probe-8664db7448-pv4fm   0/1       Running   0          1m
```

Describing the pod will show that the pod failed readiness:
```
MacbookHome:04_02 Application health checks karthik$ kubectl describe po helloworld-deployment-with-bad-readiness-probe-8664db7448-pv4fm
Name:   helloworld-deployment-with-bad-readiness-probe-8664db7448-pv4fm
Namespace:  default
Node:   minikube/192.168.99.101
Start Time: Sun, 29 Oct 2017 01:35:08 -0500
Labels:   app=helloworld
    pod-template-hash=4220863004
Annotations:  kubernetes.io/created-by={"kind":"SerializedReference","apiVersion":"v1","reference":{"kind":"ReplicaSet","namespace":"default","name":"helloworld-deployment-with-bad-readiness-probe-8664db7448","uid"...
Status:   Running
IP:   172.17.0.4
Controllers:  ReplicaSet/helloworld-deployment-with-bad-readiness-probe-8664db7448
Containers:
  helloworld:
    Container ID: docker://61fa8b27fe2e8239d5dc84fc097af17c9d4decc90b3bea09103cc9d73302f235
    Image:    karthequian/helloworld:latest
    Image ID:   docker-pullable://karthequian/helloworld@sha256:165f87263f1775f0bf91022b48a51265357ba9f36bc3882f5ecdefc7f8ef8f6d
    Port:   80/TCP
    State:    Running
      Started:    Sun, 29 Oct 2017 01:35:10 -0500
    Ready:    False
    Restart Count:  0
    Readiness:    http-get http://:90/ delay=1s timeout=1s period=10s #success=1 #failure=3
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-x8qf6 (ro)
Conditions:
  Type    Status
  Initialized   True
  Ready   False
  PodScheduled  True
Volumes:
  default-token-x8qf6:
    Type: Secret (a volume populated by a Secret)
    SecretName: default-token-x8qf6
    Optional: false
QoS Class:  BestEffort
Node-Selectors: <none>
Tolerations:  <none>
Events:
  FirstSeen LastSeen  Count From      SubObjectPath     Type    Reason      Message
  --------- --------  ----- ----      -------------     --------  ------      -------
  5m    5m    1 default-scheduler         Normal    Scheduled   Successfully assigned helloworld-deployment-with-bad-readiness-probe-8664db7448-pv4fm to minikube
  5m    5m    1 kubelet, minikube         Normal    SuccessfulMountVolume MountVolume.SetUp succeeded for volume "default-token-x8qf6"
  5m    5m    1 kubelet, minikube spec.containers{helloworld} Normal    Pulling     pulling image "karthequian/helloworld:latest"
  5m    5m    1 kubelet, minikube spec.containers{helloworld} Normal    Pulled      Successfully pulled image "karthequian/helloworld:latest"
  5m    5m    1 kubelet, minikube spec.containers{helloworld} Normal    Created     Created container
  5m    5m    1 kubelet, minikube spec.containers{helloworld} Normal    Started     Started container
  4m    1m    20  kubelet, minikube spec.containers{helloworld} Warning   Unhealthy   Readiness probe failed: Get http://172.17.0.4:90/: dial tcp 172.17.0.4:90: getsockopt: connection refused
MacbookHome:04_02 Application health checks karthik$
```

### Simulate a failing deployment that fails a liveness probe

Next, we will simulate a bad helloworld pod that fails a liveness probe. Instead of checking port 80 like the last example, we will run a liveness check on port 90 to simulate a failing scenario. Thus, our yaml now is:

```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment-with-bad-liveness-probe
spec:
  selector:
    matchLabels:
      app: helloworld
  replicas: 1 # tells deployment to run 1 pods matching the template
  template: # create pods using pod definition in this template
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: helloworld
        image: karthequian/helloworld:latest
        ports:
        - containerPort: 80
        livenessProbe:
          # length of time to wait for a pod to initialize
          # after pod startup, before applying health checking
          initialDelaySeconds: 10
          # How often (in seconds) to perform the probe.
          periodSeconds: 5
          # Amount of time to wait before timing out
          timeoutSeconds: 1
          # Kubernetes will try failureThreshold times before giving up and restarting the Pod
          failureThreshold: 2
          # Probe for http
          httpGet:
            # Path to probe
            path: /
            # Port to probe
            port: 90
```


We will run this yaml with the command `kubectl create -f helloworld-with-bad-liveness-probe.yaml`. After about a minute, we will notice that our pod is still not in a read state when we run the `kubectl get pods` command.

```
MacbookHome:04_02 Application health checks karthik$ kubectl create -f helloworld-with-bad-liveness-probe.yaml
deployment "helloworld-deployment-with-bad-liveness-probe" created
MacbookHome:04_02 Application health checks karthik$ kubectl get deployments
NAME                                            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment-with-bad-liveness-probe   1         1         1            1           5s
MacbookHome:04_02 Application health checks karthik$ kubectl get pods
NAME                                                              READY     STATUS        RESTARTS   AGE
helloworld-deployment-with-bad-liveness-probe-77759fbbbf-kljs4    1/1       Running       0          9s
MacbookHome:04_02 Application health checks karthik$ kubectl get pods
NAME                                                              READY     STATUS        RESTARTS   AGE
helloworld-deployment-with-bad-liveness-probe-77759fbbbf-kljs4    1/1       Running       1          26s
MacbookHome:04_02 Application health checks karthik$ kubectl get pods
NAME                                                             READY     STATUS    RESTARTS   AGE
helloworld-deployment-with-bad-liveness-probe-77759fbbbf-kljs4   1/1       Running   1          32s
MacbookHome:04_02 Application health checks karthik$ kubectl get pods
NAME                                                             READY     STATUS    RESTARTS   AGE
helloworld-deployment-with-bad-liveness-probe-77759fbbbf-kljs4   1/1       Running   2          38s
MacbookHome:04_02 Application health checks karthik$ kubectl get pods
NAME                                                             READY     STATUS    RESTARTS   AGE
helloworld-deployment-with-bad-liveness-probe-77759fbbbf-kljs4   1/1       Running   3          50s
MacbookHome:04_02 Application health checks karthik$ kubectl get pods
NAME                                                             READY     STATUS             RESTARTS   AGE
helloworld-deployment-with-bad-liveness-probe-77759fbbbf-kljs4   0/1       CrashLoopBackOff   3          1m
```

When we describe our pod, we notice that it is failing the liveness probe:

```
MacbookHome:04_02 Application health checks karthik$ kubectl describe po helloworld-deployment-with-bad-liveness-probe-77759fbbbf-kljs4
Name:   helloworld-deployment-with-bad-liveness-probe-77759fbbbf-kljs4
Namespace:  default
Node:   minikube/192.168.99.101
Start Time: Sun, 29 Oct 2017 01:47:59 -0500
Labels:   app=helloworld
    pod-template-hash=3331596669
Annotations:  kubernetes.io/created-by={"kind":"SerializedReference","apiVersion":"v1","reference":{"kind":"ReplicaSet","namespace":"default","name":"helloworld-deployment-with-bad-liveness-probe-77759fbbbf","uid":...
Status:   Running
IP:   172.17.0.4
Controllers:  ReplicaSet/helloworld-deployment-with-bad-liveness-probe-77759fbbbf
Containers:
  helloworld:
    Container ID: docker://41cfe81a3327b03831440e861eb2164b8a9f601ce630f024c76412d903781922
    Image:    karthequian/helloworld:latest
    Image ID:   docker-pullable://karthequian/helloworld@sha256:165f87263f1775f0bf91022b48a51265357ba9f36bc3882f5ecdefc7f8ef8f6d
    Port:   80/TCP
    State:    Running
      Started:    Sun, 29 Oct 2017 01:48:47 -0500
    Last State:   Terminated
      Reason:   Completed
      Exit Code:  0
      Started:    Sun, 29 Oct 2017 01:48:31 -0500
      Finished:   Sun, 29 Oct 2017 01:48:46 -0500
    Ready:    True
    Restart Count:  3
    Liveness:   http-get http://:90/ delay=10s timeout=1s period=5s #success=1 #failure=2
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-x8qf6 (ro)
Conditions:
  Type    Status
  Initialized   True
  Ready   True
  PodScheduled  True
Volumes:
  default-token-x8qf6:
    Type: Secret (a volume populated by a Secret)
    SecretName: default-token-x8qf6
    Optional: false
QoS Class:  BestEffort
Node-Selectors: <none>
Tolerations:  <none>
Events:
  FirstSeen LastSeen  Count From      SubObjectPath     Type    Reason      Message
  --------- --------  ----- ----      -------------     --------  ------      -------
  58s   58s   1 default-scheduler         Normal    Scheduled   Successfully assigned helloworld-deployment-with-bad-liveness-probe-77759fbbbf-kljs4 to minikube
  58s   58s   1 kubelet, minikube         Normal    SuccessfulMountVolume MountVolume.SetUp succeeded for volume "default-token-x8qf6"
  47s   12s   4 kubelet, minikube spec.containers{helloworld} Warning   Unhealthy   Liveness probe failed: Get http://172.17.0.4:90/: dial tcp 172.17.0.4:90: getsockopt: connection refused
  58s   11s   4 kubelet, minikube spec.containers{helloworld} Normal    Pulling     pulling image "karthequian/helloworld:latest"
  57s   11s   4 kubelet, minikube spec.containers{helloworld} Normal    Pulled      Successfully pulled image "karthequian/helloworld:latest"
  57s   11s   4 kubelet, minikube spec.containers{helloworld} Normal    Created     Created container
  41s   11s   3 kubelet, minikube spec.containers{helloworld} Normal    Killing     Killing container with id docker://helloworld:Container failed liveness probe.. Container will be killed and recreated.
  57s   10s   4 kubelet, minikube spec.containers{helloworld} Normal    Started     Started container
MacbookHome:04_02 Application health checks karthik$
```

And finally, when we check the deployment, we notice that deployment is not available as well.
```
MacbookHome:04_02 Application health checks karthik$ kubectl get deployments
NAME                                            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment-with-bad-liveness-probe   1         1         1            0           3m
```

To summarize, we've learned that we can use readiness and liveness probes to check the status of our pods and use them to restart pods when necessary and check pod health.
