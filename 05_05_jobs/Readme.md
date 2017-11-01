# Jobs in Kubernetes

## Chapter Goals
1. How to run jobs
2. How to run cron jobs

### How to run jobs
Jobs are a construct that run a pod once, and then stop. However, unlike pods in deployments, the output of the job is kept around until you decide to remove it.

Running a job is similar to running a deployment, and we can create this by `kubectl create -f simplejob.yaml`

To see the output of the job: `kubectl get jobs`

You can find the pod that ran by doing a `kubectl get pods --all-pods`, and then get the logs from it as well.

### How to run cron jobs
Cron jobs are like jobs, but they run periodically.

Start your cron by running `kubectl create -f cronjob.yaml`

We can use the cronjob api to view your cronjobs: `kubectl get cronjobs`. It adds the last schedule date
