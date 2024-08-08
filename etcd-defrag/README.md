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

The following table lists the configurable parameters of the etcd-defrag chart and their default values:

| Parameter                      | Description                                        | Default                   |
|--------------------------------|----------------------------------------------------|---------------------------|
| `replicaCount`                 | Number of etcd replicas                            | `1`                       |
| `image.repository`             | Image repository                                   | `bitnami/etcd`            |
| `image.tag`                    | Image tag                                          | `3.5.15-debian-12-r6`     |
| `auth.rbac.create`             | Enable RBAC authentication                         | `true`                    |
| `auth.rbac.rootPassword`       | Password for the root user                         | `""`                      |
| `persistence.enabled`          | Enable persistence using Persistent Volume Claims  | `true`                    |
| `persistence.size`             | Size of the PVC                                    | `8Gi`                     |
| `defrag.schedule`              | Cron schedule for defragmentation job              | `"0 3 * * *"`             |

You can specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example:

```bash
helm install my-etcd ./ --namespace default --set replicaCount=3
```

Alternatively, use a YAML file to specify the values for the parameters:

```bash
helm install my-etcd ./ --namespace default -f values.yaml
```

### Example `values.yaml`

```yaml
replicaCount: 3

image:
  repository: bitnami/etcd
  tag: 3.5.15-debian-12-r6

auth:
  rbac:
    create: true
    rootPassword: your-root-password

persistence:
  enabled: true
  size: 8Gi

defrag:
  schedule: "0 3 * * *"
```

### Explanation

- **`replicaCount`:** Sets the number of etcd replicas to 3.
- **`image.repository` and `image.tag`:** Specify the Docker image and version to use.
- **`auth.rbac`:** Configures role-based access control (RBAC), with an example for setting the root user password.
- **`persistence`:** Enables persistence with a PVC of size 8Gi.
- **`defrag.schedule`:** Cron schedule for the defragmentation job, set to run daily at 3 AM.

This example provides a basic configuration template for deploying the etcd chart with custom settings. You can expand upon it or modify it to fit your specific needs. Let me know if you need anything else!

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

2. **Test the Defragmentation CronJob:**

Manually trigger the defragmentation job:
```bash
kubectl create job --from=cronjob/etcd-defrag etcd-defrag-test -n <namespace>
```

Check the logs to ensure the defragmentation completed successfully:
```bash
kubectl logs -l job-name=etcd-defrag-test -n <namespace>
```

The output should confirm the defragmentation of the etcd database.

2. **Clean Up:**
```bash
kubectl delete job etcd-defrag-test -n <namespace>
```

Remove the client pod if it's no longer needed:
```bash
kubectl delete pod my-etcd-client -n <namespace>
```

Replace <namespace> with the appropriate namespace where your etcd cluster is deployed.

