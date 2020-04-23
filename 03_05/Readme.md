# Breaking down the helloworld app

## Chapter Goals
1. Understand the basics of the deployment and service
2. Understand what we created
3. Create a simple single yaml file for deployment and service
4. Run the yaml file in minikube
5. Verify that the application is working as expected

### Understand the basics of the deployment and service

Running `kubectl get all` shows us pods, services and deployments that are running that build the helloworld application. Let's peel this onion and figure out what's going on.

To take a look at the deployment YAML that runs the application, run `kubectl get deploy/hw -o yaml`. This will return the YAML that composes the helloworld service.

When working with Kubernetes, you should get familiar with these YAML files.

The Kubernetes service also comprises YAMLs. Let's take a look at that by running `kubectl get service helloworld-service -o yaml`.

### Create our deployment using YAML

If we were going to recreate our deployment and service as YAMLs, they would look like the following:

Deployment:
```
apiVersion: apps/v1
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

Service:
```
apiVersion: v1
kind: Service
metadata:
  name: helloworld-service
spec:
  # if your cluster supports it, uncomment the following to automatically create
  # an external load-balanced IP for the frontend service.
  type: LoadBalancer
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: helloworld
```


### Execute our deployment using YAML

To create these, we can run the command `kubectl create -f helloworld-deployment.yml` to create our deployment and `kubectl create -f helloworld-service.yml` to create the service. This will take the contents of the YAML file and create the necessary components in the file.

```
karthik$ kubectl create -f helloworld-deployment.yml
deployment "helloworld-deployment" created
karthik$ kubectl create -f helloworld-service.yml
service "helloworld-service" created
karthik$

```

Typically, in the real world, you would mostly not use seperate files to break up your application and would have it in a single file that encompasses the entire application with both the deployment and the service component. An example of this YAML file is shown here:

```
apiVersion: apps/v1
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
---
apiVersion: v1
kind: Service
metadata:
  name: helloworld-service
spec:
  # if your cluster supports it, uncomment the following to automatically create
  # an external load-balanced IP for the frontend service.
  type: LoadBalancer
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: helloworld
```

Notice the `---` that marks the end of one section and starts another.

### Verify that the application is working as expected
Finally, to see this new helloworld working as expected, we will run the minikube command to expose the service in the browser with the following command:

```
karthik$ minikube service helloworld-service
Opening kubernetes service default/helloworld-service in default browser...
karthik$
```


