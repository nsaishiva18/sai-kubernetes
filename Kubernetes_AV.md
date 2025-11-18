# 1. Kubernetes Introduction 

Microservies 
Kubernetes is an Open source COE(Container Orchestration Engine) Platform.

Basics of container 

1. What container is bringing in to the picture
2. how is it different from a virtual machine
3. What is your networking isolation.
4. what is your namespace isolation
5. Why containers are lightweight in nature
6. How do we secure your containers - Distroless images.
7. Multi-stage docker builds

## Q. What is the difference between Docker and Kubernetes ?

A. `Docker` is a container platform - Docker is making your container journey very easy because it provides a complete lifecycle. Containers are *Ephemeral* in nature means shortlife, they can die and revive anytime.

`Kubernetes` is a container orchestration platform.

Problems with Containers

1. `Single Host Nature`- If container-1 is using more momery then container-50 or 100 may not create or it directly die.
2. `Auto Healing` - (With out manual intervention container should start itself) is not available in containerization. If container got killed then application is not accessable, then somebody has to start or act on that container.
3. `Auto Scaling` - (Automatically container count gets increased) - As soon as load gets increase manually we will increase the container count from 10 to 20 or 100 as per the requests. Load Balancer is not available in container.
4. `Enterprise Level Support` -  Docker is Minimalistic or Simple platform - By default docker doesn't support any enterprise level applications support.  Load Balancer, Firewall, Auto-Scale, Auto-Healing, API Gateway, etc

**Note:-** By default K8s is a cluster(Group of nodes). It will be installed in Master/Node architecture. Can also be installed in one node(Developing environment). Every thing in kubernetes will be yaml files.

 1. `Single Host Nature Issue` - If container-1 is having issue K8s can put pods/apps in different node.
 2. `Auto Healing Issue` - K8s Controls and fix the damages.
 when ever container goes down(Due to multiple reasons), K8s will start new container(API Server will take care of this) even before container goes down. 
 3. `Auto Scaling Issue`- Replication Controller(Old Name)- Replica Set(New name).
 HPA(Horizontal Pod Auto-Scaling) --- If load is increasing for one container this will spin-up one/more containers.
 4. `Enterprise Level Support Issue` - K8s is one of the part of `BORG`.

# 2. Kubernetes Architecture Using Examples

**POD**: A Kubernetes pod is a collection of one or more containers. It's the smallest unit of a Kubernetes application.  

- A `POD` is wrapper over the container which has some advanced capabilities.
- With out container runtime, container will never run. Container Runtime component in docker is Dockershim.

## Control plane(Master node)

 1. `Api server` - Core component of k8s, accepts all incoming reqs, exposes k8s to external world. Responsible for handling APIs.
 2. `Etcd`- key value store(K8s Object Store) which stores all the resources of the K8s are stored as objects, cluster related infos. 
 3. `Scheduler` - Scheduling pods or resources on k8s, receives info from api server & acts on it.
 4. `Controller Manager` - Ensures controllers like replica set are running. NODE Controller, Replication, Replicaset, Deployment, Endpoint, Daemonsets, Job Controller.
 5. `Cloud Controller Manager` - like terraform.

## Data plane(Worker node)  -- Basically responsible for running applications.

1. `kubelet` - Responsible for creating pod, ensures pod is always running.
2. `kube proxy` - Provides networking like Docker0, default load balancing. Provides Cluster IP addresses. It used IP tables for networking related configuration. 
3. `container runtime` - Runs container inside pod. K8s supports Dockershim, ContainerD, CRI-O.

# 3. Install Kubernetes on LAPTOP | Minikube.

.  Install Minikube
.  Install Kubectl

minikube.sigs.k8s.io

# 4. How to manage Hundreds of Kubernetes Clusters | KOPS | K8s Production Systems

Kubernetes is an Open source COE(Container Orchestration Engine) Platform.

- Order of the distributions

1. Kubernetes
2. Openshift
3. Rancher
4. Tanzo
5. EKS - Elastic Kubernetes Service
6. AKS - Azure Kubernetes Service
7. GKE - Google Kubernetes Engine
8. DKE - Docker Kubernetes Engine

`KOPS` - Kubernetes Operations.
Lifecycle of K8s - install -- Upgrade -- Modifications -- Deletions.

## Kubernetes Installation Using KOPS on EC2

### Create an EC2 instance or use your personal laptop.

Dependencies required 

1. Python3
2. AWS CLI
3. kubectl

###  Install dependencies

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

```
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
```

```
sudo apt-get update
sudo apt-get install -y python3-pip apt-transport-https kubectl
```

```
pip3 install awscli --upgrade
```

```
export PATH="$PATH:/home/ubuntu/.local/bin/"
```

### Install KOPS (our hero for today)

```
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

chmod +x kops-linux-amd64

sudo mv kops-linux-amd64 /usr/local/bin/kops
```

### Provide the below permissions to your IAM user. If you are using the admin user, the below permissions are available by default

1. AmazonEC2FullAccess
2. AmazonS3FullAccess
3. IAMFullAccess
4. AmazonVPCFullAccess

### Set up AWS CLI configuration on your EC2 Instance or Laptop.

Run `aws configure`

## Kubernetes Cluster Installation 

Please follow the steps carefully and read each command before executing.

### Create S3 bucket for storing the KOPS objects.

```
aws s3api create-bucket --bucket kops-abhi-storage --region us-east-1
```

### Create the cluster 

```
kops create cluster --name=demok8scluster.k8s.local --state=s3://kops-S3Bucket-storage --zones=us-east-1a --node-count=1 --node-size=t2.micro --master-size=t2.micro  --master-volume-size=8 --node-volume-size=8
```

### Important: Edit the configuration as there are multiple resources created which won't fall into the free tier.

```
kops edit cluster myfirstcluster.k8s.local
```

Step 12: Build the cluster

```
kops update cluster demok8scluster.k8s.local --yes --state=s3://kops-S3Bucket-storage
```

This will take a few minutes to create............

After a few mins, run the below command to verify the cluster installation.

```
kops validate cluster demok8scluster.k8s.local
```
The below command will create a hosted zone.
```
aws route53 create-hosted-zone --name dev.example.com --caller-reference 1
```


# 5. Kubernetes PODS| Deploy your First APP

`Cluster` `Scaling` `Healing` `Enterprise level support`

POD - Lowest level of deployment.

Q. Why we should deploy your container as a POD why can't we deploy as a container in K8s?

A. Pod is defined as a definition of how to run a container.
We will pass all the arguments for running a container in docker like -d, -p, -v, --network etc..., but in k8s will pass all the arguments in pod.yaml file.
POD can be a single container or multiple containers.

In K8s everything will be written in yaml files.

If we put 2 containers in one Pod - It will allowed shared networking, shared storage.

`Kubectl` - It's a command line tool for K8s. We can directly interact with K8s clusters.

## Install kubectl 

**Practicle**

google kubectl installation - follow steps accordingly as per OS.

$ Kubrctl version

minikube, k3s, Kind, microk8s.
Kind is kubernetes in docker.

##Install minikube

google minikube installation - follow steps accordingly as per OS.
$ minikube

## After kubectl and minikube installation we need to install cluster.

```sh
$ minikube start  # it will create a single VM --> Single node K8s cluster will get created.

$ minikube start --memory=4096 --driver=hyperkit

$ kubectl get nodes # Will have one node which is both Data plane and Control Plane.

```

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

Equivalent command for above yaml file in docker is 

`docker run -d nginix:1.14.2 --name nginx -p 80:80`
```sh
$ kubectl create -f pod.yml
$ kubectl gets pods
$ kubectl gets pods -o wide
$ minikube ssh (In real time we will ssh to master or worker node)
then
$ curl 'IP_Address of POD'

```

Q. How do we remember all the commands?

A. google kubectl cheat sheet for help in commands.

kubectl delete pod 'Pod_Name'

Q. How will we Auto-Scale and Auto-Heal?

A. On top of a pod we have a wrapper called $deployment$.
We have to use Deployment in K8s to get these features like Auto-Scaling and Auto-Healing.

In Real time we will not deploy pods but will actually deploy our deployments or stateful sets or daemon sets.

Q. How to verify the application?
A. kubectl logs 'POD_Name'.

Q. How do you debug a pod?

A. kubectl describe pod 'POD_Name'. or
 Kubectl logs 'POD_Name'.

# 6.  Kubernetes Deployment | REPLICASETS

Q1. What is the difference between Container vs POD vs Deployment ?

A. POD is somewhere similar to the container and it can offer some advantages like pod contains single or multiple containers(In POD containers can share networking and Storage). while in container we will provide all the arguments in a single command whereas in POD we will write a YAML file.

But POD doesn't provide Auto-Healing or Auto-Scaling features.
Deployment can provide Zero downtime deployments, Auto-Healing and Auto-Scaling features.

K8s suggest do not create a POD directly, create POD using deployment resource.
Deployment Resource will create a **Replica Set(K8s Controller)**, this will roll our PODs.

Q. Why Replica Set is required?

A. In some cases we always do not want a single replica of our container, sometimes load will be high and some times load will be less. High Availability or Load Balancing.

Some times we expose our apps to multiple concurrent users who can access in a way that 100 requests should to POD-1 and 100 should go to POD-2 

In Deployment YAML manifest we will metion pods count as 2, then Deployment will make sure always 2 PODS are available(even 1 POD goes down will create or replace the same).

Replica Set Controller will implement Auto-Healing.

Q. What is the difference between $Deployment$ and $Replicaset$ ?

A. $Replicaset$ : It is basically a Kubernetes Controller which is implementing Auto Healing feature of PODs.

$Deployment$: When you create a Deployment a replicaset is created automatically which is responsible for tracking this controller behavior in Kubernetes.

*Practical*
```
$ Kubectl get pods
$ kubectl get deploy
$ Kubectl delete deploy 'Deployment_Name' -- This will delete deployments and PODs aswell.
$ Kubectl get all

```
Q. How do you list out all the resources that are available in a particular namespace and in all namespaces all application in a cluster ?

A. $ *Kubectl get all* and *Kubectl get all -A*

```
$ Kubectl apply -f pod.yml  --- Pod will get created in K8s Cluster
$ Kubectl get pods --- This will list all the pods created.
$ Kubectl get pods -o wide  OR kubectl describe pod 'POD_Name' --- This will give all the info of PODS.
$ minikube ssh 
$ kubectl delete pod nginx --- by this we won't be able to access application. So we need to create deployment.

$ vim deployment.yml
$ kubectl apply -f deployment.yml --- This will create a deployment which will also creates POD or PODS as per replicaset available in deployment manifest.
$ Kubectl get deploy --- will get deployment details
$ kubectl get rs --- Will give Replicaset details.

$ kubectl get pods -w  --- We can watch the PODS.
$ kubectl delete pod 'nginx' --- will delete the pod.
```

**Deploy** ---> **Replicaset** ---> **POD**

# Everything about Kubernetes Services | DISCOVERY| LOAD BALANCING | NETWORKING.

For Each Deployment most of the times we will create a service in the world of Kubenetes.

Q. Why do we need SERVICE in Kubernetes?

A. SVC is offering(Advantages) 


1. `Load Balancing` - Instead of accessing IP Addresses it will access Service(Labels - payment.default.svc).
2. `Service Discovery` - Labels and Selectors.
3. `Expose to world` - Service will expose application.

Service will be created in 3 Mode types:
1. `Cluster IP` Mode (Discover and LB)
2. `Nodeport`Mode (Inside Organization can access - who ever has access to that node can access) and 
3. `Load Balancer`Mode (External world).


--> If No Service - We will deploy POD as the Deployment-- which will create a Replicaset - Which will create a POD(either single or multiple based on Replicaset).

Bcuz of Auto Healing if POD goes down, automatically deployment will create one more POD(application will be up) based on Replicaset count. But the IP address will get change for the POD which started on behalf of the POD went down. 

## Kubernetes Interview Questions.

1Q. What is the difference between Docker and Kubernetes ?

A. Docker is a container platform where as Kubernetes is a container orchestration environment that offers capabilities like Auto healing, Auto Scaling, Clustering and Enterprise level support like Load Balancing.

2Q .What is the main components of Kubernetes architecture ?

A. 1.  Control Plane (API Server, SCHEDULER, Controller Manager, C-CM, ETCD).
2. Data Plane (Kubectl, Kube-proxy, Container Run time).


3Q .What are the main differences between Docker Swarm and Kubernetes ?
A. Kubernetes is better suited for large organizations as it offers more scalability, networking capabilities like policies and huge third party ecosystem support.

4Q . What is the difference between Docker container and a Kubernetes pod ?
A. A pod in Kubernetes is a runtime specification of a container in docker. A pod provides more declarative way of defining using YAML and you can run more than one container in a POD.

5Q. What is namespace in Kubernetes ?

A.In K8s namespace is a logical isolation od resources, network policies, rbac and everything. For example, there are two projects using same K8s cluster, One project can use ns1 and other project can use ns2 without overlap and authentication problems. 

6Q. What is the role of Kube Proxy ?

A. Kube-proxy works by maintaining a set of network rules on each node in the cluster, which are updated dynamically as services are added or removed. When a client sends a request to a service, the request is intercepted by Kube-proxy then looks up the destination endpoint for the service and routes the request accordingly.

Kube-proxy is an essential component of a Kubernetes cluster, as it ensures that services can communicate with each other.

7Q. What are the different types of services within Kubernetes?

A. There are three different types of services that a use can create.

1. Cluster IP Mode
2. Node Port Mode
3. Load Balancer Mode.

8Q. What is the difference between NodePort and LoadBalancer type service ?

A. When a service is created a NodePort type, The Kube-proxy updates the IPTables with Node IP address and port that is chosen in the service configuration to access the pods.

Where as if you create a Service as a type Load Balancer, the cloud control manager creates a external load balancer IP using the underlying cloud provider logic in the C-CM. Users can access services using the external IP.

9Q. What is the role of Kubelet ?

A. Kubelet manages the containers that are scheduled to run on that node. It ensures that the containers are running and healthy, and that the resources they need are available.

Kubelet communicates with the Kubernetes API server to get the information about the containers that should be running on the node, and then starts and stops the containers as needed to maintain the desired state. it also monitors the containers to ensure that they are running correctly, and restarts them if necessary.

Q10. Day to Day activities on Kubernetes ?

A. 

# Kubernetes Services | Learn Traffic flow using KUBESHARK

*Practicle*
```
$ minikube status
$ kubectl get all 
$ kubectl delete deploy "Deployment_Name"
$ kubectl delete svc "DVC_Name"
$ kubectl get all  - Now we should see only K8s default service running.

$ cd Docker-Zero-to-Hero (Take from github)
$ cd python-web-app
$ vim Dockerfile
$ docker build -t abhishekf5/python-sample-app-demo:v1 .
$ vim deployment.yaml  --- file contains RC, labels & Selectors and image name, port.
$ Kubectl apply -f deployment.yml  --- Deployment is created for application.
$ kubectl get deploy -- Kubectl will talk to K8s API Server and gets the information of deploy.
$ kubectl get pods
$ kubectl get pods -O wide --- this will give us IP details of PODS.
$ kubectl get pods -v=9 (Max verbosity level - this will give more info about the api call)
$ kubectl delete pod "Pod_name"
$  kubectl get pods -O wide --- Now we will get the same replica set pods with different IP address.
$ minikube ssh

$ curl -L http://IP_Address:8000/demo  --- This can only be accessed in side minikube --- If we come out of k8s cluster we can not able to access the application. Bcuz a POD by default will have "Cluster Network" attached to it.

$ Vim service.yml
google kubernetes service take an example and edit as per requirement. Change the target port(Port on which our application is running) as per the application.

$ kubectl apply -f service.yml
$ kubectl get svc

$ minikube ssh
$ curl -L http://IP_Address:80/demo

come out of ssh.

$ minikube ip
$ curl -L http://minikube_IP_Address:30007
```
`How to Expose your application`
For `LOAD Balancer` - Kubeshark
$ kubectl edit svc "SVC_Name"
Search(/ in vim mode) for type: and change (/NodePort) to LoadBalancer --- This will not work here as we are using minikube.

$ kubectl get svc

`Service DISCOVERY`

**Note :** If there is a mismatch in Labels & Selectors, then service will not be discoverable.


# Kubernetes Service, Ingress with TLS and Ingress Controllers with Live coding.

Kubernetes Service -- An abstract way to expose an application running on a set od Pods as a network service.

### Load Balancer Type Service

Q. Is Load Balancer service type only restricted to Cloud providers ?

A. Bare Metal LB Implementation - https://github.com/metallb/metallb

Q. If Load Balancer Service type can do the thing for you, why use an Ingress resource?

Q. What is Kubernetes Ingress? 

A. `Ingress` - exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource. 

`Ingress Controller` - In order for the Ingress resource to work, the cluster muct have an ingress controller running.

```
$ Kubectl get ing

HostBased - $ curl IP_Address -H 'host:foo.bar.com'

PathBases - $ curl IP_Address/second -H 'host:foo.bar.com'
```
- H = Header

### TLS - SSL Passthrough - SSL Offloading - SSL Bridging.

# Kubernetes INGRESS

Enterprise Load balancer supports - Ratio Based, Sticky Session, Path Based, Domain Based, Host Based, White Listing, Black Listing.

**Problem-1** Enterprise & TLS Load Balencing.(Above types not supported by K8s earlier).

**Problem-2** If service is type Load Balencer then for each service Kubernetes, cloud provider will charge for each and every IP Address. 

**NOTE :** Ingress is of no use with out Ingress Controller.
Ingress Controller - It is a Load Balancer that you can choose from the requirement( Nginix, F5, HA-Proxy, ...).

Q. What is the difference between Load balancer type service and the traditional Kubernetes Ingress ?

A. The Load Balancing type service is good but it was missing all the above capabilities and Cloud provider will charge for each and every load balancer service type(Static Public Addresses).

- Architecture  - User will create Ingress resource(Along with that user need to deploy Ingress Controller(Load Balancer or LB + API Gateway)) - Load Balancing companies will write their own Ingress Controllers. 

*Practicle*

```
$ Kubectl get pods
$ kubectl get svc
$ minikube ip
$ cur -L http://ip:Port/demo -v - No output
```
Host based Load Balancing.

Google Kubernetes Ingress 
Created ingress.yaml and tried to access app - no output as Ingress Controller is not installed.

Google Kubernetes Nginix Ingress Controller Install.

$ Minikube addons enable ingress

$ cur -L http://ip:Port/demo -v - we will get output as ingress Controller is installed --- Ingress Controller will identify Ingress installed.

In real time we need to update /etc/hosts with domain and IP.
192.168.64.11 foo.bar.com


# Introduction to Kubernetes RBAC
RBAC  - Role Based Access Control - is related to security.

RBAC divided in to 2 types. 1. User Management and 2. Service Accounts(Managing the access of the services that are running on the cluster).

1. How you will manage access to the users in your k8s cluster
2. How you will deal with service accounts.

Service Accounts / Users  
Roles / Cluster Role  
Role Binding / CRB(Cluster Role Binding)

# Kubernetes Custom Resources| Custom Controller

1. CRD -Custom Resource Definition.(Define a new type of API to K8s and also to Validate)
2. CR -Custom Resource, 
3. Custom Controller.

- When ever you want to introduce new Resource or extend the API service or Capabilities to K8s then we will use above.

Deployment, Service, POD, Config Map, Secrets -OOTB in K8s.

- https://github.com/istio/istio
- https://github.com/Kubernetes/sample-controller 

CNCF - Cloud NAtive Computing Foundation.

# ConfigMaps & Secrets

`Config-Map` is solving the problem of Storing the information, which can be used by the application later point of time.

`Secrets` also solves the same problem as ConfigMap but secrets deal with sensitive data. Here the data will be encrypted.

When ever a resource is created in K8s, resource related information is stored in "etcd"(in the form of Objects). Any one or hacker who able to access etcd, they can retrieve sensitive information.

**Note :** Container will never allow to change the environment variable - For this we need to recreate the container. 
In production we can not restart the containers it might get in to traffic loss.

echo MzMwNg== | base64 --decode

# Kubernetes Monitoring Using PROMETHEUS.


1) # Kubernetes Troubleshooting | ImagePullBackOff - How to use Private Images in Kubernetes

```
kubectl get pods -w  ---> This will keep watching the get pods command.
```


3) 

```
kubectl config view | grep kind
kubectl config use-context kind-multi

```

# Kubernetes in Docker(KIND)

- Single Node K8s cluster --> Kind will crete single docker conatiner and installs k8s cluster.
- Multi Node K8s cluster --> 1 master and 2 workers --> Kind will create 3 docker containers. In 1 it installs k8s control plane and in rest it will install Data Plane. --> Here Kind take the responsibility to Data planes to Control plane.

- Kind is light weight.

```sh
kind version

kind create cluster --name=single-node-cluster # will create single node cluster
kind get cluster 
kubectl config current-context 

kubectl create deployment nginx --image nginx
kubectl get pods -w

```

Q. How to creat multi-node-cluster in KIND ?

Ans. vi multi-node-k8s-cluster.yaml

```sh
apiVersion: kind.xk8s.io/v1alpha4
kind: Cluster

nodes:
- role: control-plane
- role: control-plane
- role: worker
- role: worker
- role: worker
- role: worker

```


```sh

kind create cluster --name 6-node-cluster --config=multi-node-k8s-cluster.yaml # This will create a 6 node k8s cluster.

kubectl config current-context
kubectl get nodes
kind get clusters

docker ps | grep -i single-node-cluster
docker ps | grep -i 6-node-cluster

kubectl create deployment nginx --image nginx
kubectl get pods -w
kubectl port-forward pod/<pod_name> 8888:80  # With this we can expose the interface.

```

We can use KIND in - upgrades, developing own clusters.
