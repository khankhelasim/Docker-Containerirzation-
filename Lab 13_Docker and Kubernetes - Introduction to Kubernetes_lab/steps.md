
---

## **2️⃣ steps.md**
This will contain **exactly the full strict step-by-step lab** I gave you earlier.  
You just paste the whole lab commands from my previous message into `steps.md` so anyone can follow them exactly.

Example:
```md
# Kubernetes Minikube Lab Steps

## Task 1: Install and Configure Kubernetes Cluster using Minikube
### Subtask 1.1: Install Minikube
Step 1: ...
###################################################

Task 1: Install and Configure Kubernetes Cluster using Minikube

Subtask 1.1: Install Minikube

Step 1: Update your system packages

sudo apt update && sudo apt upgrade -y


Step 2: Install required dependencies

sudo apt install -y curl wget apt-transport-https


Step 3: Download and install minikube

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube


Step 4: Verify minikube installation

minikube version

Subtask 1.2: Install kubectl

Step 1: Download kubectl

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"


Step 2: Make kubectl executable and move to PATH

chmod +x kubectl

sudo mv kubectl /usr/local/bin/


Step 3: Verify kubectl installation

kubectl version --client

Subtask 1.3: Start Minikube Cluster

Step 1: Start minikube with Docker driver

minikube start --driver=docker


Step 2: Verify cluster status

minikube status


Step 3: Check cluster information

kubectl cluster-info


Step 4: View cluster nodes

kubectl get nodes

Task 2: Deploy a Simple Multi-Container Application using Kubernetes Pods

Subtask 2.1: Create a Simple Web Application Pod

Step 1: Create directory for manifests

mkdir ~/k8s-lab && cd ~/k8s-lab


Step 2: Create nginx pod manifest

Step 3: Deploy the pod

kubectl apply -f nginx-pod.yaml


Step 4: Verify pod creation

kubectl get pods


Step 5: Get detailed pod information

kubectl describe pod nginx-pod

Subtask 2.2: Create a Multi-Container Pod

Step 1: Create pod with nginx + redis

Step 2: Deploy the pod

kubectl apply -f multi-container-pod.yaml


Step 3: Check both pods running

kubectl get pods


Step 4: View logs from specific container

kubectl logs multi-container-pod -c nginx

kubectl logs multi-container-pod -c redis

Subtask 2.3: Create a Deployment

Step 1: Create nginx deployment manifest

Step 2: Deploy the application

kubectl apply -f nginx-deployment.yaml


Step 3: Verify deployment

kubectl get deployments

kubectl get pods -l app=nginx

Task 3: Expose a Service and Access it Externally
Subtask 3.1: ClusterIP Service

Step 1: Create service manifest

Step 2: Apply service

kubectl apply -f nginx-service-clusterip.yaml


Step 3: View service

kubectl get services

Subtask 3.2: NodePort Service

Step 1: Create NodePort manifest

Step 2: Apply NodePort service

kubectl apply -f nginx-service-nodeport.yaml


Step 3: Get service details

kubectl get services

Subtask 3.3: Access Application Externally

Step 1: Get minikube IP

minikube ip


Step 2: Access via NodePort

curl http://$(minikube ip):30080


Step 3: Use minikube service command

minikube service nginx-service-nodeport --url


Step 4: Open in browser (if GUI)

minikube service nginx-service-nodeport

Subtask 3.4: LoadBalancer Service

Step 1: Create LoadBalancer manifest

Step 2: Apply LoadBalancer service

kubectl apply -f nginx-service-loadbalancer.yaml


Step 3: Start minikube tunnel (new terminal)

minikube tunnel


Step 4: Check external IP

kubectl get services nginx-service-loadbalancer

Step 5: Access via LoadBalancer IP

curl http://<EXTERNAL-IP>

Task 4: Understand Kubernetes Workload Management

Subtask 4.1: Explore Pod Lifecycle

Step 1: Watch pods

kubectl get pods -w

Step 2: Delete a pod

kubectl delete pod <pod-name>

kubectl get pods

Step 3: Scale deployment

kubectl scale deployment nginx-deployment --replicas=5

kubectl get pods

Step 4: Scale down

kubectl scale deployment nginx-deployment --replicas=2

kubectl get pods

Subtask 4.2: Resource Management

Step 1: Check resource usage

kubectl top nodes

kubectl top pods

Step 2: Describe deployment

kubectl describe deployment nginx-deployment

Step 3: View events

kubectl get events --sort-by=.metadata.creationTimestamp

Subtask 4.3: Rolling Updates

Step 1: Update nginx image

kubectl set image deployment/nginx-deployment nginx=nginx:1.21

Step 2: Watch rolling update

kubectl rollout status deployment/nginx-deployment

Step 3: Check rollout history

kubectl rollout history deployment/nginx-deployment

Step 4: Rollback

kubectl rollout undo deployment/nginx-deployment

Task 5: Use kubectl to Interact with Kubernetes

Subtask 5.1: Basic kubectl Commands

Step 1: List all resources

kubectl get all

Step 2: Get detailed info

kubectl describe deployment nginx-deployment

kubectl describe service nginx-service-nodeport

Step 3: View in YAML

kubectl get deployment nginx-deployment -o yaml

kubectl get service nginx-service-nodeport -o yaml

Step 4: Get resources with labels

kubectl get pods -l app=nginx

kubectl get all -l app=nginx

Subtask 5.2: Advanced Operations

Step 1: Execute in pod

kubectl exec -it nginx-pod -- /bin/bash

# Inside:

# nginx -v

# exit

Step 2: Port forwarding

kubectl port-forward pod/nginx-pod 8080:80

# In another terminal:

curl http://localhost:8080

Step 3: Copy files

echo "Hello Kubernetes" > test.txt

kubectl cp test.txt nginx-pod:/tmp/test.txt

kubectl exec nginx-pod -- cat /tmp/test.txt

Step 4: View logs

kubectl logs nginx-pod

kubectl logs -f nginx-pod

kubectl logs --tail=10 nginx-pod

Subtask 5.3: Namespaces

Step 1: Create namespace

kubectl create namespace test-namespace

Step 2: List namespaces

kubectl get namespaces

Step 3: Deploy in namespace

kubectl apply -f nginx-pod.yaml -n test-namespace

Step 4: List resources in namespace

kubectl get pods -n test-namespace

kubectl get all -n test-namespace

Step 5: Set default namespace

kubectl config set-context --current --namespace=test-namespace

kubectl get pods

Step 6: Switch back to default

kubectl config set-context --current --namespace=default

Subtask 5.4: Resource Management and Cleanup

Step 1: View quotas/limits

kubectl describe node minikube

Step 2: Delete specific resources

kubectl delete pod nginx-pod

kubectl delete service nginx-service-clusterip

Step 3: Delete by label

kubectl delete pods -l app=nginx

Step 4: Delete from files

kubectl delete -f nginx-deployment.yaml

kubectl delete -f nginx-service-nodeport.yaml

kubectl delete -f nginx-service-loadbalancer.yaml

Step 5: Cleanup namespace

kubectl delete namespace test-namespace
