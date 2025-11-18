# Kubernetes Commands and YAML Scripts

## 5. Kubernetes PODS| Deploy your First APP


Now we need to create a POD.(google K8s pod and create pod yaml file).

vi pod.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

```sh
kubectl apply -f pod.yaml
```

Equivalent command for above yaml file in docker is 

- `docker run -d nginix:1.14.2 --name nginx -p 80:80 # -d running in background.`

```sh
kubectl create -f pod.yml
kubectl gets pods
kubectl gets pods -o wide
minikube ssh #(In real time we will ssh to master or worker node) then use curl
curl 'IP_Address of POD'

kubectl logs <pod_name>
kubectl describe pod <pod_name>
```

## Installing KIND  - Gourav J. Shah School of Devops


## Install Kubectl on windows

curl.exe -LO "https://dl.k8s.io/release/v1.30.0/bin/windows/amd64/kubectl.exe"

Get-Module -Name YourModuleName -ListAvailable

On powershell with administrator 

```sh
cd C:\DevOps\kind

Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1')) # chacolatey install cmd.


cd C:\DevOps\kubectl
choco install kubernetes-cli




```

```sh
docker version # this should show both client and server version.
kubectl version
docker ps

git clone https://github.com/nsaishiva18/k8s-code.git  # Github code to install 3 node k8s cluster in kind.

cd k8s-code/helper/kind
ls
cat kind-three-node-cluster.yaml

kind create cluster --config kind-three-node-cluster.yaml

kubectl cluster-info --context kind-kind
kubectl config current-context
kubectl config use-context kind-three-node-cluster # This will switch to particular cluster.
kubectl get nodes


kubectl get nodes
kubectl get pods -A

# Setup Visualiser 
git clone https://github.com/nsaishiva18/kube-ops-view

ls -ltr kube-ops-view/deploy
kubectl apply -f kube-ops-view/deploy

kubectl get pods, nodes
kubectl get all #  localhost:32000/#scale=3.0 


```

```sh
kind create cluster --config .\kind-three-node-cluster.yaml # installed kind-three-node-cluster.
kubectl cluster-info --context kind-kind


```