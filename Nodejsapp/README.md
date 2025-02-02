## Installing Argocd CLI

### Step1: Install the binary.

```
curl -sSL -o argocd-linux-amd64 "https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64"
chmod +x argocd-linux-amd64
sudo mv argocd-linux-amd64 /usr/local/bin/argocd
```

### Verify the installation:

```
argocd version
```

-> Since ArgoCD server is exposed as a NodePort service for HTTPS (443). This means you can access the ArgoCD UI and API using your Minikube Node IP and NodePort.

### Step2: Get the Minikube Node IP

```
minikube ip
```

Note: If you're running this on an AWS Ubuntu instance, replace minikube ip with curl ifconfig.me to get your instance's public IP.

### Step3: Find the ArgoCD admin password.

```
kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### Step2: Login to ArgoCD through CLI.

```
argocd login 192.168.49.2:30101 \  # <minikubeip>:<nodeport>
  --username admin \
  --password <PASTE_PASSWORD> \
  --insecure
```
 
## Node.js App with ArgoCD Deployment

### Step1: Build & Push Docker Image

```
docker login -u swapnil0078
docker build -t swapnil0078/nodejs-app:latest .
docker push swapnil0078/nodejs-app:latest
```

### Step2: Apply Kubernetes Manifests

```
kubectl apply -f k8s/
```


### Step3: Deploy with ArgoCD

```
argocd app create nodejs-app \
  --repo https://github.com/Swapnilbarsagade/MiniKube-Cluster.git \
  --path Nodejsapp/k8s \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default
```

### Step4: Sync & Access App

```
argocd app sync nodejs-app
kubectl port-forward svc/nodejs-service 8080:80
```

