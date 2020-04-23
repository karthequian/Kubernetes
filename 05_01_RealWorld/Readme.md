# Running a more real world example

In this chapter, we'll take the popular Kubernetes guestbook, and attempt to run it! You can read more about the guestbook here: [https://kubernetes.io/docs/tutorials/stateless-application/guestbook/]

Run the guestbook by executing `kubectl create -f guestbook.yaml`

You'll see something like this:

```
$ kubectl create -f guestbook.yaml
deployment.apps/redis-master created
service/redis-master created
deployment.apps/redis-slave created
service/redis-slave created
deployment.apps/frontend created
service/frontend created
```

Load up the guestbook by running the command `minikube service frontend`. The output would look like this:
```
$ minikube service frontend
|-----------|----------|-------------|---------------------------|
| NAMESPACE |   NAME   | TARGET PORT |            URL            |
|-----------|----------|-------------|---------------------------|
| default   | frontend |          80 | http://192.168.64.2:31824 |
|-----------|----------|-------------|---------------------------|
🎉  Opening service default/frontend in default browser...
```