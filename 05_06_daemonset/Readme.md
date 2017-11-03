Daemonsets: A DaemonSet ensures that all Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. Examples of a daemon set would be running your logging or monitoring agent on your nodes.

In this example, I will just run a simple busybox image as a daemonset.



Statefulsets Manages the deployment and scaling of a set of Pods, and provides guarantees about the ordering and uniqueness of these Pods. Unlike a Deployment, a StatefulSet maintains a sticky identity for each of their Pods.