# How to Migrate Cluster using Velero

## Introduction

### How Velero Works

Velero consists of two parts:

- A `server` that runs on your cluster.
- A `command-line` client that runs locally.

### Backup and Restore Workflow

## Prerequisites

- Config accesible between 2 cluster (image, ...)
- StorageClass between 2 cluster.

## Step 1 - Create S3 bucket to store

Create a file `credentials-velero` with content:

```bash
[default]
aws_access_key_id=<AWS_ACCESS_KEY_ID>
aws_secret_access_key=<AWS_SECRET_ACCESS_KEY>
```

## Step 2 - Installing Velero CLI

```bash
curl -OL https://github.com/vmware-tanzu/velero/releases/download/v1.13.2/velero-v1.13.2-linux-amd64.tar.gz
tar -xvf velero-v1.13.2-linux-amd64.tar.gz
cp velero-v1.13.2-linux-amd64/velero /usr/local/bin
```

## Step 2 - Install velero server in cluster

Note that KUBECONFIG env is point to the cluster

```bash
velero install \
    --provider aws \
    --plugins velero/velero-plugin-for-aws:v1.9.0 \
    --bucket annd2-velero2 \
    --backup-location-config region=ap-southeast-1 \
    --secret-file ./credentials-velero \
    --use-node-agent \
    --use-volume-snapshots=false
```

## Step - Add back up annotation for colume is used in pods

Check the the pod use the PV and then add this annotation to include PV as file system in backup.

```bash
kubectl -n annd2 annotate pod/web-0 backup.velero.io/backup-volumes-excludes=disk-sss
```

## Step - Create backup

```bash
velero create backup annd2 --include-namespaces annd2 --csi-snapshot-timeout=20m
```

## Step - Create restore from backup

```bash
velero restore create --from-backup annd2
```

## Step - Understand

Get log to debug:

```bash
velero backup logs annd2 -v=5
```
