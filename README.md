# Druid Setup and Installation
- **Install ZooKeeper**:

```bash
kubectl apply -f zookeeper.yaml
```
- **Install MySQL or PostgreSQL**:
  - Create a configMap using mysql-init-script file:
  
    ```bash
    kubectl create configmap -n demo my-init-script \
    --from-file=mysql-init-script.sql    
    ```
  - Create MySQL:
    ```bash
    kubectl apply -f mysql.yaml
    ```
- **Create a S3 Bucket**:
  - Install MinIO and S3 Bucket
  
  ```bash
  helm upgrade --install --namespace "minio-operator" --create-namespace "minio-operator" minio/operator --set operator.replicaCount=1
  ```
  ```bash
  helm upgrade --install --namespace "minio-operator" --create-namespace "minio-operator" minio/operator \
  --set operator.replicaCount=1 \
  --set operator.env[0].name=MINIO_OPERATOR_TLS_ENABLE \
  --set operator.env[0].value="off" \
  --set operator.env[1].name=MINIO_CONSOLE_TLS_ENABLE \
  --set operator.env[1].value="off"
  ```
  ```bash
  helm upgrade --install --namespace "demo" --create-namespace druid-minio minio/tenant \
  --set tenant.pools[0].servers=1 \
  --set tenant.pools[0].volumesPerServer=1 \
  --set tenant.pools[0].size=1Gi \
  --set tenant.certificate.requestAutoCert=false \
  --set tenant.buckets[0].name="druid" \
  --set tenant.pools[0].name="default"
  ```
  - Create deep-storage-config secret:
  
  ```bash
  kubectl apply -f deep-storage-config.yaml
  ```
- **Install Druid**:

```bash
  kubectl apply -f druid.yaml
```