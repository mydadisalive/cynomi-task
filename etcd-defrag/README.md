# etcd-defrag Helm Chart

## Overview

This Helm chart deploys an etcd cluster with a CronJob for periodic defragmentation. Defragmentation is crucial for maintaining the health and performance of etcd nodes by compacting the database and reclaiming storage space.

## Prerequisites

- Kubernetes 1.12+
- Helm 3.0+

## Installation

To install the chart with the release name `my-etcd`, navigate to the directory containing your Helm chart and run:

```bash
helm install my-etcd ./ --namespace default
```

This command deploys etcd on the Kubernetes cluster using the default configuration.

## Configuration

You can configure the schedule and other parameters by modifying the values.yaml file. Below are the available configuration options:

| Parameter                      | Description                                        | Default                   |
|--------------------------------|----------------------------------------------------|---------------------------|
| `defrag.schedule`              | Cron schedule for defragmentation job              | `"0 3 * * *"`             |


To modify these values, you can either update the values.yaml file or use --set flag with the helm install command.
Example:

```bash
helm install my-etcd-defrag . --set defrag.schedule="0 2 * * *"
```


Alternatively, use a YAML file to specify the values for the parameters:

```bash
helm install my-etcd ./ --namespace default -f values.yaml
```

### Example `values.yaml`

```yaml
defrag:
  schedule: "0 3 * * *"
```

### Testing

After deploying the Helm chart, you can verify the installation and test the defragmentation job as follows:

1. **Verify the Installation:**

   Ensure that all the etcd pods are running:

   ```bash
   kubectl get pods -n <namespace>
   ```
   You should see output similar to:
   NAME                                READY   STATUS    RESTARTS   AGE
  my-etcd-0                           1/1     Running   0          2m
  my-etcd-1                           1/1     Running   0          2m
  my-etcd-2                           1/1     Running   0          2m
  my-etcd-client                      1/1     Running   0          2m

2. **Access the etcd Client:**
Create a pod to use as an etcd client:
```bash
Create a pod to use as an etcd client:
```

Connect to the client pod:
```bash
kubectl exec --namespace <namespace> -it my-etcd-client -- bash
```

Once inside the pod, set and get a key to verify the etcd operation:
```bash
etcdctl --user root:$ROOT_PASSWORD put /message "Hello, etcd!"
etcdctl --user root:$ROOT_PASSWORD get /message
```

You should see:
```bash
/message
Hello, etcd!
```

3. **Test the Defragmentation CronJob:**

Manually trigger the defragmentation job:
```bash
kubectl create job --from=cronjob/etcd-defrag etcd-defrag-test -n <namespace>
```

Check the logs to ensure the defragmentation completed successfully:
```bash
kubectl logs -l job-name=etcd-defrag-test -n <namespace>
```

The output should confirm the defragmentation of the etcd database.

4. **Clean Up:**
```bash
kubectl delete job etcd-defrag-test -n <namespace>
```

Remove the client pod if it's no longer needed:
```bash
kubectl delete pod my-etcd-client -n <namespace>
```

Replace <namespace> with the appropriate namespace where your etcd cluster is deployed.

