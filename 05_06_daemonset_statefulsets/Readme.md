# Daemonsets and Statefulsets

## Chapter Goals
1. How to run daemonsets
2. How to run statefulsets

## Daemonsets

Daemonsets: A DaemonSet ensures that all Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. Examples of a daemon set would be running your logging or monitoring agent on your nodes.

First, you'll want to make sure you tag minikube with a label of `kubectl label node/minikube infra=development` to label the node first.

In the example, I will just run a simple busybox image as a daemonset, and then run daemonset examples to show how you can tag things to run on specific nodes
`kubectl create -f daemonset.yaml` will run on the nodes

`kubectl create -f daemonset-infra-development.yaml`  will only run on nodes labeled `infra=development` (as shown above in the label)

`kubectl create -f daemonset-infra-prod.yaml`  will only run on nodes labeled `infra=production`


## Statefulsets

Statefulsets Manages the deployment and scaling of a set of Pods, and provides guarantees about the ordering and uniqueness of these Pods. Unlike a Deployment, a StatefulSet maintains a sticky identity for each of their Pods.
