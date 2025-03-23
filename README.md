# 📌 3-Tier To-Do List Application

🚀 A containerized To-Do List application deployed on Kubernetes using a 3-tier architecture: **Frontend (Nginx), Backend (Flask API), and Database (PostgreSQL)**. Managed with **Docker** and **Kubernetes (KIND Cluster)**.

---

## 📜 Architecture Overview

Your system follows a **3-tier architecture**:

1️⃣ **Frontend (Nginx + HTML + JS)**
   - Runs on an Nginx container
   - Serves `index.html`
   - Communicates with the backend via API requests

2️⃣ **Backend (Flask API)**
   - A Flask application inside a Python container
   - Handles CRUD operations
   - Interacts with the PostgreSQL database

3️⃣ **Database (PostgreSQL)**
   - Stores task data
   - Deployed with a Persistent Volume for data persistence

---

## ⚙️ How It Works

📌 **User Workflow**:

1. User interacts with the frontend (`index.html`) via a web browser.
2. JavaScript fetches data from the Flask API (`flask-backend`) using HTTP requests.
3. Flask API processes requests, communicates with PostgreSQL, and responds with JSON data.
4. Tasks are stored in PostgreSQL, allowing users to create, update, or delete them.
5. Kubernetes orchestrates everything, ensuring smooth operation within the KIND cluster.

---

## 📂 Deployment Breakdown

### 🐳 **Docker Images**
✅ **Frontend** → Uses Nginx to serve `index.html`.  
✅ **Backend** → Runs a Flask API inside a Python container.  
✅ **Database** → Uses the official PostgreSQL image.  

### ☸️ **Kubernetes (K8s) Setup**
📌 **Flask Backend Deployment (`flask-deployment.yml`)**  
- Exposes Flask API at port `5000` (NodePort `30000`).

📌 **Frontend Deployment (`frontend-deployment.yml`)**  
- Serves `index.html` and interacts with Flask API.  
- Exposed at port `80` (NodePort `30001`).

📌 **PostgreSQL Deployment (`postgresql-deployment.yml`)**  
- Uses Persistent Volume (`PV/PVC`) for data storage.  
- Communicates with Flask API using service name `postgres`.

📌 **KIND Cluster Configuration (`config.yml`)**  
- Defines control-plane & worker nodes.  
- Maps required ports (`30000`, `30001`).

---

### 🛠️ Setup & Deployment
1️⃣ **Clone the Repository**
```sh
git clone https://github.com/SanketNalage/Kubernetes-Hand-on/tree/main/todo-list-main.git
cd todo-app
```

---

## 🚀 Getting Started

### 🔧 Prerequisites
Ensure you have the following installed:
- [Docker](https://www.docker.com/)
- [Kubernetes (kubectl)](https://kubernetes.io/docs/tasks/tools/)
- [KIND (Kubernetes in Docker)](https://kind.sigs.k8s.io/)

---

# 🚀 Deploying a To-Do List App on KIND Cluster

---

## 📌 1️⃣ **Login to Docker**
```sh
docker login
```

---

## 🔧 2️⃣ **Build and Push Backend Image**
```sh
docker build -t backend .
docker images
docker tag backend:latest sanketnalage/backend:latest
docker push sanketnalage/backend
```

---

## 🎨 3️⃣ **Build and Push Frontend Image**
```sh
docker build -t frontend .
docker tag frontend:latest sanketnalage/frontend:latest
docker push sanketnalage/frontend
```

---

## 📦 4️⃣ **Create the KIND Cluster**
```sh
kind create cluster --config=config.yml
```

## 📦 5️⃣ **Load Docker Images into KIND Cluster**
```sh
kind load docker-image sanketnalage/backend:latest --name todo-cluster
kind load docker-image sanketnalage/frontend:latest --name todo-cluster
```

---

## 🔍 6️⃣ **Verify Loaded Images in KIND**
### 🖥️ Access the Node's Shell
```sh
docker exec -it todo-cluster-control-plane bash
```
### 📜 List Loaded Images
```sh
crictl images
```
**✅ Expected Output:**
```
docker.io/sanketnalage/backend                  latest               0c77f39c943db       401MB
docker.io/sanketnalage/frontend                 latest               bf4828882f2de       49.3MB
```

---

## 🚀 7️⃣ **Apply Kubernetes Manifests**
```sh
kubectl apply -f kubernetes/postgres-deployment.yml
kubectl apply -f flask-deployment.yml
kubectl apply -f frontend-deployment.yml
```

---

---
## Enter in the postgersql database and create the table run the command single order.
```sh
kubectl exec -it postgres-0 -- bash
psql -U youruser -d todolist
```
```sh
CREATE TABLE tasks (
    id SERIAL PRIMARY KEY,
    description TEXT NOT NULL
);
```

## 🚀 8️⃣ **Check Running Services**

```sh
kubectl get pods
kubectl get svc
```
## 🚀 9️⃣ **Do the port-forwarding to run the app**
```sh
sudo -E kubectl port-forward svc/flask-backend 30000:5000
```
---

🎉 **Congratulations! Your To-Do List app is now deployed on your KIND cluster!** 🎉
