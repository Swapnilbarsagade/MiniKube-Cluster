## Minikube on Ubuntu 24.04 LTS

### Prerequisites

1) Instance type - t2.medium
2) 2 GB RAM or more
3) CPU / vCPU or more
4) 20 GB free hard disk space or more
5) Docker / Virtual Machine Manager – KVM & VirtualBox

### Step 1: Launch the Instance and access through SSH.

### Step 2: Install Minikube 

1) Update the system packages on Ubuntu 24.04 LTS

```
sudo apt update -y
```
2)  Install  dependencies.

```
sudo apt install curl wget apt-transport-https -y
```
3) Install the docker.

```
sudo apt install docker.io
```

4) Configure to Run docker without sudo permission

```
sudo usermod -aG docker $USER
sudo chmod 666 /var/run/docker.sock
```

5) To check whether virtualization support is enabled on your machine or not

```
egrep -q 'vmx|svm' /proc/cpuinfo && echo yes || echo no
```

6) Install the KVM and and other tools on Ubuntu 24.04 LTS

```
sudo apt install qemu-kvm libvirt-clients libvirt-daemon-system bridge-utils virtinst libvirt-daemon
```

7) Download and install latest minikube binary setup

```
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

8) Check minikube version on Ubuntu.

```
minikube version
```

### Step 3: Install Kubectl for MiniKube

1) Download kubectl binary setup on minikube.(see document for latest version)

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

2) Set the executable permissions on kubectl binary.

```
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```
3) check kubectl version.

```
kubectl version --client --output=yaml
```

### Step 4: Start the MiniKube Cluster

1) Start the minikube

```
minikube start

```
2) Start the minikube with the docker driver and run. (skip if first step is used)

```
minikube start --vm-driver docker
```

3) Check the status of Minikube

```
minikube status
```

4) Check Cluster Status, node status and cluster info.

```
kubectl cluster-info
kubectl get nodes
```

5)  to list of addons for minikube

```
minikube addons list
```

6) Deploy a Simple App

```
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --type=NodePort --port=80
minikube service nginx --url
```
7) Port-Forward the Nginx Service

-> for local access  

```
kubectl port-forward svc/nginx 8080:80
```
You can access it at: http://localhost:8080

-> for external access
```
kubectl port-forward --address 0.0.0.0 svc/nginx 8080:80
```
Access it from a browser or API client on another machine using: http://instance-public-ip:8080
