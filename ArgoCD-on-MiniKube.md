## ArgoCD on Minikube

### Step1:  Install Argo CD

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Step2: verify the installation by getting all the objects in the ArgoCD namespace.

```
kubectl get all -n argocd
```

### Step3:  Expose the ArgoCD API Server

Since Minikube doesn’t support LoadBalancer services by default, expose ArgoCD using NodePort:

```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
```

Get the NodePort:

```
kubectl get svc argocd-server -n argocd
```

### Step4: Retrieve the ArgoCD Admin Password

```
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
```

### Step5: Access ArgoCD UI on Browser

By default, the ArgoCD server is not exposed outside the cluster. You can expose it using port-forwarding to access the ArgoCD UI.

```
kubectl port-forward svc/argocd-server -n argocd --address 0.0.0.0 8080:443
```

You can access it at: http://<your-instance-public-ip>:8080


## Troubleshooting

### if port 8080 is already occupied by another process on your system. Here’s how to fix it:

1) Find the Process Using Port 8080

```
sudo netstat -tulnp | grep 8080
```
or
```
sudo lsof -i :8080
```
2) Kill the Process Using Port 8080

```
sudo kill -9 <PID>
```

3) Retry Port-Forwarding

```
kubectl port-forward svc/argocd-server -n argocd --address 0.0.0.0 8080:443
```