# pratham-rassberrypi5-mongo-setup
pratham-rassberrypi5-mongo-setup

-------------------------------------------------------------

# MongoDB 8.0 Installation Guide for Raspberry Pi 5

This guide provides step-by-step instructions for installing **MongoDB 8.0** on a **Raspberry Pi 5** running Ubuntu.

## Prerequisites
- Raspberry Pi 5 (ARM64 architecture)
- Ubuntu 22.04 (Jammy Jellyfish) or later
- Internet connection

---

## Step 1: Add the MongoDB Repository
MongoDB packages are not available in the default Ubuntu repository, so we need to add the official MongoDB repository:

```bash
echo 'deb [arch=arm64] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/8.0 multiverse' | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list
```

This command adds the MongoDB repository for version 8.0 to your system.

---

## Step 2: Import the MongoDB GPG Key
To verify package authenticity, import MongoDB's official GPG key:

```bash
sudo wget -O /etc/apt/trusted.gpg.d/mongodb-repo.asc https://www.mongodb.org/static/pgp/server-8.0.asc
```

This ensures that all MongoDB packages come from a trusted source.

---

## Step 3: Update the Package Database
Refresh your package list to include the newly added MongoDB repository:

```bash
sudo apt update
```

This updates the system to recognize the latest MongoDB packages.

---

## Step 4: Install MongoDB
Now, install MongoDB 8.0 using the package manager:

```bash
sudo apt install -y mongodb-org
```

The `-y` flag automatically confirms the installation.

---

## Step 5: Start and Enable MongoDB Service
Once installed, start the MongoDB service and enable it to run on startup:

```bash
sudo systemctl start mongod
sudo systemctl enable mongod
```

This ensures that MongoDB starts automatically whenever the system reboots.

---

## Step 6: Verify MongoDB Installation
To check if MongoDB was installed successfully, run:

```bash
mongod --version
```

To check if MongoDB is running properly:

```bash
sudo systemctl status mongod
```

---

## Step 7: Connect to MongoDB
To access the MongoDB shell and verify that it's running, execute:

```bash
mongosh
```

If `mongosh` is not installed, you can install it separately using:

```bash
sudo apt install -y mongodb-mongosh
```

---

## Step 8: Allow Remote Connections (Optional)
If you want to allow remote connections to MongoDB, edit the MongoDB configuration file:

```bash
sudo nano /etc/mongod.conf
```

Find the line that starts with `bindIp`, and change it from:

```
bindIp: 127.0.0.1
```

to:

```
bindIp: 0.0.0.0
```

Save the file (`CTRL + X`, then `Y`, then `ENTER`), and restart MongoDB:

```bash
sudo systemctl restart mongod
```

---

## Step 9: Uninstall MongoDB (If Needed)
If you ever need to remove MongoDB, use the following commands:

```bash
sudo systemctl stop mongod
sudo apt purge -y mongodb-org*
sudo rm -r /var/log/mongodb
sudo rm -r /var/lib/mongodb
```

This will completely remove MongoDB and its associated data.

---

## Additional Resources
- [Official MongoDB Documentation](https://www.mongodb.com/docs/manual/release-notes/8.0/)
- [MongoDB Download Page](https://www.mongodb.com/try/download/community)

---

### ✅ You have successfully installed MongoDB 8.0 on your Raspberry Pi 5! 🚀


# OR you Directly apply this YAML file
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongo-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi

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
              value: "admin"
            - name: MONGO_INITDB_ROOT_PASSWORD
              value: "password"
          volumeMounts:
            - name: mongo-storage
              mountPath: /data/db
          livenessProbe:
            exec:
              command:
                - mongosh
                - --eval
                - "db.adminCommand('ping')"
            initialDelaySeconds: 30
            periodSeconds: 10
          readinessProbe:
            exec:
              command:
                - mongosh
                - --eval
                - "db.adminCommand('ping')"
            initialDelaySeconds: 10
            periodSeconds: 5
      volumes:
        - name: mongo-storage
          persistentVolumeClaim:
            claimName: mongo-pvc

---
apiVersion: v1
kind: Service
metadata:
  name: mongo-service
spec:
  selector:
    app: mongo
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
  type: ClusterIP
```
# OR IF you want to use NodePort Insted of ClusterIP use this service yaml

```
Updated Service YAML (Using NodePort)
yaml
Copy
Edit
```
