# pratham-rassberrypi5-mongo-setup

This repository contains the Kubernetes deployment configuration for setting up MongoDB on a Raspberry Pi 5.

## Deployment Configuration

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongo
          image: mongo:8.0
          ports:
            - containerPort: 27017
          env:
            - name: MONGO_INITDB_ROOT_USERNAME
              value: "pivotchain"
            - name: MONGO_INITDB_ROOT_PASSWORD
              value: "devops"
          volumeMounts:
            - name: mongo-storage
              mountPath: /data/db
      volumes:
        - name: mongo-storage
          persistentVolumeClaim:
            claimName: mongo-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: mongo
spec:
  selector:
    app: mongo
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
  type: ClusterIP
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mongo-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: "/data"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongo-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: manual
  volumeName: mongo-pv
```  

## Usage
1. Apply the configuration to your Kubernetes cluster:
   ```sh
   kubectl apply -f mongo-deployment.yaml
   ```
2. Verify the deployment:
   ```sh
   kubectl get pods
   ```
3. Check the service:
   ```sh
   kubectl get svc mongo
   ```

