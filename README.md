# Kubernetes — Automatically Cleaning Up Evicted Pods (Workaround)

*Note: This is a workaround method, not an actual method to remove evicted pods.*

## Pod eviction can happen for plenty of reasons. 

A few examples are:

- VerticalPodAutoscaler scaling a Pod
- When a Node is under resource pressure (CPU, memory, etc.)
- When a higher priority Pod preempts a lower priority Pod
- when a Pod’s emptyDir usage exceeds the configured size limit

For some reason, the evicted pod will not be removed automatically.  In this article, we are introducing a workaround to remove the evicted pods automatically using cronjobs.


## Prerequisites

- Kubernetes Cluster admin rights.
- helm

We have developed a helm template for deploying the cronjob for cleanup evicted pods.

## The chart will deploy the following resource on your Kubernetes Cluster. 

- Namespace
- Service account 
- Cluster Role
- Cluster Rolebinding 
- Cronjob

## Deployment: 

```
git clone https://github.com/sahirabbas/remove_evicted_pods_workaround.git
```

### Deploy the cronjob using helm.

```
cd remove_evicted_pods_workaround
helm install <name>  .
```

By default, the chart will create a namespace evicted-pods-cleanup and will deploy the cronjob on the namespace and the cronjob will run every 30 mins. 

Follow the below table for overridable values. 

Value | Value for | Default value |
--- | --- | --- |
namespace.name | Namespace Name | evicted-pods-cleanup |
|serviceaccount.name | ServiceAccount Name | podevict-sa |
|clusterrole.name | ClusterRole Name| romove-evicted |
| clusterrolebinding.name | ClusterRoleBinding Name | romove-evicted |
| cronjob.name | Cronjob Name | cleanup-evicted-pods |
| cronjob.activedeadlineseconds | activeDeadlineSeconds for the Cronjob | 1500 |
| cronjob.schedule | Schedule for Cronjob | */30 * * * * (every 30 mins) |
| cronjob.startingdeadlineseconds | startingDeadlineSeconds for the Cronjob | 180 |
| failedjobshistorylimit | failedJobsHistoryLimit to keep | 1 |
| successfuljobshistorylimit | successfulJobsHistoryLimit to keep | 1 |


If you want to override any of the values. For example, if you want to override the cron timing, follow the below command. 

```
helm install remove-evic --set cronjob.schedule='*/20 * * * *' .
```
