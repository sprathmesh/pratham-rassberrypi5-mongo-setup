# pratham-rassberrypi5-mongo-setup
pratham-rassberrypi5-mongo-setup
---
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
              mountPath: /data/db  # ✅ MongoDB will write to /data/db inside the container
      volumes:
        - name: mongo-storage
          persistentVolumeClaim:
            claimName: mongo-pvc

---
#apiVersion: v1
#kind: Service
#metadata:
#  name: mongo-service
#spec:
#  selector:
#    app: mongo
#  ports:
#    - protocol: TCP
#      port: 27017
#      targetPort: 27017
#      nodePort: 32017
#  type: NodePort
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
--
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mongo-pv
spec:
  capacity:
    storage: 5Gi  # Ensure it matches PVC
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual  # Explicitly set storage class
  hostPath:
    path: "/data"  # ✅ Store MongoDB data directly in /data

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
      storage: 5Gi  # ✅ Matches PV size
  storageClassName: manual  # Match PV's storage class
  volumeName: mongo-pv  # Ensure PVC binds to the correct PV

---
