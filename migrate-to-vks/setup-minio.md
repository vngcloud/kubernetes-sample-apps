# Set up MINIO

Setup minio to store backup inside cluster.

```bash
kubectl create ns velero
kubectl apply -f 00-minio-deployment.yaml
```
