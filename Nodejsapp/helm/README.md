## 🚀 **Deploy a Node.js App on ArgoCD Using Helm Chart**

---

## **1️⃣ Prerequisites**
✅ **Kubernetes Cluster (EKS, GKE, AKS, Minikube, etc.)**  
✅ **ArgoCD installed on the cluster** (`kubectl get pods -n argocd`)  
✅ **Helm installed** (`helm version`)  
✅ **A Helm Chart for the Node.js app**  

---

## **2️⃣ Install ArgoCD (If Not Installed)**
If you haven’t installed ArgoCD yet, install it using:

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

✅ Check the pods:
```sh
kubectl get pods -n argocd
```

✅ Expose the ArgoCD UI:
```sh
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Access UI: **https://localhost:8080**  
Default Login:  
- Username: `admin`
- Password: `kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode`

---

## **3️⃣ Create a Helm Chart for the Node.js App**
### **📌 Structure of Helm Chart**
```
nodejs-app/
│── Chart.yaml
│── values.yaml
│── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│── charts/
│── README.md
```

### **📌 `Chart.yaml`**
```yaml
apiVersion: v2
name: nodejs-app
description: A Helm chart for deploying Node.js application
type: application
version: 1.0.0
appVersion: "1.0.0"
```

### **📌 `values.yaml`**
```yaml
replicaCount: 2

image:
  repository: your-dockerhub-username/nodejs-app
  tag: latest
  pullPolicy: Always

service:
  type: LoadBalancer
  port: 80
  targetPort: 3000

ingress:
  enabled: false
```

### **📌 `deployment.yaml`**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nodejs-app
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: nodejs-app
  template:
    metadata:
      labels:
        app: nodejs-app
    spec:
      containers:
        - name: nodejs-app
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: 3000
```

### **📌 `service.yaml`**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodejs-app
spec:
  selector:
    app: nodejs-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: LoadBalancer
```

---

## **4️⃣ Push the Helm Chart to a Git Repository**
ArgoCD uses **GitOps**, so we must push the Helm chart to a **GitHub repository**.

```sh
git init
git add .
git commit -m "Initial commit for Node.js Helm Chart"
git remote add origin https://github.com/your-username/nodejs-app-helm.git
git push -u origin main
```

---

## **5️⃣ Create an ArgoCD Application for the Node.js App**
Apply this **ArgoCD Application manifest** to deploy the Helm chart.

### **📌 `nodejs-argocd.yaml`**
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nodejs-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/your-username/nodejs-app-helm.git  # Replace with your repo
    targetRevision: main
    path: nodejs-app  # Path to Helm chart in Git repo
    helm:
      valueFiles:
        - values.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: nodejs-app
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

Apply this to ArgoCD:
```sh
kubectl apply -f nodejs-argocd.yaml -n argocd
```

---

## **6️⃣ Verify the Deployment**
✅ Check the **ArgoCD UI**  
- Open **https://localhost:8080**  
- Log in and check the status of the **Node.js app**  

✅ Check the **Pods in Kubernetes**
```sh
kubectl get pods -n nodejs-app
```

✅ Check the **LoadBalancer Service**
```sh
kubectl get svc -n nodejs-app
```

---

## **🎯 Conclusion**
You have successfully deployed a **Node.js application** on **EKS using Helm and ArgoCD**. 🚀  
